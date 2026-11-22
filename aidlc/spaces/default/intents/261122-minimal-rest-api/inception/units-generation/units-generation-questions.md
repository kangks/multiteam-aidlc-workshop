# Units Generation Plan and Questions

## Known Decomposition Constraints

- The application has exactly three Units: a health walking-skeleton Unit, a payments feature Unit, and a notifications feature Unit.
- The health Unit is the smallest runnable end-to-end slice and establishes the shared FastAPI service foundation.
- Payments and notifications each depend on the health foundation but must not depend on one another.
- Construction is team-owned.
- The Unit dependency graph describes only dependency topology; Delivery Planning will decide the economic delivery sequence.
- Domain Design was skipped at the user's request, so the requirements and existing story artifacts are the boundary sources.

## Q1. [Architect] Which Unit kinds and deployment model should represent the three Units?

All three Units contribute to one minimal FastAPI application. The Unit kind determines which later design artifacts each Unit receives.

- A. Health is a `service`; payments and notifications are `library` Units embedded into that one deployable service (Suggested)
- B. All three are independently deployable `service` Units
- C. All three are `service` Units but ship together as one deployment
- D. Leave Unit kinds unspecified so every Unit receives the full design set
- X. Other (please specify)

[Answer]: A. Health is a `service`; payments and notifications are `library` Units embedded into that one deployable service (Suggested)

## Q2. [Architect] How should each feature Unit connect to the shared health foundation?

The boundary must let payments and notifications be built independently while integrating into the same FastAPI process.

- A. The health Unit owns the app factory and shared error and deterministic-ID utilities; each feature exposes a FastAPI router that the app factory registers (Suggested)
- B. Each feature edits the central application module directly to add its route
- C. Each feature runs as an independent FastAPI sub-application mounted by the health Unit
- D. Use another integration boundary
- X. Other (please specify)

[Answer]: A. The health Unit owns the app factory and shared error and deterministic-ID utilities; each feature exposes a FastAPI router that the app factory registers (Suggested)
## Consolidated Summary Confirmation

- Create exactly three Units: `U1` health walking skeleton, `U2` payments, and `U3` notifications.
- Use directory names `u1-health-skeleton`, `u2-payments`, and `u3-notifications`.
- Classify the health Unit as `service` and the two feature Units as `library` because they are embedded in one deployable FastAPI service.
- Make payments and notifications depend directly on the health foundation, with no dependency between the two feature Units.
- Let the health Unit own the FastAPI app factory, shared JSON error handling, and deterministic-ID utility.
- Require each feature Unit to expose a FastAPI router that the app factory registers.
- Keep the application stateless and let each Unit own its tests and source boundary.
- Record only dependency topology here; Delivery Planning will decide the economic delivery sequence.

Does this all look correct before I generate the artifact?

- Looks correct
- Request changes

[Answer]: Looks correct
