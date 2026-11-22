# Personas

## Sources

- `aidlc/spaces/default/intents/261122-minimal-rest-api/inception/requirements-analysis/requirements.md`
- `aidlc/spaces/default/intents/261122-minimal-rest-api/inception/practices-discovery/team-practices.md`
- `aidlc/spaces/default/intents/261122-minimal-rest-api/inception/user-stories/user-stories-questions.md`

## Primary Personas

### Casey, the API Consumer

- **Role:** Developer or automated client integrating with the workshop API.
- **Goals:** Submit valid synthetic payments and notifications; receive predictable accepted responses; retry safely after a client-side timeout.
- **Pain points:** Ambiguous schemas, inconsistent errors, unstable identifiers, and hidden external dependencies.
- **Technical comfort:** High.
- **Frequency:** Repeated use during local development and automated tests.
- **Priority:** Primary for payments and notifications.
- **Relevant requirements:** FR3-FR14, NFR1, NFR3, NFR4, NFR7, NFR8.

### Riley, the Service Operator

- **Role:** Developer or operator starting and checking the local API process.
- **Goals:** Start the service with one documented command; determine readiness through a stable health response; diagnose failures without exposing request data.
- **Pain points:** Undocumented startup commands, false-positive health checks, non-JSON errors, and logs that leak payloads.
- **Technical comfort:** High.
- **Frequency:** At every local run, test session, and deployment check.
- **Priority:** Primary for the health walking skeleton.
- **Relevant requirements:** FR1, FR2, FR11, FR12, NFR1, NFR2, NFR7.

### Morgan, the Maintainer

- **Role:** Team member responsible for changing, testing, and integrating the code.
- **Goals:** Work test-first; keep the three Units independently testable; run formatting, linting, and tests from a clean checkout; avoid databases, credentials, and providers.
- **Pain points:** Coupled feature code, order-dependent tests, hidden state, inconsistent style, and behavior without requirement coverage.
- **Technical comfort:** High.
- **Frequency:** During every implementation and review cycle.
- **Priority:** Supporting persona for all three stories' quality constraints.
- **Relevant requirements:** FR13, FR14, NFR4, NFR5, NFR6, NFR8.

## Relationships and Priority

- Riley validates the health walking skeleton before Casey relies on the feature endpoints.
- Casey's payment and notification outcomes are independent after the shared service foundation exists.
- Morgan maintains the shared foundation and quality controls but does not introduce a fourth implementation Unit.
- Persona priority follows delivery order: Riley for the walking skeleton, Casey for both independent feature Units, and Morgan across each Unit's Definition of Done.
