# Requirements Analysis Questions

The initial request fixes the application shape: a health walking-skeleton Unit followed by independent payments and notifications feature Units, built with team-owned Construction. These questions settle the remaining behavior and quality requirements.

## Q1. [Developer] Which application stack should the REST API use?

The repository is empty, so the runtime and framework must be selected before implementation and test commands can be defined.

- A. Python 3.12 with FastAPI and Uvicorn (Suggested)
- B. TypeScript on Node.js 22 with Fastify
- C. TypeScript on Node.js 22 with Express
- D. Go with the standard `net/http` package
- E. Another stack
- X. Other (please specify)

[Answer]: A. Python 3.12 with FastAPI and Uvicorn (Suggested)

## Q2. [Product owner] What should `POST /payments` do in this minimal API?

This determines whether the endpoint demonstrates an API contract or performs real payment processing. No payment-card details will be accepted unless you explicitly require a provider integration.

- A. Validate amount and currency, then return a synthetic accepted payment with an ID (Suggested)
- B. Store and return simulated payment records without contacting a provider
- C. Integrate with a real payment provider
- D. Expose a read-only sample response instead of creating payments
- X. Other (please specify)

[Answer]: A. Validate amount and currency, then return a synthetic accepted payment with an ID (Suggested)

## Q3. [Product owner] What should `POST /notifications` do in this minimal API?

This determines whether the endpoint demonstrates submission behavior or sends a real message.

- A. Validate channel, recipient, and message, then return a synthetic accepted notification with an ID (Suggested)
- B. Store simulated notification records without contacting a provider
- C. Integrate with a real email, SMS, or push provider
- D. Expose a read-only sample response instead of creating notifications
- X. Other (please specify)

[Answer]: A. Validate channel, recipient, and message, then return a synthetic accepted notification with an ID (Suggested)

## Q4. [Architect] How much state should the application retain?

Payments and notifications can be accepted without persistence, kept only until restart, or stored durably. The two feature Units must remain independent.

- A. Stateless: validate each request and return a generated response without retaining records (Suggested)
- B. Keep records in memory until the process restarts
- C. Persist records in a local database
- D. Persist records in a managed database
- X. Other (please specify)

[Answer]: A. Stateless: validate each request and return a generated response without retaining records (Suggested)

## Q5. [Security engineer] Who may call the API, and what data may requests contain?

The access model affects authentication requirements. A minimal demonstration can remain unauthenticated if it accepts only synthetic data and is not exposed publicly.

- A. Local or test use only, no authentication, and no real personal or payment-card data (Suggested)
- B. Internal network use with an API key and no payment-card data
- C. Public access with token-based authentication and no payment-card data
- D. Public access with real sensitive data and formal compliance controls
- X. Other (please specify)

[Answer]: A. Local or test use only, no authentication, and no real personal or payment-card data (Suggested)

## Q6. [Operations engineer] What should `GET /health` report?

The walking-skeleton Unit needs a testable response that proves the application starts and can serve HTTP traffic.

- A. Return HTTP 200 with JSON status `ok` when the process is ready (Suggested)
- B. Return separate liveness and readiness details from one endpoint
- C. Provide separate `/health/live` and `/health/ready` endpoints
- X. Other (please specify)

[Answer]: A. Return HTTP 200 with JSON status `ok` when the process is ready (Suggested)

## Q7. [Quality engineer] Which baseline service targets should apply?

Concrete targets make the API testable without adding production-scale requirements to a workshop application.

- A. Each endpoint responds within 500 ms in local tests; invalid requests return JSON errors; duplicate submissions are safe when the same idempotency key is supplied (Suggested)
- B. Use a 1-second local response target and standard JSON validation errors; do not require idempotency
- C. Define production-grade availability, throughput, and latency targets now
- D. Require only functional correctness, with no response-time target
- X. Other (please specify)

[Answer]: A. Each endpoint responds within 500 ms in local tests; invalid requests return JSON errors; duplicate submissions are safe when the same idempotency key is supplied (Suggested)

## Q8. [Architect] How should repeated idempotency keys behave without stored records?

The stateless choice says the service retains no records, while the service-target choice requires duplicate submissions to be safe. The response behavior must reconcile those decisions.

- A. Derive the same synthetic ID from the idempotency key and request content, so retries return the same response without storage (Suggested)
- B. Remove the idempotency requirement because the API has no side effects
- C. Keep a temporary in-memory idempotency registry until restart
- D. Persist idempotency records durably
- X. Other (please specify)

[Answer]: A. Derive the same synthetic ID from the idempotency key and request content, so retries return the same response without storage (Suggested)
## Consolidated Summary Confirmation

- Use Python 3.12 with FastAPI and Uvicorn.
- `GET /health` returns HTTP 200 with JSON status `ok` when the process is ready.
- `POST /payments` validates amount and currency, accepts no payment-card details, and returns a synthetic accepted payment with an ID.
- `POST /notifications` validates channel, recipient, and message, then returns a synthetic accepted notification with an ID.
- The application is stateless and retains no payment or notification records.
- The API is limited to local or test use, requires no authentication, and accepts no real personal or payment-card data.
- Every endpoint responds within 500 ms in local tests, and invalid requests return JSON errors.
- Payments and notifications accept an idempotency key; retries with the same key and request content derive the same synthetic ID and response without storage.
- The health endpoint is the walking-skeleton Unit; payments and notifications are independent feature Units built with team-owned Construction.
- Development follows TDD, with a failing test written before each implementation change.

Does this all look correct before I generate the artifact?

- Looks correct
- Request changes

[Answer]: Looks correct
