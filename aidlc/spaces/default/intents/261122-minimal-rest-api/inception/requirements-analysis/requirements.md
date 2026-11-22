# Requirements

## Intent Analysis

Build a minimal, runnable REST API that proves an end-to-end service foundation through a health endpoint, then adds independent payments and notifications capabilities. The API is a local workshop application, not a production transaction system. Its purpose is to demonstrate clear contracts, validation, deterministic retries, test-first delivery, and independent team ownership without databases or external providers.

## Sources

- [desc] Initial description: Build a minimal REST API with health, payments, and notifications endpoints, split into a walking-skeleton Unit and two independent feature Units, using team-owned Construction.
- [Q1] Python 3.12, FastAPI, and Uvicorn were selected as the application stack.
- [Q2] Payments validate amount and currency and return a synthetic accepted result.
- [Q3] Notifications validate channel, recipient, and message and return a synthetic accepted result.
- [Q4] The service retains no payment or notification records.
- [Q5] The API is for local or test use, has no authentication, and accepts no real personal or payment-card data.
- [Q6] The health endpoint returns HTTP 200 with JSON status `ok` when ready.
- [Q7] Local responses complete within 500 ms, validation errors use JSON, and retries are idempotent.
- [Q8] Synthetic IDs are derived deterministically from the idempotency key and canonical request content without storage.
- [memory:M1] `aidlc/spaces/default/intents/261122-minimal-rest-api/inception/practices-discovery/team-practices.md` defines direct integration to `main`, plan-selected walking skeletons, TDD ordering, deployment practice, and stack-standard code style.

## Functional Requirements

### Service Foundation

#### FR1 - Health status

The system shall expose `GET /health` and return HTTP 200 with the JSON object `{"status":"ok"}` when the process is ready to serve requests. [desc] [Q6]

**Acceptance criteria**
- Given the application has started, when a client calls `GET /health`, then the response status is 200, the content type is JSON, and the body equals `{"status":"ok"}`.
- Given the application is not ready to serve HTTP traffic, when the readiness check runs, then it must not report a successful ready response.

#### FR2 - Runnable application entry point

The system shall provide a documented command that starts the FastAPI application on Python 3.12 using Uvicorn. [Q1]

**Acceptance criteria**
- Given a supported Python 3.12 environment with project dependencies installed, when the documented start command runs, then the process starts without an import or configuration error and `GET /health` becomes reachable.
- Given a required dependency is unavailable, when startup is attempted, then startup fails with a non-zero exit and a message identifying the missing dependency.

### Payments

#### FR3 - Payment request contract

The system shall expose `POST /payments` and require JSON fields `amount`, `currency`, and `idempotency_key`. [desc] [Q2] [Q7]

**Acceptance criteria**
- Given a JSON request with all required fields, when it is submitted to `POST /payments`, then the request reaches payment validation.
- Given any required field is absent, when the request is submitted, then the system rejects it with a JSON validation error that identifies the field.

#### FR4 - Payment input validation

The system shall accept a positive decimal `amount`, an uppercase three-letter `currency` code, and a non-empty `idempotency_key`; it shall reject zero, negative, non-decimal, malformed-currency, or blank-key values. [Q2] [Q7]

**Acceptance criteria**
- Given `amount` is greater than zero, `currency` is three uppercase ASCII letters, and `idempotency_key` is non-empty, when the payment is submitted, then validation succeeds.
- Given `amount` is zero or negative, when the payment is submitted, then the response is a JSON validation error and no accepted response is returned.
- Given `currency` is not three uppercase ASCII letters or the key is blank, when the payment is submitted, then the response is a JSON validation error identifying the invalid field.

#### FR5 - Synthetic payment acceptance

The system shall return HTTP 202 for a valid payment request with a JSON body containing `status: "accepted"` and an opaque non-empty `payment_id`. It shall not contact a payment provider or move money. [Q2] [Q5]

**Acceptance criteria**
- Given a valid payment request, when it is submitted, then the response status is 202 and the JSON body contains exactly the accepted status and a non-empty payment identifier.
- Given the response is produced, then no external payment-provider call or durable write occurs.

#### FR6 - Stateless payment idempotency

The system shall derive `payment_id` deterministically from the payment idempotency key and canonical validated request content, without retaining a record. [Q4] [Q7] [Q8]

**Acceptance criteria**
- Given two valid payment requests have the same idempotency key and equivalent validated content, when both are submitted, then both responses contain the same `payment_id` and body.
- Given either the key or validated request content differs, when a request is submitted, then its identifier derivation input differs.
- Given the process restarts, when the same key and equivalent content are submitted again, then the same identifier is returned.

### Notifications

#### FR7 - Notification request contract

The system shall expose `POST /notifications` and require JSON fields `channel`, `recipient`, `message`, and `idempotency_key`. [desc] [Q3] [Q7]

**Acceptance criteria**
- Given a JSON request with all required fields, when it is submitted to `POST /notifications`, then the request reaches notification validation.
- Given any required field is absent, when the request is submitted, then the system rejects it with a JSON validation error that identifies the field.

#### FR8 - Notification input validation

The system shall accept `channel` values `email`, `sms`, or `push` and require non-empty `recipient`, `message`, and `idempotency_key` strings after trimming surrounding whitespace. [Q3]

**Acceptance criteria**
- Given an allowed channel and non-empty recipient, message, and key, when the notification is submitted, then validation succeeds.
- Given the channel is unsupported, when the notification is submitted, then the response is a JSON validation error identifying `channel`.
- Given the recipient, message, or key is blank after trimming, when the notification is submitted, then the response is a JSON validation error identifying the blank field.

#### FR9 - Synthetic notification acceptance

The system shall return HTTP 202 for a valid notification request with a JSON body containing `status: "accepted"` and an opaque non-empty `notification_id`. It shall not send a real message. [Q3] [Q5]

**Acceptance criteria**
- Given a valid notification request, when it is submitted, then the response status is 202 and the JSON body contains exactly the accepted status and a non-empty notification identifier.
- Given the response is produced, then no email, SMS, push, or other external provider call occurs.

#### FR10 - Stateless notification idempotency

The system shall derive `notification_id` deterministically from the notification idempotency key and canonical validated request content, without retaining a record. [Q4] [Q7] [Q8]

**Acceptance criteria**
- Given two valid notification requests have the same idempotency key and equivalent validated content, when both are submitted, then both responses contain the same `notification_id` and body.
- Given either the key or validated request content differs, when a request is submitted, then its identifier derivation input differs.
- Given the process restarts, when the same key and equivalent content are submitted again, then the same identifier is returned.

### Common API Behavior

#### FR11 - JSON validation errors

The system shall return JSON errors for malformed JSON, missing fields, wrong field types, and invalid field values without exposing a stack trace. [Q7]

**Acceptance criteria**
- Given a request body is malformed JSON, when it is submitted to a body-accepting endpoint, then the system returns a 4xx response with a JSON error body.
- Given validation fails, then the error identifies the affected field when one is known and contains no Python traceback.

#### FR12 - Unknown routes and unsupported methods

The system shall return a JSON 404 response for unknown routes and a JSON 405 response for unsupported methods on known routes. [Q7]

**Acceptance criteria**
- Given a route does not exist, when it is requested, then the response is HTTP 404 with a JSON body.
- Given a route exists but the HTTP method is unsupported, when it is requested, then the response is HTTP 405 with a JSON body.

#### FR13 - No retained business records

The system shall not retain payment or notification request bodies, accepted results, or identifiers in memory, files, logs, caches, or databases after generating a response. [Q4] [Q5]

**Acceptance criteria**
- Given an accepted request completes, when process state and configured storage are inspected, then no payment or notification business record exists.
- Given a request is handled, then logs contain no request body, recipient, message, idempotency key, or amount value.

#### FR14 - Independent feature behavior

The payments and notifications capabilities shall operate independently after the shared health foundation is available. A failure or invalid request in one capability shall not change the other capability's responses. [desc] [memory:M1]

**Acceptance criteria**
- Given the payments endpoint receives an invalid request, when the notifications endpoint receives a valid request, then the notification request is still accepted.
- Given the notifications endpoint receives an invalid request, when the payments endpoint receives a valid request, then the payment request is still accepted.

## Non-Functional Requirements

#### NFR1 - Local response time

Each endpoint shall complete within 500 ms in the automated local test environment, measured from request submission to receipt of the complete response. [Q7]

**Verification:** Each endpoint's happy-path test records elapsed wall-clock time and fails at 500 ms or more.

#### NFR2 - Local-only exposure

The documented default start command shall bind to loopback only. The application shall not include a public deployment configuration unless a later approved requirement changes this constraint. [Q5]

**Verification:** The start configuration resolves to `127.0.0.1` or an equivalent loopback address, and no public listener or public infrastructure is created.

#### NFR3 - Sensitive-data boundary

The API shall accept only synthetic workshop data and shall reject undeclared fields, including fields intended to carry payment-card details or personal profile data. [Q5]

**Verification:** Requests containing undeclared fields such as card numbers are rejected with a JSON validation error, and tests use synthetic values only.

#### NFR4 - No external dependencies at runtime

Request handling shall not require a database, message broker, payment provider, notification provider, or network call to another service. [Q2] [Q3] [Q4]

**Verification:** Endpoint tests pass with outbound network access disabled and no database or provider configured.

#### NFR5 - Test-first delivery

Development shall follow TDD: add a failing automated test before each behavior change, then implement the smallest change needed to pass it. [memory:M1]

**Verification:** Each Unit includes tests for its requirements, and the implementation and test history or work log demonstrates failing-test-first ordering.

#### NFR6 - Code quality

The codebase shall pass the chosen Python stack's standard formatter and linter with no reported violations before merge. [memory:M1]

**Verification:** The documented formatting check and lint command both exit zero in the local environment and CI.

#### NFR7 - Diagnostic safety

The service shall log startup, shutdown, request method, request path, response status, and unexpected errors without logging request bodies or secrets. [Q5] [Q7]

**Verification:** Captured logs contain the required operational fields, omit request payload values, and contain no stack trace in client responses.

#### NFR8 - Deterministic tests

The automated test suite shall run without external services, persistent state, real credentials, or ordering dependencies between the payments and notifications tests. [desc] [Q4] [memory:M1]

**Verification:** The complete suite passes from a clean checkout in one command and also passes when payment and notification test groups run in either order.

## Constraints

- Python 3.12, FastAPI, and Uvicorn are required. [Q1]
- The application is divided into a health walking-skeleton Unit, a payments feature Unit, and a notifications feature Unit. [desc]
- Payments and notifications remain mutually independent after the health foundation. [desc] [memory:M1]
- Construction is team-owned. [desc] [memory:M1]
- Completed work integrates directly into `main`. [memory:M1]
- No authentication, database, external provider, or real sensitive data is included. [Q2] [Q3] [Q4] [Q5]

## Assumptions

- ISO 4217-style uppercase three-letter currency syntax is sufficient validation because no real payment processing occurs. [Q2]
- Supported notification channels are `email`, `sms`, and `push`; they select only validation behavior and do not trigger delivery. [Q3]
- Synthetic IDs are opaque stable strings; consumers must not infer business meaning from their format. [Q8]
- FastAPI's generated OpenAPI document may exist as framework behavior, but custom documentation is not required for this workshop.

## Out of Scope

- Real payment authorization, capture, settlement, refund, card handling, or provider integration.
- Real email, SMS, push delivery, templates, scheduling, or provider integration.
- Authentication, authorization, user accounts, tenant isolation, and public internet exposure.
- Databases, durable storage, record retrieval, update, deletion, listing, filtering, or pagination.
- Production availability, throughput, autoscaling, disaster recovery, and regulatory certification.
- User interface development.

## Open Questions

None.
