# User Stories — Backlog

> Each HU is derived from a functional requirement (RF) already referenced in
> `06-data/models.md` and grounded in the entities of `02-domain/entities-and-rules.md`.
> Format: standard HU with Acceptance Criteria in Given/When/Then.
> Refined (Ready) HUs go to the sprint. Unrefined ones are epics or ideas.

---

## Backlog status

| Cut | Sprint | Total HUs | Refined | In progress | Completed |
|-----|--------|-----------|---------|-------------|-----------|
| Cut 1 | Sprint 1-2 | 13 | 13 | 0 | 0 |
| Cut 2 | Sprint 3-4 | 0 | 0 | 0 | 0 |

---

## Epics

| ID | Epic | Description | SRS Module |
|----|------|-------------|-----------|
| EP-001 | User & Credential Management | Registration, login, session, and account lifecycle for Drivers and Mechanics | Module 1 |
| EP-002 | Vehicle Management | Drivers register and maintain the vehicles they may request assistance for | Module 2 |
| EP-003 | Service Request | Creation and lifecycle of an assistance ticket (`ServiceRequest`) | Module 3 |
| EP-004 | Matchmaking & Tracking | Assignment of the nearest mechanic and live status/GPS follow-up | Module 4 |

---

## User Stories

### HU-AUTH-001 — Register as Driver or Mechanic {#HU-AUTH-001}

**Epic:** EP-001 (RF1.1)

> **As** a person without an account
> **I want** to register with email, username, ID document, password, and role (Driver or Mechanic)
> **so that** I can access FixGo's roadside assistance services

**Acceptance Criteria:**

```gherkin
Scenario 1: Successful registration
  Given I am on the registration screen with no existing account
  When  I submit a unique email, unique username, unique ID document, a valid password, and select my role
  Then  the system creates my user with status ACTIVE and role DRIVER or MECHANIC
  And   an email verification is triggered

Scenario 2: Duplicate email or document
  Given an account already exists with that email or ID document
  When  I try to register again with the same value
  Then  the system rejects the registration with a descriptive validation error
```

**Definition of Done:**
- [ ] Code reviewed and approved
- [ ] Unit tests written
- [ ] Acceptance criteria verified (manual or automated)
- [ ] API contract updated if applicable
- [ ] Deployed to staging

| Field | Value |
|-------|-------|
| Story Points | 5 |
| Priority | Must Have |
| Target sprint | Sprint 1 |
| Status | Backlog |
| Dependencies | — |
| Affected service(s) | `auth-service` |

---

### HU-AUTH-002 — Log in with account lockout protection {#HU-AUTH-002}

**Epic:** EP-001 (RF1.2)

> **As** a registered Driver or Mechanic
> **I want** to log in with my credentials and have my account temporarily locked after repeated failures
> **so that** I can access my account safely and be protected from brute-force attempts

**Acceptance Criteria:**

```gherkin
Scenario 1: Successful login
  Given I have a registered, ACTIVE, non-locked account
  When  I submit the correct email/username and password
  Then  the system authenticates me and issues a session/JWT

Scenario 2: Account lockout after repeated failures
  Given I have submitted 5 consecutive failed login attempts
  When  I attempt to log in again
  Then  the system locks the account until `locked_until` and shows a lockout message
```

| Field | Value |
|-------|-------|
| Story Points | 3 |
| Priority | Must Have |
| Target sprint | Sprint 1 |
| Status | Backlog |
| Dependencies | HU-AUTH-001 |
| Affected service(s) | `auth-service` |

---

### HU-AUTH-003 — Assign and enforce role on registration {#HU-AUTH-003}

**Epic:** EP-001 (RF1.3, ERF1.3.1)

> **As** the system
> **I want** every account to have exactly one role (DRIVER, MECHANIC, or ADMIN) and support an optional second authentication factor
> **so that** access to role-specific features is enforced consistently

**Acceptance Criteria:**

```gherkin
Scenario 1: Default role
  Given a new registration with no explicit role selected
  When  the account is created
  Then  the role defaults to DRIVER

Scenario 2: MFA enabled
  Given an account with `mfa_enabled = true`
  When  the user logs in with correct credentials
  Then  the system requests the second factor before issuing the session
```

| Field | Value |
|-------|-------|
| Story Points | 3 |
| Priority | Should Have |
| Target sprint | Sprint 2 |
| Status | Backlog |
| Dependencies | HU-AUTH-001 |
| Affected service(s) | `auth-service` |

---

### HU-AUTH-004 — Deactivate / reactivate account {#HU-AUTH-004}

**Epic:** EP-001 (RF1.5, RF1.6)

> **As** a registered Driver or Mechanic
> **I want** to deactivate my account and have it reactivated under controlled conditions
> **so that** I can stop using the platform without permanently losing my history

**Acceptance Criteria:**

```gherkin
Scenario 1: Deactivation
  Given I am authenticated with an ACTIVE account
  When  I request account deactivation
  Then  the status changes to DEACTIVATED and I can no longer log in normally

Scenario 2: Reactivation
  Given a DEACTIVATED account
  When  an authorized flow reactivates it
  Then  the status returns to ACTIVE and prior history (vehicles, requests) remains intact
```

| Field | Value |
|-------|-------|
| Story Points | 2 |
| Priority | Should Have |
| Target sprint | Sprint 2 |
| Status | Backlog |
| Dependencies | HU-AUTH-001 |
| Affected service(s) | `auth-service` |

---

### HU-AUTH-005 — Update profile data {#HU-AUTH-005}

**Epic:** EP-001 (RF1.7)

> **As** a registered Driver or Mechanic
> **I want** to update my account/profile information
> **so that** my data stays accurate over time

**Acceptance Criteria:**

```gherkin
Scenario 1: Successful update
  Given I am authenticated
  When  I submit valid changes to my editable profile fields
  Then  the system persists the change and updates `updated_at`

Scenario 2: Invalid update
  Given I am authenticated
  When  I submit a value that violates a uniqueness or format constraint (e.g. an email already in use)
  Then  the system rejects the update with a descriptive error
```

| Field | Value |
|-------|-------|
| Story Points | 2 |
| Priority | Should Have |
| Target sprint | Sprint 2 |
| Status | Backlog |
| Dependencies | HU-AUTH-001 |
| Affected service(s) | `auth-service` |

---

### HU-VEH-001 — Register a vehicle {#HU-VEH-001}

**Epic:** EP-002 (RF2.1)

> **As** a registered Driver
> **I want** to register a vehicle with plate, brand, model, and type
> **so that** I can request roadside assistance for it

**Acceptance Criteria:**

```gherkin
Scenario 1: Successful registration
  Given I am authenticated as a Driver
  When  I submit a unique plate, brand, model, and vehicle type
  Then  the system creates the vehicle linked to my driver profile with status ACTIVE

Scenario 2: Duplicate plate
  Given a vehicle with that plate already exists in the system
  When  I try to register another vehicle with the same plate
  Then  the system rejects the registration with a descriptive validation error
```

| Field | Value |
|-------|-------|
| Story Points | 3 |
| Priority | Must Have |
| Target sprint | Sprint 1 |
| Status | Backlog |
| Dependencies | HU-AUTH-001 |
| Affected service(s) | `auth-service` |

---

### HU-VEH-002 — Edit vehicle details {#HU-VEH-002}

**Epic:** EP-002 (RF2.2, ERF2.1.1)

> **As** a registered Driver
> **I want** to edit my vehicle's optional details (color, year, notes)
> **so that** mechanics have more context about my vehicle when responding

**Acceptance Criteria:**

```gherkin
Scenario 1: Successful edit
  Given I own a registered vehicle
  When  I update its color, year, or notes
  Then  the system persists the change and updates `updated_at`
```

| Field | Value |
|-------|-------|
| Story Points | 2 |
| Priority | Could Have |
| Target sprint | Sprint 2 |
| Status | Backlog |
| Dependencies | HU-VEH-001 |
| Affected service(s) | `auth-service` |

---

### HU-VEH-003 — Select a registered vehicle when requesting service {#HU-VEH-003}

**Epic:** EP-002 (RF2.3)

> **As** a registered Driver with one or more vehicles
> **I want** to pick which of my vehicles a service request is for
> **so that** the mechanic knows exactly what vehicle they will be attending

**Acceptance Criteria:**

```gherkin
Scenario 1: Vehicle selected
  Given I have at least one ACTIVE vehicle registered
  When  I create a service request
  Then  I must select one of my ACTIVE vehicles and the request stores its `vehicle_id`
```

| Field | Value |
|-------|-------|
| Story Points | 1 |
| Priority | Must Have |
| Target sprint | Sprint 1 |
| Status | Backlog |
| Dependencies | HU-VEH-001, HU-REQ-001 |
| Affected service(s) | `auth-service`, `service-request` |

---

### HU-VEH-004 — Deactivate a vehicle without losing history {#HU-VEH-004}

**Epic:** EP-002 (RF2.4)

> **As** a registered Driver
> **I want** to deactivate a vehicle instead of deleting it
> **so that** the service history tied to that vehicle is preserved

**Acceptance Criteria:**

```gherkin
Scenario 1: Deactivation
  Given I own an ACTIVE vehicle with prior service history
  When  I deactivate it
  Then  its status changes to INACTIVE and it no longer appears as selectable in HU-VEH-003
  And   its past service requests remain visible in my history
```

| Field | Value |
|-------|-------|
| Story Points | 2 |
| Priority | Should Have |
| Target sprint | Sprint 2 |
| Status | Backlog |
| Dependencies | HU-VEH-001 |
| Affected service(s) | `auth-service` |

---

### HU-VEH-005 — See an estimated cost before confirming a request {#HU-VEH-005}

**Epic:** EP-002 (RF2.5)

> **As** a registered Driver
> **I want** to see an estimated cost for the assistance before I confirm my request
> **so that** I can decide whether to proceed

**Acceptance Criteria:**

```gherkin
Scenario 1: Estimate shown
  Given I am creating a service request for a selected vehicle and problem type
  When  the system calculates an estimate
  Then  `estimated_cost` is shown to me before I confirm the request
```

| Field | Value |
|-------|-------|
| Story Points | 3 |
| Priority | Could Have |
| Target sprint | Sprint 3 |
| Status | Backlog |
| Dependencies | HU-REQ-001 |
| Affected service(s) | `service-request` |

---

### HU-REQ-001 — Create a roadside assistance request {#HU-REQ-001}

**Epic:** EP-003 (RF3.1)

> **As** a Driver stranded with a vehicle breakdown
> **I want** to create a service request describing the problem type and its location
> **so that** the nearest mechanic can be dispatched to help me

**Acceptance Criteria:**

```gherkin
Scenario 1: Successful request
  Given I am authenticated as a Driver with an ACTIVE vehicle and GPS permission granted
  When  I submit a problem type, optional description, and my current GPS location
  Then  the system creates a `ServiceRequest` with status PENDING and emits `ServiceRequested`

Scenario 2: Missing location permission
  Given the mobile app does not have GPS permission granted
  When  I try to create a request
  Then  the system blocks creation and prompts me to enable location access
```

| Field | Value |
|-------|-------|
| Story Points | 5 |
| Priority | Must Have |
| Target sprint | Sprint 1 |
| Status | Backlog |
| Dependencies | HU-AUTH-001, HU-VEH-001 |
| Affected service(s) | `service-request` |

---

### HU-REQ-002 — Set request priority {#HU-REQ-002}

**Epic:** EP-003 (RF3.2)

> **As** a Driver creating a service request
> **I want** to indicate the urgency of my situation
> **so that** critical cases can be prioritized in matchmaking

**Acceptance Criteria:**

```gherkin
Scenario 1: Default priority
  Given I create a request without specifying priority
  When  the request is saved
  Then  `priority` defaults to NORMAL

Scenario 2: Explicit high priority
  Given I mark my situation as urgent (e.g. unsafe location, highway shoulder)
  When  I submit the request
  Then  `priority` is stored as HIGH and is considered in matchmaking ordering
```

| Field | Value |
|-------|-------|
| Story Points | 2 |
| Priority | Should Have |
| Target sprint | Sprint 2 |
| Status | Backlog |
| Dependencies | HU-REQ-001 |
| Affected service(s) | `service-request` |

---

### HU-REQ-003 — Track my own request status {#HU-REQ-003}

**Epic:** EP-003 (RF3.3)

> **As** a Driver with an open service request
> **I want** to see its current status update in real time
> **so that** I know what is happening while I wait

**Acceptance Criteria:**

```gherkin
Scenario 1: Status changes
  Given my request moves between PENDING, ACCEPTED, ON_THE_WAY, IN_PROGRESS, COMPLETED, or CANCELLED
  When  the status changes in `service-request`
  Then  the change is reflected in `/service_requests/{requestId}/status` in Firebase within the sync layer
  And   I see the updated status in the app without refreshing manually
```

| Field | Value |
|-------|-------|
| Story Points | 5 |
| Priority | Must Have |
| Target sprint | Sprint 2 |
| Status | Backlog |
| Dependencies | HU-REQ-001 |
| Affected service(s) | `service-request`, `geolocation-service` |

---

### HU-REQ-004 — Cancel a request with a reason {#HU-REQ-004}

**Epic:** EP-003 (RF3.4)

> **As** a Driver with an active service request
> **I want** to cancel it and state a reason
> **so that** the mechanic and the platform know I no longer need assistance

**Acceptance Criteria:**

```gherkin
Scenario 1: Successful cancellation
  Given my request has status PENDING or ACCEPTED
  When  I cancel it and provide a `cancellation_reason`
  Then  the status changes to CANCELLED and the reason is stored

Scenario 2: Cannot cancel a completed request
  Given my request has status COMPLETED
  When  I attempt to cancel it
  Then  the system rejects the cancellation
```

| Field | Value |
|-------|-------|
| Story Points | 2 |
| Priority | Must Have |
| Target sprint | Sprint 2 |
| Status | Backlog |
| Dependencies | HU-REQ-001 |
| Affected service(s) | `service-request` |

---

### HU-REQ-005 — Enforce the full request lifecycle {#HU-REQ-005}

**Epic:** EP-003 (RF3.5, INV-003, INV-004)

> **As** the system
> **I want** to enforce the request status lifecycle (PENDING → ACCEPTED → ON_THE_WAY → IN_PROGRESS → COMPLETED, or → CANCELLED at any point before COMPLETED)
> **so that** requests cannot skip states or close without a valid diagnostic

**Acceptance Criteria:**

```gherkin
Scenario 1: Invalid transition rejected
  Given a request with status PENDING
  When  an attempt is made to move it directly to COMPLETED
  Then  the system rejects the transition as invalid

Scenario 2: Completion requires a diagnostic
  Given a request with status IN_PROGRESS
  When  the mechanic attempts to mark it COMPLETED without a registered `Diagnostic`
  Then  the system blocks the transition (INV-004)

Scenario 3: Matching under SLA
  Given a new PENDING request
  When  the matchmaking process runs
  Then  it must complete (assign or fail) within 3 seconds (INV-003)
```

| Field | Value |
|-------|-------|
| Story Points | 8 |
| Priority | Must Have |
| Target sprint | Sprint 2 |
| Status | Backlog |
| Dependencies | HU-REQ-001 |
| Affected service(s) | `service-request`, `service-execution` |

---

### HU-DISP-001 — View incoming requests as a Mechanic {#HU-DISP-001}

**Epic:** EP-004 (RF4.1)

> **As** a verified Mechanic marked AVAILABLE
> **I want** to see nearby pending or assigned service requests
> **so that** I can accept and respond to them

**Acceptance Criteria:**

```gherkin
Scenario 1: List of requests
  Given I am authenticated as a Mechanic with `verification_status = VERIFIED` and `availability_status = AVAILABLE`
  When  I open my requests list
  Then  I see PENDING requests matched to me and my own ACCEPTED/ON_THE_WAY/IN_PROGRESS requests, filterable by status

Scenario 2: Unverified mechanic
  Given my `verification_status` is PENDING or REJECTED
  When  I try to view incoming requests
  Then  the system blocks access and explains I must complete verification first (INV-001)
```

| Field | Value |
|-------|-------|
| Story Points | 3 |
| Priority | Must Have |
| Target sprint | Sprint 2 |
| Status | Backlog |
| Dependencies | HU-REQ-001 |
| Affected service(s) | `service-request` |

---

### HU-DISP-002 — Share live GPS location during a service {#HU-DISP-002}

**Epic:** EP-004 (RF4.2, INV-002)

> **As** a Mechanic en route to or performing a service
> **I want** my current location to be tracked with 15-meter accuracy
> **so that** the Driver can see my real-time position and ETA

**Acceptance Criteria:**

```gherkin
Scenario 1: Live tracking
  Given I have accepted a request and my GPS is active
  When  my device reports a new coordinate
  Then  `/mechanics/{mechanicId}/location` in Firebase is updated with `{lat, lng, updatedAt}` within 15-meter accuracy

Scenario 2: GPS lost
  Given my GPS signal is lost mid-service
  When  no location update arrives for a defined threshold
  Then  the Driver's app shows a "location unavailable" state instead of a stale position
```

| Field | Value |
|-------|-------|
| Story Points | 5 |
| Priority | Must Have |
| Target sprint | Sprint 2 |
| Status | Backlog |
| Dependencies | HU-DISP-001 |
| Affected service(s) | `geolocation-service` |

---

### HU-DISP-003 — Register the final diagnostic and close the service {#HU-DISP-003}

**Epic:** EP-004 (RF4.3, RF4.4)

> **As** a Mechanic finishing an on-site repair
> **I want** to submit a diagnostic record and mark the service as completed
> **so that** the request is closed with a traceable outcome

**Acceptance Criteria:**

```gherkin
Scenario 1: Successful closure
  Given a request with status IN_PROGRESS assigned to me
  When  I submit `findings` and a `final_status`
  Then  the system creates the `Diagnostic` record, marks the request COMPLETED, and emits `ServiceCompleted`

Scenario 2: One diagnostic per request
  Given a request already has a registered `Diagnostic`
  When  I try to submit a second diagnostic for the same request
  Then  the system rejects it, since `request_id` is unique in `diagnostics` (RF4.4)
```

| Field | Value |
|-------|-------|
| Story Points | 5 |
| Priority | Must Have |
| Target sprint | Sprint 2 |
| Status | Backlog |
| Dependencies | HU-REQ-005 |
| Affected service(s) | `service-execution` |

---

### HU-DISP-004 — Receive real-time notifications {#HU-DISP-004}

**Epic:** EP-004 (RF4.5)

> **As** a Driver or Mechanic involved in an active service
> **I want** to receive a notification whenever the request status changes
> **so that** I don't have to keep the app open and checking manually

**Acceptance Criteria:**

```gherkin
Scenario 1: Status-change notification
  Given a request I'm involved in changes status (e.g. ACCEPTED, ON_THE_WAY, COMPLETED)
  When  the change is persisted
  Then  a notification is queued at `/notifications/{userId}` and pushed via Firebase Cloud Messaging

Scenario 2: Notifications disabled at OS level
  Given the user's OS has blocked push notifications for the app
  When  a status change occurs
  Then  the notification still appears in the in-app notification center at `/notifications/{userId}`
```

| Field | Value |
|-------|-------|
| Story Points | 3 |
| Priority | Should Have |
| Target sprint | Sprint 3 |
| Status | Backlog |
| Dependencies | HU-REQ-005 |
| Affected service(s) | `geolocation-service` |

---

## Deferred — pending an ADR (Module 7, Auditing)

Module 7 of the SRS requires querying audit logs by date range, user, action type, module, and
operation status. As noted in `06-data/models.md`, **no table or service exists yet** for this,
and the team must first decide between a decentralized or centralized audit model before these
HUs can be written with a concrete `Affected service(s)` field. Placeholder epic reserved:

| ID | Epic | Description | SRS Module |
|----|------|-------------|-----------|
| EP-005 | Audit & Traceability | Query and export system audit logs | Module 7 (blocked on ADR) |

---

## Rules for writing HUs

### 1. The role matters
Do not write "As a user" — that says nothing. Use the specific role:
```
✓ As a registered Driver
✓ As a verified Mechanic
✓ As a system administrator
✗ As a user
✗ As a person
```

### 2. The benefit justifies the work
The "so that" must describe a business benefit, not redescribe the action:
```
✓ so that I can resume my journey without waiting on hold with a call center
✗ so that I can see my request (this only describes the feature)
```

### 3. ACs are verifiable
Each AC must be verifiable manually or automatable as a test:
```
✓ Then the system shows a message "Order #123 confirmed"
✓ Then the confirmation email arrives in less than 30 seconds
✗ Then the system works well (not verifiable)
✗ Then the user is satisfied (not verifiable)
```

### 4. One HU = one unit of value
If the HU has 15 ACs, it is probably 3 HUs.
The team must be able to complete it in one sprint (maximum 2 weeks).

---

## Correlations

- Full template with DoD checklist → `04-requirements/_template-hu.md`
- Non-functional requirements → `04-requirements/non-functional.md`
- Traceability matrix → `04-requirements/traceability-matrix.md`
- Domain entities and invariants (INV-00X) → `02-domain/entities-and-rules.md`
- Data model and RF source citations → `06-data/models.md`
- API contracts derived from these HUs → `07-api/contracts/openapi/`
