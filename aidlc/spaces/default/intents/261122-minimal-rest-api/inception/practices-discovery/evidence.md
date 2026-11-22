# Practices Discovery Evidence

## Initial Request

- The human requested: "build a minimal REST API with health, payments, and notifications endpoints, split into a walking-skeleton Unit and two independent feature Units, using team-owned Construction."
- The initial request establishes the application shape, team-owned Construction, the health walking-skeleton Unit, and the independent payments and notifications feature Units.

## Human-Confirmed Interview Decisions

The completed `practices-discovery-questions.md` records the human's confirmation of these practices:

- Completed work is committed directly to the shared `main` branch.
- A walking skeleton is used when the selected plan calls for one; this application uses the health endpoint as its first end-to-end Unit.
- Development uses TDD, with a failing test written before each implementation change.
- Merges deploy automatically to staging, and production deployment requires separate approval.
- The project uses the chosen stack's standard formatter, linter, and idiomatic naming.
- The consolidated summary was confirmed with `[Answer]: Looks correct` before final artifact generation.

## Organization Defaults Consulted

- `aidlc/spaces/default/memory/org.md` supplied suggested defaults for Way of Working, Walking Skeleton, Testing Posture, Deployment, and Code Style.
- The organization defaults were interview suggestions, not evidence of team decisions.
- The human selected direct commits to `main` instead of the suggested short-lived branch and squash-merge approach.
- The human selected TDD instead of the organization's fallback test-after methodology.
- The confirmed walking-skeleton, deployment, and code-style decisions align with the corresponding organization defaults.
- The matching sections in `aidlc/spaces/default/memory/team.md` and `aidlc/spaces/default/memory/project.md` contained no previously affirmed practices when the draft was prepared.

## Lead Assessment

- The health endpoint is the smallest end-to-end Unit that can establish the application's shared API foundation before feature work begins.
- Keeping payments and notifications in separate, mutually independent Units preserves the structure required by the initial request and supports team-owned Construction.
- Collaborators were off, so no support-agent contribution files were produced or used. The pipeline and deployment lead integrated the draft with the completed interview directly.
- The stage definition and pipeline/deployment guidance were inspected to distinguish required artifact structure from suggested working practices.

## Remaining Uncertainty

- The language, framework, runtime, package layout, formatter, and linter have not yet been selected.
- Exact test commands, test types beyond the TDD ordering, coverage targets, and CI implementation remain undecided.
- The staging and production platforms, production approver, deployment mechanism, and rollback procedure remain undecided.
- The exact health response contract and end-to-end verification command remain to be defined in later requirements and delivery work.
- The repository has no resolvable `HEAD`; the timestamp artifact therefore records the honest `UNCOMMITTED` sentinel.
