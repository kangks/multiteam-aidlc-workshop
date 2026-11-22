# Team Practices

## Way of Working

- **Integration branch**: main
- We commit completed work directly to the shared `main` branch.
- We use team-owned Construction for this application.
- We divide the application into a health walking-skeleton Unit, a payments feature Unit, and a notifications feature Unit. After the health Unit establishes the shared foundation, the payments and notifications Units remain independent.

## Walking Skeleton

- We use a walking skeleton when the selected plan calls for one.
- For this application, the health endpoint is the first end-to-end Unit.
- We keep payments and notifications out of the walking skeleton and implement them as independent feature Units.

## Testing Posture

- **Methodology**: tdd
- **Methodology evidence**: The team confirmed TDD during the Practices Discovery interview.
- **Ordering**: Write a failing test before each implementation change, then make the change needed to pass the test.

## Deployment

- We deploy merges automatically to staging.
- We require separate approval before deploying to production.

## Code Style

- We use the chosen stack's standard formatter and linter.
- We follow idiomatic naming conventions for the chosen stack.
