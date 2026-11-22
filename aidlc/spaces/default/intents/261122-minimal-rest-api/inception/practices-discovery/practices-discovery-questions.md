# Practices Discovery Questions

These questions establish the team's working practices for this project. The organization defaults are suggestions, not established team decisions.

## Q1. [Delivery lead] How should completed work reach the shared branch?

This determines the integration branch and merge style used by team-owned Units. The suggested default is short-lived branches merged into `main` with one squash commit per Unit.

- A. Use short-lived branches, squash-merge each Unit into `main` (Suggested)
- B. Commit directly to `main`
- C. Use short-lived branches and merge commits into `main`
- D. Use another integration branch or merge approach
- X. Other (please specify)

[Answer]: B. Commit directly to `main`

## Q2. [Delivery lead] Build a thin end-to-end slice first? A walking skeleton is a minimal version that runs the whole way through, built first to prove the pieces connect before the real features go in.

This work already requires the health endpoint as its first walking-skeleton Unit. This answer determines the team's standing practice for future work.

- A. Use a walking skeleton when the selected plan calls for one (Suggested)
- B. Always use a walking skeleton for new applications and major features
- C. Use a walking skeleton only for this project
- D. Do not make it a standing team practice
- X. Other (please specify)

[Answer]: A. Use a walking skeleton when the selected plan calls for one (Suggested)

## Q3. [Quality engineer] When should tests be written relative to implementation?

This determines the testing methodology and ordering that each Unit follows. The suggested workshop default is to implement each testable layer, then write and run that layer's tests.

- A. Test-after: implement each testable layer, then write and run its tests (Suggested)
- B. TDD: write a failing test before each implementation change
- C. BDD: agree behavior scenarios first, then implement and test them
- D. ATDD: define acceptance tests first, then build until they pass
- E. Custom ordering
- X. Other (please specify)

[Answer]: B. TDD: write a failing test before each implementation change

## Q4. [Release engineer] How should successful changes be deployed?

This determines the default deployment cadence and production control. The suggested default deploys merges to staging automatically and requires a separate approval for production.

- A. Deploy merges automatically to staging; require approval for production (Suggested)
- B. Deploy automatically to both staging and production after checks pass
- C. Deploy manually to every environment
- D. Do not deploy this workshop application
- X. Other (please specify)

[Answer]: A. Deploy merges automatically to staging; require approval for production (Suggested)

## Q5. [Developer] Which code-style rules should govern the project?

No language or framework has been selected yet. The suggested default follows the chosen language's standard formatter and naming conventions, with linting before merge.

- A. Use the chosen stack's standard formatter, linter, and idiomatic naming (Suggested)
- B. Use the standard formatter and naming, but do not require linting before merge
- C. Define project-specific conventions after the technology stack is selected
- X. Other (please specify)

[Answer]: A. Use the chosen stack's standard formatter, linter, and idiomatic naming (Suggested)
## Consolidated Summary Confirmation

- Completed work is committed directly to the shared `main` branch.
- A walking skeleton is used when the selected plan calls for one; this application uses the health endpoint as that first end-to-end Unit.
- Development follows TDD: write a failing test before each implementation change.
- Merges deploy automatically to staging, while production requires separate approval.
- The project uses the chosen stack's standard formatter, linter, and idiomatic naming.

Does this all look correct before I generate the artifact?

- Looks correct
- Request changes

[Answer]: Looks correct
