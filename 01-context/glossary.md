# Project Glossary

> **Instructions:** Define here all technical and business terms used in the project.
> This is the official dictionary — if there is ambiguity, this document wins.
> Add terms throughout the project, not only at the start.

---

## How to use this glossary

1. Before using a technical or business term in code, docs, or conversations: look it up here.
2. If it's not there: add it with its definition.
3. If there is disagreement about the definition: discuss it as a team and update this document.

---

## Domain terms

| Term | Definition | Notes / Synonyms |
| :--- | :--- | :--- |
| **Driver** | Individual operating a vehicle who experiences an unexpected mechanical failure and requests on-site roadside assistance. | Synonyms: Client, User. Avoid: Customer (commercial sales context). |
| **Mechanic** | Verified automotive technician or workshop offering roadside diagnostics and mechanical intervention. | Synonyms: Service Provider, Workshop. Avoid: Contractor. |
| **Breakdown** | An unexpected vehicular failure (mechanical, electrical, or tire issue) immobilizing the driver. | Synonyms: Incident, Emergency. Avoid: Accident (FixGo does not handle collisions). |
| **Matchmaking** | The real-time algorithmic pairing of a driver's breakdown request with the nearest qualified mechanic. | Synonyms: Dispatch, Pairing. |
| **On-site Repair** | Diagnostic and mechanical services performed directly at the physical location of the immobilized vehicle. | Synonyms: Roadside Assistance. Avoid: In-shop overhaul. |
| **Service Request** | The digital ticket emitted by a driver specifying the failure type, vehicle model, and real-time GPS location. | Synonyms: Ticket, Help Request. |
| **Workshop** | A registered commercial automotive establishment that dispatches certified mobile mechanics. | Synonyms: Auto Repair Shop, Garage. |
| **Verification** | The mandatory approval workflow confirming a mechanic’s legal credentials and professional capacity. | Synonyms: Validation, Vetting. |
| **Dispatch Range** | The maximum geographic radius within which a mechanic is eligible to accept a breakdown request. | Synonyms: Service Radius, Coverage Area. |
| **Direct Settlement** | Out-of-app financial payment completed directly between driver and mechanic without platform intervention. | Synonyms: Offline Payment. Avoid: In-app transaction. |

---

## Technical terms of the project

| Term | Definition |
|------|-----------|
| Microservice | Independent service with a single responsibility, its own process, and its own database |
| Domain Event | A fact that occurred in the business that other services can observe. Name always in past tense. |
| Bounded Context | Boundary within which a particular domain model has consistent meaning |
| API Gateway | Single entry point to the system that routes requests to the corresponding microservices |
| Circuit Breaker | Pattern that stops calls to a failing service, preventing failure cascades |
| Saga | Sequence of local transactions across different services with compensating transactions on failure |
| Dead Letter Queue | Queue where messages that could not be processed after several retries are sent |
| Idempotence | Property of an operation to produce the same result if executed multiple times |

---

## Acronyms

| Acronym | Meaning |
|---------|---------|
| IAM | Identity and Access Management |
| JWT | JSON Web Token |
| API | Application Programming Interface |
| CRUD | Create, Read, Update, Delete |
| DTO | Data Transfer Object |
| FR | Functional Requirement |
| NFR | Non-Functional Requirement |
| SLO | Service Level Objective |
| SLA | Service Level Agreement |
| ADR | Architecture Decision Record |
| PR | Pull Request |
| DoD | Definition of Done |
| CI/CD | Continuous Integration / Continuous Delivery |
