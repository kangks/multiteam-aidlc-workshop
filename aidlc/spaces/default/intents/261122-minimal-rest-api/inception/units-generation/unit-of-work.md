# Units of Work

## Decomposition Basis

- **Unit Ownership**: team
- **Authoritative source:** `aidlc/spaces/default/intents/261122-minimal-rest-api/inception/requirements-analysis/requirements.md`.
- **Team constraints:** `aidlc/spaces/default/intents/261122-minimal-rest-api/inception/practices-discovery/team-practices.md` requires the health walking skeleton, independent payments and notifications Units, TDD, and direct integration to `main`.
- **Supporting source:** `aidlc/spaces/default/intents/261122-minimal-rest-api/inception/user-stories/stories.md` supplies story IDs for mapping, but User Stories was skipped before approval and is not the authority for Unit boundaries.
- **Limitation:** Domain Design was skipped, so no component catalogue independently validates these boundaries. This decomposition is defended by the approved requirements, the explicit three-Unit request, stateless behavior, and distinct endpoint contracts.

## Unit Summary

| Unit ID | Directory | Name | Kind | Deployment Model | Complexity | Direct Dependencies |
|---|---|---|---|---|---|---|
| U1 | `u1-health-skeleton` | Health Walking Skeleton | `service` | One runnable FastAPI service foundation | S | None |
| U2 | `u2-payments` | Payments Feature | `library` | Embedded router and payment module in the U1 service | S | U1 |
| U3 | `u3-notifications` | Notifications Feature | `library` | Embedded router and notification module in the U1 service | S | U1 |

## U1 - Health Walking Skeleton

### Purpose

Deliver the smallest runnable end-to-end slice: a Python 3.12 FastAPI process started with Uvicorn, reachable on loopback, with `GET /health` returning the approved ready response.

### Responsibilities

- Own the application package and FastAPI app factory.
- Own the documented local Uvicorn start command.
- Expose `GET /health` with HTTP 200 and `{"status":"ok"}` when ready.
- Own shared JSON handling for unknown routes, unsupported methods, validation failures, and unexpected errors.
- Own shared request-safe diagnostics that omit bodies and secrets.
- Own the deterministic synthetic-ID utility used by later feature routers.
- Own dependency, test, formatter, and linter configuration for the project.
- Establish the router-registration interface used by U2 and U3.

### Boundaries

- Does not implement payment or notification request models, validation rules, routes, or tests.
- Does not persist business records.
- Does not call external services.
- Exposes shared application utilities through explicit Python module interfaces; feature modules do not modify shared internals outside those interfaces.

### Delivery and Verification

- Produces a runnable service before either feature Unit exists.
- Passes health, startup, common-error, local-latency, logging-safety, formatting, linting, and clean-checkout tests.
- Follows TDD, with a failing test before each behavior change.

### Requirement Coverage

FR1, FR2, FR11, FR12, NFR1, NFR2, NFR5, NFR6, NFR7, NFR8.

## U2 - Payments Feature

### Purpose

Add a stateless `POST /payments` capability that validates synthetic input and returns a deterministic accepted response without moving money or retaining data.

### Responsibilities

- Own the payment request and response models.
- Own payment amount, currency, and idempotency-key validation.
- Own payment canonicalization and deterministic `payment_id` derivation through the U1 shared utility.
- Expose a FastAPI payment router through the U1 router-registration interface.
- Own payment-specific tests for acceptance, validation errors, deterministic retries, no external effects, no retention, latency, and isolation.

### Boundaries

- Depends on U1 application, error, diagnostics, router-registration, and deterministic-ID interfaces.
- Does not modify or depend on U3 source or tests.
- Does not own the application entry point or shared configuration.
- Does not contact a payment provider, database, broker, or external service.
- Does not retain or log payment request or response data.

### Delivery and Verification

- Builds and tests with U1 plus U2 source, without U3 source.
- Passes the project formatter, linter, payment tests, and existing U1 tests.
- Follows TDD, with a failing test before each behavior change.

### Requirement Coverage

FR3, FR4, FR5, FR6, FR11, FR13, FR14, NFR1, NFR3, NFR4, NFR5, NFR6, NFR7, NFR8.

## U3 - Notifications Feature

### Purpose

Add a stateless `POST /notifications` capability that validates synthetic input and returns a deterministic accepted response without sending a real message or retaining data.

### Responsibilities

- Own the notification request and response models.
- Own channel, recipient, message, and idempotency-key validation.
- Own notification canonicalization and deterministic `notification_id` derivation through the U1 shared utility.
- Expose a FastAPI notification router through the U1 router-registration interface.
- Own notification-specific tests for acceptance, validation errors, deterministic retries, no external effects, no retention, latency, and isolation.

### Boundaries

- Depends on U1 application, error, diagnostics, router-registration, and deterministic-ID interfaces.
- Does not modify or depend on U2 source or tests.
- Does not own the application entry point or shared configuration.
- Does not contact an email, SMS, push, database, broker, or other external service.
- Does not retain or log notification request or response data.

### Delivery and Verification

- Builds and tests with U1 plus U3 source, without U2 source.
- Passes the project formatter, linter, notification tests, and existing U1 tests.
- Follows TDD, with a failing test before each behavior change.

### Requirement Coverage

FR7, FR8, FR9, FR10, FR11, FR13, FR14, NFR1, NFR3, NFR4, NFR5, NFR6, NFR7, NFR8.

## Cross-Unit Constraints

- U2 and U3 share only interfaces and utilities owned by U1.
- U2 and U3 must each build and pass tests with U1 and without the other feature Unit.
- No Unit introduces persistence, credentials, provider calls, or public exposure.
- All Units integrate directly into `main` under team ownership.
- Deployment remains one FastAPI service; the `library` kind identifies embedded feature code, not a separately published package.
