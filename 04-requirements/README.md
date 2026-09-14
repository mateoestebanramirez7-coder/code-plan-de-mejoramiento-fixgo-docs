# 04 — Requirements

> **What is this?** The formal specification of what the system must do.
> Functional: what it does. Non-functional: how well it does it.

## Why this section exists

Requirements are the contract between the team and the client/stakeholder.
Without them:
- There is no way to verify whether the system is complete
- Scope changes have no baseline for comparison
- Tests have no success criterion

---

## Types of requirements

### Functional (FR)
Describe **what the system does**: functions, behaviors, data transformations.
*Example: "The system must allow the user to recover their password via email."*

### Non-functional (NFR)
Describe **how it does it**: quality, performance, availability, security.
*Example: "The system must respond in less than 200ms for 95% of requests."*

NFRs are usually harder to meet than FRs and are ignored more frequently. **They are equally important.**

---

## What is here and how to fill it in

### `functional.md` ⭐
List of all the system's functional requirements.
**Fill in:** numbered, with the module/service they belong to, source (originating HU), priority.

**Format:**
```markdown
| ID | Module | Description | Source (HU) | Priority |
|----|--------|-------------|------------|---------|
| FR-001 | IAM / Auth | The system must allow drivers and mechanics to register and authenticate securely via Firebase. | HU-FIX-001 | High |
| FR-002 | Geolocation | The system must capture and validate real-time GPS coordinates with a precision of at least 15 meters. | HU-FIX-002 | High |
| FR-003 | Dispatch | The system must automatically match and dispatch a service request to the nearest available verified mechanic. | HU-FIX-003 | High |
| FR-004 | Service Tracking | The system must provide live status updates of the mechanic's transit and service resolution to the driver. | HU-FIX-004 | Medium |
```

### `non-functional.md` ⭐
Quality, performance, and technical constraint requirements.
**Fill in:** by category (performance, availability, security, scalability, etc.)

**Format:**
```markdown
## Performance
| ID | Requirement | Metric | How to verify |
|----|------------|--------|--------------|
| NFR-001 | Matching response SLA | Matching dispatch under 3 seconds | Automated performance testing with simulation tools |
| NFR-002 | API response time | p95 < 200ms for core endpoints | Load testing using K6 or Apache JMeter |

## Availability
| ID | Requirement | Metric | How to verify |
|----|------------|--------|--------------|
| NFR-010 | System uptime | 99.9% monthly availability | Production cloud monitoring and uptime alerts |

## Security
| ID | Requirement | Description |
|----|------------|-------------|
| NFR-020 | Authentication | JWT-based authentication with 1-hour token expiration |
| NFR-021 | Data Encryption | AES-256 encryption applied to sensitive user profile and transaction data at rest |
```

### `user-stories.md`
Formalized user stories (coming from the `03-product/` backlog).
**Fill in:** with As/I want/So that format + verifiable acceptance criteria.

### `traceability-matrix.md` ⭐
Table that connects: HU → Requirement → Test case.
**Fill in:** when you have requirements and tests defined. Allows coverage verification.

**Format:**
```markdown
## HU-FIX-001: Driver Account Registration
**As** a stranded driver
**I want** to register an account and authenticate using Firebase
**So that** I can securely access the platform and request roadside assistance

### Acceptance criteria
- [ ] AC1: Given a new driver on the sign-up screen, when they provide a valid email and password, then a Firebase authentication credential is created and a `DriverRegistered` event is emitted.
- [ ] AC2: Given invalid email formatting, when the user attempts registration, then an error message is displayed.

### Technical notes
Must integrate with Firebase Authentication and validate unique user profiles in the User Management bounded context.

**Estimation:** 5 SP  **Priority:** High

---

## HU-FIX-002: Real-Time GPS Geolocation
**As** a driver or mechanic
**I want** the system to track geographic coordinates accurately
**So that** matching algorithms can pinpoint the nearest roadside assistance units

### Acceptance criteria
- [ ] AC1: Given an active user session, when location permission is granted, then coordinates are captured with a precision threshold under 15 meters.
- [ ] AC2: Given disabled location services, when attempting a service request, then a prompt requests location activation.

### Technical notes
Leverage device geolocation APIs and validate coordinate data streaming within the routing service.

**Estimation:** 8 SP  **Priority:** High

---

## HU-FIX-003: Automated Service Dispatch
**As** a stranded driver
**I want** the system to automatically match my request with the closest available mechanic
**So that** my emergency response time stays under the 3-second SLA threshold

### Acceptance criteria
- [ ] AC1: Given a confirmed service request with valid coordinates, when the dispatch algorithm runs, then the nearest verified available mechanic receives a push notification within 3 seconds.
- [ ] AC2: Given no mechanics are available within the radius, when searching, then a retry notification and expanded radius prompt are triggered.

### Technical notes
Implemented via asynchronous event messaging using RabbitMQ and spatial querying in PostgreSQL.

**Estimation:** 8 SP  **Priority:** High
```

### `_template-hu.md`
HU-[MODULE]-[ID]: [Title of the User Story]
As a [type of user / role]
I want [some goal or feature]
So that [some benefit, value, or business objective]

Acceptance criteria
[ ] AC1: Given [initial context or pre-state], when [action or event occurs], then [expected system outcome/behavior].

[ ] AC2: Given [alternative context], when [action occurs], then [error handling or secondary outcome].

Technical notes
[Include architectural constraints, dependencies, event triggers, or database considerations here.]

Estimation: [X] SP  Priority: [High / Medium / Low]

### `_template-nfr.md`
NFR-[CATEGORY]-[ID]: [Title of Non-Functional Requirement]
Category: [Performance / Availability / Security / Scalability / Maintainability]
Target Metric: [Specific, quantifiable metric, e.g., p95 < 200ms or 99.9% uptime]
Verification Method: [Exact testing tool or monitoring procedure, e.g., K6 load testing or cloud metrics]

Description & Constraint
[Detailed explanation of the technical constraint, compliance standard, or quality attribute requirement that the system must satisfy.]

---

## Correlations with other sections

| This section feeds... | Why |
|-----------------------|-----|
| `05-architecture/` | Performance/availability NFRs guide architectural decisions |
| `11-quality/testing-strategy.md` | Each FR must have at least one test case |
| `09-microservices/` | FRs are grouped by responsible service |
| `07-api/` | Integration FRs → endpoints in API contracts |
| `15-project-control/risks.md` | Very demanding NFRs usually generate technical risks |

---

## Common mistakes to avoid

❌ **"The system must be fast"** → Not measurable. Better: "p95 < 200ms"

❌ **"The system must be secure"** → Not verifiable. Better: "Authentication with JWT, tokens expire in 1h"

❌ Writing requirements that describe the solution instead of the problem.

✅ A good requirement is: **specific, measurable, achievable, relevant, and verifiable**.

---

## Questions this section must answer

- What must the system do for each type of user?
- With what speed, availability, and security?
- Which requirement originates each test case?
- Are all requirements covered by tests?
