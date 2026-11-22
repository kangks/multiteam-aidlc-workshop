# Unit-to-Story Map

## Mapping Basis

`stories.md` exists and supplies the `USx.y` keys required by this stage. User Stories was skipped before approval, so the approved `requirements.md` remains authoritative; the story mapping is a convenient grouping that must not override requirement scope.

## Story Assignment

| Story ID | Story | Unit ID | Directory | Coverage |
|---|---|---|---|---|
| US1.1 | Start and check the API | U1 | `u1-health-skeleton` | Service startup, health, common errors, loopback exposure, diagnostics, and shared quality setup |
| US2.1 | Submit a synthetic payment | U2 | `u2-payments` | Payment contract, validation, deterministic acceptance, stateless safety, latency, and isolation |
| US3.1 | Submit a synthetic notification | U3 | `u3-notifications` | Notification contract, validation, deterministic acceptance, stateless safety, latency, and isolation |

Every Unit has one primary story. No story spans U2 and U3.

## Requirement Allocation

| Requirement IDs | Unit IDs | Rationale |
|---|---|---|
| FR1, FR2 | U1 | Runnable health foundation |
| FR3, FR4, FR5, FR6 | U2 | Payment contract and behavior |
| FR7, FR8, FR9, FR10 | U3 | Notification contract and behavior |
| FR11, FR12 | U1; U2; U3 | U1 owns common JSON error behavior; each feature verifies it at its route boundary |
| FR13 | U2; U3 | Each feature prevents retention of its own business data |
| FR14 | U2; U3 | Each feature proves independence from the sibling feature |
| NFR1 | U1; U2; U3 | Each endpoint verifies the local 500 ms target |
| NFR2 | U1 | U1 owns startup and loopback binding |
| NFR3 | U2; U3 | Each feature enforces its synthetic-data boundary |
| NFR4 | U2; U3 | Each feature proves no external runtime dependency |
| NFR5, NFR6 | U1; U2; U3 | Every Unit follows TDD and project quality checks |
| NFR7 | U1; U2; U3 | U1 owns diagnostics; all Units verify payloads are not logged |
| NFR8 | U1; U2; U3 | Every Unit's tests run without external services or order dependence |

## Work Within Each Unit

### U1 `u1-health-skeleton`

1. Define failing tests for startup, health, common errors, local latency, and safe diagnostics.
2. Create project packaging, the FastAPI app factory, and the Uvicorn entry point.
3. Implement `GET /health` and shared JSON error behavior.
4. Implement the deterministic-ID and router-registration interfaces needed by feature Units.
5. Pass U1 tests, the clean-checkout suite, formatting, and linting.

### U2 `u2-payments`

1. Define failing tests for payment validation, acceptance, deterministic retries, no retention, no external effects, latency, and independence.
2. Implement payment request and response models.
3. Implement payment canonicalization and deterministic identifier derivation through the U1 utility.
4. Expose and register the payment router through the U1 interface.
5. Pass U1 and U2 tests, formatting, and linting without U3 source.

### U3 `u3-notifications`

1. Define failing tests for notification validation, acceptance, deterministic retries, no retention, no external effects, latency, and independence.
2. Implement notification request and response models.
3. Implement notification canonicalization and deterministic identifier derivation through the U1 utility.
4. Expose and register the notification router through the U1 interface.
5. Pass U1 and U3 tests, formatting, and linting without U2 source.

## Coverage Verification

- US1.1 maps to U1.
- US2.1 maps to U2.
- US3.1 maps to U3.
- Every Unit has at least one story.
- Every approved functional and non-functional requirement is allocated to one or more Units.
