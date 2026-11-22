# User Stories

## Sources

- `aidlc/spaces/default/intents/261122-minimal-rest-api/inception/requirements-analysis/requirements.md` defines FR1-FR14 and NFR1-NFR8.
- `aidlc/spaces/default/intents/261122-minimal-rest-api/inception/practices-discovery/team-practices.md` requires a health walking skeleton, independent payments and notifications Units, TDD, direct integration to `main`, and stack-standard quality checks.
- `aidlc/spaces/default/intents/261122-minimal-rest-api/inception/user-stories/user-stories-questions.md` confirms three vertical Must Have stories and the selected personas.

## Story Map

| Order | Story | Persona | Capability | Priority | Intended Unit |
|---|---|---|---|---|---|
| 1 | US1.1 | Service Operator | Start and check the service | Must Have | Health walking skeleton |
| 2 | US2.1 | API Consumer | Submit a synthetic payment | Must Have | Payments |
| 3 | US3.1 | API Consumer | Submit a synthetic notification | Must Have | Notifications |

Payments and notifications both depend on the service foundation established by US1.1. They do not depend on each other.

## US1.1 - Start and check the API

**Story:** As a Service Operator, I want to start the API with one documented command and check its readiness, so that I know the service foundation is available before feature traffic begins.

**Priority:** Must Have  
**Dependencies:** None  
**Requirement coverage:** FR1, FR2, FR11, FR12, NFR1, NFR2, NFR5, NFR6, NFR7, NFR8

### Acceptance Criteria

#### AC1.1.1 - Ready response

Given the FastAPI application has started and is ready to serve traffic  
When Riley calls `GET /health`  
Then the response is HTTP 200 with JSON content type  
And the body is exactly `{"status":"ok"}`.

#### AC1.1.2 - Documented startup

Given Python 3.12 and the documented dependencies are installed  
When Riley runs the documented Uvicorn start command  
Then the application starts without import or configuration errors  
And `GET /health` becomes reachable on the loopback interface.

#### AC1.1.3 - Startup dependency failure

Given a required Python dependency is unavailable  
When Riley attempts to start the application  
Then startup exits unsuccessfully  
And the diagnostic output identifies the missing dependency.

#### AC1.1.4 - Route and method errors

Given the application is running  
When Riley requests an unknown route or uses an unsupported method on a known route  
Then the service returns HTTP 404 or 405 respectively  
And the response is JSON without a Python traceback.

#### AC1.1.5 - Local response target

Given the local automated test environment  
When the health endpoint's happy path is measured from request submission through complete response  
Then it completes in less than 500 ms.

#### AC1.1.6 - Safe diagnostics

Given the service starts, stops, or handles a request  
When logs are captured  
Then they include startup, shutdown, method, path, status, and unexpected-error information as applicable  
And they contain no request body, secret, or client-visible traceback.

#### AC1.1.7 - Test-first and clean-checkout quality

Given Morgan implements the health walking skeleton  
When the Unit is prepared for integration to `main`  
Then each behavior was preceded by a failing automated test  
And the complete local test, formatter, and linter commands exit zero from a clean checkout without external services or credentials.

### INVEST Check

- **Independent:** Establishes the shared service foundation and can be delivered before either feature.
- **Negotiable:** Internal module and command layout remain open to design.
- **Valuable:** Gives operators an observable readiness signal and proves end-to-end HTTP execution.
- **Estimable:** One FastAPI entry point, one endpoint, common error handling, and quality setup.
- **Small:** Fits the requested walking-skeleton Unit.
- **Testable:** AC1.1.1-AC1.1.7 define observable pass/fail outcomes.

## US2.1 - Submit a synthetic payment

**Story:** As an API Consumer, I want to submit a validated synthetic payment and receive a stable accepted result, so that I can exercise a payment contract safely without moving money or configuring a provider.

**Priority:** Must Have  
**Dependencies:** US1.1  
**Requirement coverage:** FR3, FR4, FR5, FR6, FR11, FR13, FR14, NFR1, NFR3, NFR4, NFR5, NFR6, NFR7, NFR8

### Acceptance Criteria

#### AC2.1.1 - Valid payment accepted

Given Casey supplies JSON with a positive decimal `amount`, a three-letter uppercase `currency`, and a non-empty `idempotency_key`  
When Casey calls `POST /payments`  
Then the response is HTTP 202  
And the JSON body contains `status` equal to `accepted` and a non-empty opaque `payment_id`.

#### AC2.1.2 - Missing or invalid payment fields

Given a payment request omits a required field, has a zero or negative amount, has a non-decimal amount, has a malformed currency, or has a blank key  
When Casey calls `POST /payments`  
Then the response is a 4xx JSON validation error identifying the affected field  
And no accepted result is returned.

#### AC2.1.3 - Deterministic payment retry

Given two payment requests have the same idempotency key and equivalent validated content  
When Casey submits both requests, including across a process restart  
Then both responses have the same status, body, and `payment_id`  
And no payment record is retained to produce that result.

#### AC2.1.4 - No real payment effect

Given a valid payment request  
When the accepted response is produced  
Then no provider, database, message broker, or other external service is contacted  
And no money movement or durable write occurs.

#### AC2.1.5 - No retained or logged payment data

Given any payment request completes  
When process state, configured storage, and logs are inspected  
Then no request body, amount, idempotency key, accepted result, or identifier is retained or logged.

#### AC2.1.6 - Payment performance and isolation

Given the local automated test environment and an invalid notification request has occurred  
When Casey submits a valid payment request  
Then the payment response still completes in less than 500 ms  
And it is accepted independently of notification behavior.

#### AC2.1.7 - Payment Unit quality

Given Morgan implements the payment story  
When the Unit is prepared for integration to `main`  
Then each payment behavior was preceded by a failing automated test  
And payment tests, the full suite, formatting, and linting exit zero without external services, persistent state, or ordering dependence on notification tests.

### INVEST Check

- **Independent:** Depends only on the shared health foundation, not on notifications.
- **Negotiable:** Internal validation and identifier algorithms remain open within the approved contract.
- **Valuable:** Gives consumers a safe payment-shaped API contract and retry behavior.
- **Estimable:** One endpoint with bounded validation and deterministic response generation.
- **Small:** Fits one independently owned feature Unit.
- **Testable:** AC2.1.1-AC2.1.7 cover happy path, validation, retry, safety, performance, and quality.

## US3.1 - Submit a synthetic notification

**Story:** As an API Consumer, I want to submit a validated synthetic notification and receive a stable accepted result, so that I can exercise a notification contract safely without sending a real message or configuring a provider.

**Priority:** Must Have  
**Dependencies:** US1.1  
**Requirement coverage:** FR7, FR8, FR9, FR10, FR11, FR13, FR14, NFR1, NFR3, NFR4, NFR5, NFR6, NFR7, NFR8

### Acceptance Criteria

#### AC3.1.1 - Valid notification accepted

Given Casey supplies an allowed `channel` of `email`, `sms`, or `push` and non-empty `recipient`, `message`, and `idempotency_key` values after trimming  
When Casey calls `POST /notifications`  
Then the response is HTTP 202  
And the JSON body contains `status` equal to `accepted` and a non-empty opaque `notification_id`.

#### AC3.1.2 - Missing or invalid notification fields

Given a notification request omits a required field, uses an unsupported channel, or contains a recipient, message, or key that is blank after trimming  
When Casey calls `POST /notifications`  
Then the response is a 4xx JSON validation error identifying the affected field  
And no accepted result is returned.

#### AC3.1.3 - Deterministic notification retry

Given two notification requests have the same idempotency key and equivalent validated content  
When Casey submits both requests, including across a process restart  
Then both responses have the same status, body, and `notification_id`  
And no notification record is retained to produce that result.

#### AC3.1.4 - No real notification effect

Given a valid notification request  
When the accepted response is produced  
Then no email, SMS, push, database, message broker, or other external service is contacted  
And no real message or durable write occurs.

#### AC3.1.5 - No retained or logged notification data

Given any notification request completes  
When process state, configured storage, and logs are inspected  
Then no request body, recipient, message, idempotency key, accepted result, or identifier is retained or logged.

#### AC3.1.6 - Notification performance and isolation

Given the local automated test environment and an invalid payment request has occurred  
When Casey submits a valid notification request  
Then the notification response still completes in less than 500 ms  
And it is accepted independently of payment behavior.

#### AC3.1.7 - Notification Unit quality

Given Morgan implements the notification story  
When the Unit is prepared for integration to `main`  
Then each notification behavior was preceded by a failing automated test  
And notification tests, the full suite, formatting, and linting exit zero without external services, persistent state, or ordering dependence on payment tests.

### INVEST Check

- **Independent:** Depends only on the shared health foundation, not on payments.
- **Negotiable:** Internal validation and identifier algorithms remain open within the approved contract.
- **Valuable:** Gives consumers a safe notification-shaped API contract and retry behavior.
- **Estimable:** One endpoint with bounded validation and deterministic response generation.
- **Small:** Fits one independently owned feature Unit.
- **Testable:** AC3.1.1-AC3.1.7 cover happy path, validation, retry, safety, performance, and quality.

## Cross-Story Rules

- US1.1 is delivered first as the walking skeleton.
- US2.1 and US3.1 may proceed independently after US1.1.
- All implementation follows TDD and passes the selected Python formatter, linter, and test commands before direct integration to `main`.
- Automatic staging deployment and separate production approval remain upstream team practices; the approved requirements did not settle their applicability to this local-only workshop application.
