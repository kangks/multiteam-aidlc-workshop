# Unit Dependency Topology

## Sources and Limits

- The approved `requirements.md` and affirmed `team-practices.md` define the three Units and their independence constraints.
- The existing `stories.md` provides supporting story IDs but was skipped before approval.
- Domain Design was skipped, so this topology does not claim validation against a component catalogue.
- This document defines dependencies only. Delivery Planning decides the economic delivery sequence.

## Machine-Readable Unit Graph

```yaml
units:
  - name: u1-health-skeleton
    kind: service
    depends_on: []
  - name: u2-payments
    kind: library
    depends_on: [u1-health-skeleton]
  - name: u3-notifications
    kind: library
    depends_on: [u1-health-skeleton]
```

## Dependency Diagram

```text
[u1-health-skeleton]
          |
          +------> [u2-payments]
          |
          +------> [u3-notifications]
```

The arrows mean that each feature Unit depends on the health foundation. There is no edge between payments and notifications.

## Direct Dependencies

| Unit | Depends On | Reason |
|---|---|---|
| U1 `u1-health-skeleton` | None | It owns the runnable FastAPI foundation and shared interfaces. |
| U2 `u2-payments` | U1 `u1-health-skeleton` | It registers a payment router and uses shared error, diagnostic, and deterministic-ID utilities. |
| U3 `u3-notifications` | U1 `u1-health-skeleton` | It registers a notification router and uses shared error, diagnostic, and deterministic-ID utilities. |

## Integrated Slice

U1 is the first resolved DAG Unit and the walking skeleton. It runs independently before later Units by starting the FastAPI process and serving `GET /health` end to end. It contains enough application packaging, routing, diagnostics, tests, and local startup behavior to prove the service foundation without payment or notification code.

## Integration Points

### U1 to U2

- **Mechanism:** In-process Python module interface.
- **Contract:** U2 exports a FastAPI router; U1's app factory registers it.
- **Shared utilities:** JSON error conventions, safe request diagnostics, and deterministic-ID derivation.
- **Data:** Validated payment request values enter only U2; U1 retains no payment business data.
- **Failure behavior:** Payment validation or handling failure produces a payment response and does not alter health or notification behavior.

### U1 to U3

- **Mechanism:** In-process Python module interface.
- **Contract:** U3 exports a FastAPI router; U1's app factory registers it.
- **Shared utilities:** JSON error conventions, safe request diagnostics, and deterministic-ID derivation.
- **Data:** Validated notification request values enter only U3; U1 retains no notification business data.
- **Failure behavior:** Notification validation or handling failure produces a notification response and does not alter health or payment behavior.

### U2 and U3

- **Mechanism:** Separate ways; there is no direct interface or data flow.
- **Constraint:** Neither Unit imports, calls, modifies, or requires the other Unit's source or tests.

## Parallel Development Opportunities

After U1 exists, U2 and U3 are dependency-ready at the same time because neither has an edge to the other. Each can be built in a separate project checkout containing the project baseline and U1, and each Unit's code and tests must pass without the sibling feature Unit.

## Cycle Check

The graph is acyclic:

- U1 has no dependency.
- U2 depends only on U1.
- U3 depends only on U1.
- No edge returns to U2 or U3 from U1, and no edge connects U2 to U3.
