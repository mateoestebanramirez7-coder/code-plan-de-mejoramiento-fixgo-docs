# Data Models per Service

> Source of truth for entities: `02-domain/entities-and-rules.md`, `02-domain/domain-map.md`,
> `02-domain/domain-events.md`, and the **FixGo SRS v1.0** (Modules 1, 2, 3, and 7).
> Each service is the sole owner of its tables — no other service queries them directly
> (see `09-microservices/service-catalog.md` → Data ownership matrix).

---

## Database engine

**Primary engine:** MySQL — for all transactional entities in the system (users, vehicles,
service requests, diagnostics).

**Justification:** Relationships between Driver, Vehicle, Mechanic, and ServiceRequest are
strongly relational (FKs, referential integrity) and require ACID transactions — for
example, a `ServiceRequest` cannot exist without a valid `driver_id`, nor can a `Vehicle`
exist without its owner. MySQL fits this case better than a document store.

**Note on Firebase Realtime Database:** The SRS (IS4, IC2) requires real-time
synchronization of requests, statuses, and GPS locations via WebSockets/Firebase. FixGo
uses **MySQL as the transactional source of truth** and **Firebase Realtime Database as an
ephemeral sync layer** (live state of a `ServiceRequest`, mechanic's GPS position). This
hybrid decision must be formally documented in an ADR
(`05-architecture/decisions/records/`) — that ADR does not exist yet and is a pending item
for the team.

---

## Data ownership matrix

| Entity | Owner service | How other services access it |
|---|---|---|
| User / credentials | `auth-service` | REST API (`GET /users/:id`) or JWT |
| Driver (profile) | `auth-service` | REST API |
| Mechanic (profile) | `auth-service` | REST API |
| Vehicle | `auth-service` | REST API (`GET /vehicles/:id`) — referenced by `vehicleId` in `service-request` |
| ServiceRequest | `service-request` | `ServiceRequested` event / REST API |
| GPSLocation (live) | `geolocation-service` (Firebase) | WebSocket / event |
| Diagnostic / CompletionRecord | `service-execution` | `ServiceCompleted` event |
| AuditLog | Each service (decentralized) | Not shared — internal use for Module 7 |

---

## Schema: `auth-service` (MySQL)

> Owner of identity, Driver/Mechanic profiles, and vehicles (SRS Modules 1 and 2).

### Table: `users`

**Purpose:** Authentication credentials and base role. As with the reference auth-service,
it only holds what's needed to authenticate — the business profile (Driver or Mechanic)
lives in separate tables referenced by `user_id`.

| Field | Type | Nullable | Description | Constraints |
|---|---|---|---|---|
| id | UUID | No | Unique identifier | PK |
| email | VARCHAR(255) | No | Email address (SRS RF1.1) | UNIQUE, NOT NULL |
| username | VARCHAR(100) | No | Username (SRS RF1.1) | UNIQUE, NOT NULL |
| document_id | VARCHAR(50) | No | ID document (SRS RF1.1) | UNIQUE, NOT NULL |
| password_hash | VARCHAR(255) | No | Password hash | NOT NULL |
| role | ENUM | No | `DRIVER`, `MECHANIC`, `ADMIN` (SRS RF1.3) | DEFAULT `DRIVER` |
| email_verified | BOOLEAN | No | Email verification flag (SRS RF1.1) | DEFAULT false |
| mfa_enabled | BOOLEAN | No | Second factor enabled (SRS ERF1.3.1) | DEFAULT false |
| failed_attempts | INT | No | Failed login attempts (SRS RF1.2) | DEFAULT 0 |
| locked_until | TIMESTAMP | Yes | Temporary lock after failed attempts | NULL = not locked |
| status | ENUM | No | `ACTIVE`, `DEACTIVATED` (SRS RF1.5/RF1.6) | DEFAULT `ACTIVE` |
| created_at | TIMESTAMP | No | Registration date | DEFAULT NOW() |
| updated_at | TIMESTAMP | No | Last modification (SRS RF1.7) | ON UPDATE NOW() |
| deleted_at | TIMESTAMP | Yes | Soft delete | NULL = active |

**Indexes:**
| Name | Fields | Justification |
|---|---|---|
| idx_users_email | email | Login (SRS RF1.2) |
| idx_users_document_id | document_id | Enforce uniqueness on registration (SRS RF1.1) |

---

### Table: `drivers`

**Purpose:** Extended profile of a user with `DRIVER` role.

| Field | Type | Nullable | Description | Constraints |
|---|---|---|---|---|
| id | UUID | No | PK | PK |
| user_id | UUID | No | FK → `users.id` | UNIQUE, ON DELETE CASCADE |
| created_at | TIMESTAMP | No | Profile creation date | DEFAULT NOW() |
| updated_at | TIMESTAMP | No | Last modification | ON UPDATE NOW() |

**Modeling decision:** Kept intentionally thin for now — the driver has no attributes of
its own beyond `user_id` in the current SRS. It exists to mirror the `mechanics` pattern
and allow growth without migrating `users`.

---

### Table: `mechanics`

**Purpose:** Extended profile of a user with `MECHANIC` role (based on
`02-domain/entities-and-rules.md`, `Mechanic` entity).

| Field | Type | Nullable | Description | Constraints |
|---|---|---|---|---|
| id | UUID | No | PK | PK |
| user_id | UUID | No | FK → `users.id` | UNIQUE, ON DELETE CASCADE |
| workshop_name | VARCHAR(150) | No | Workshop's commercial name (INV-001) | NOT NULL |
| verification_status | ENUM | No | `PENDING`, `VERIFIED`, `REJECTED` (INV-001) | DEFAULT `PENDING` |
| availability_status | ENUM | No | `AVAILABLE`, `BUSY`, `OFFLINE` | DEFAULT `OFFLINE` |
| created_at | TIMESTAMP | No | Registration date | DEFAULT NOW() |
| updated_at | TIMESTAMP | No | Last modification | ON UPDATE NOW() |

**Note:** The live GPS location (`GPSLocation`, INV-002) is **not** stored in MySQL — it
lives in Firebase Realtime Database due to the real-time update requirement (SRS IS4).
MySQL only stores the availability status.

**Indexes:**
| Name | Fields | Justification |
|---|---|---|
| idx_mechanics_availability | availability_status | Matchmaking queries by availability |

---

### Table: `vehicles`

**Purpose:** Vehicles registered by a driver (SRS RF2.1–RF2.5).

| Field | Type | Nullable | Description | Constraints |
|---|---|---|---|---|
| id | UUID | No | PK | PK |
| driver_id | UUID | No | FK → `drivers.id` (SRS RF2.1) | ON DELETE RESTRICT |
| plate | VARCHAR(20) | No | License plate (SRS RF2.1) | UNIQUE, NOT NULL |
| brand | VARCHAR(80) | No | Brand (SRS RF2.1) | NOT NULL |
| model | VARCHAR(80) | No | Model (SRS RF2.1) | NOT NULL |
| type | ENUM | No | Vehicle type (SRS RF2.1) | NOT NULL |
| color | VARCHAR(40) | Yes | Color (SRS ERF2.1.1) | — |
| year | INT | Yes | Year (SRS ERF2.1.1) | — |
| notes | TEXT | Yes | Status notes / particular features (SRS ERF2.1.1) | — |
| status | ENUM | No | `ACTIVE`, `INACTIVE` (SRS RF2.4) | DEFAULT `ACTIVE` |
| created_at | TIMESTAMP | No | Registration date | DEFAULT NOW() |
| updated_at | TIMESTAMP | No | Last modification (SRS RF2.2) | ON UPDATE NOW() |

**Modeling decision:** `status = INACTIVE` instead of a physical delete, because SRS RF2.4
explicitly requires preserving the service history tied to a deactivated vehicle.

**Indexes:**
| Name | Fields | Justification |
|---|---|---|
| idx_vehicles_driver_id | driver_id | List a driver's vehicles |
| idx_vehicles_plate | plate | Enforce uniqueness on registration |

---

## Schema: `service-request` (MySQL)

> Owner of assistance requests (SRS Module 3, `02-domain` "Matchmaking & Dispatch"
> bounded context).

### Table: `service_requests`

**Purpose:** An active roadside-assistance request, from creation to closure.

| Field | Type | Nullable | Description | Constraints |
|---|---|---|---|---|
| id | UUID | No | PK (`requestId`) | PK |
| driver_id | UUID | No | Logical FK → `auth-service.drivers.id` | NOT NULL |
| vehicle_id | UUID | No | Logical FK → `auth-service.vehicles.id` (SRS RF2.3) | NOT NULL |
| mechanic_id | UUID | Yes | Logical FK → `auth-service.mechanics.id` | NULL until matched |
| problem_type | VARCHAR(100) | No | Problem type (SRS RF3.1) | NOT NULL |
| description | TEXT | Yes | Incident description (SRS RF3.1) | — |
| priority | ENUM | No | Priority (SRS RF3.2) | DEFAULT `NORMAL` |
| status | ENUM | No | `PENDING`, `ACCEPTED`, `ON_THE_WAY`, `IN_PROGRESS`, `COMPLETED`, `CANCELLED` (SRS RF3.5) | DEFAULT `PENDING` |
| estimated_cost | DECIMAL(10,2) | Yes | Estimated cost (SRS RF2.5) | — |
| cancellation_reason | TEXT | Yes | Cancellation reason (SRS RF3.4) | NULL if not cancelled |
| requested_at | TIMESTAMP | No | Request timestamp | DEFAULT NOW() |
| updated_at | TIMESTAMP | No | Last modification (SRS RF3.3) | ON UPDATE NOW() |

**Modeling decision:** The `status` enum expands the original one in `02-domain`
(`PENDING/MATCHED/COMPLETED/CANCELLED`) to the 6 states required by SRS RF3.5
(pending, accepted, on the way, in progress, completed, plus cancelled). This change must
also be reflected in `02-domain/entities-and-rules.md` to avoid drift.

**Invariants inherited from `02-domain/entities-and-rules.md`:**
- INV-003: matching must complete within the 3-second SLA (measured in `service-request`,
  not persisted as a field).
- INV-004: cannot move to `COMPLETED` without an associated `Diagnostic` in `service-execution`.

**Indexes:**
| Name | Fields | Justification |
|---|---|---|
| idx_sr_driver_id | driver_id | Driver's history (SRS RF4.3) |
| idx_sr_mechanic_id | mechanic_id | Mechanic's active requests (SRS RF4.1) |
| idx_sr_status | status | Filter by status (SRS RF4.1, ERF4.4.1) |

---

## Schema: `service-execution` (MySQL)

> Owner of service closure and diagnostics ("Service Execution" bounded context).

### Table: `diagnostics`

**Purpose:** Diagnostic and closure record for a request (`ServiceCompleted` event,
INV-004).

| Field | Type | Nullable | Description | Constraints |
|---|---|---|---|---|
| id | UUID | No | PK (`diagnosticId`) | PK |
| request_id | UUID | No | Logical FK → `service-request.service_requests.id` | UNIQUE, NOT NULL |
| mechanic_id | UUID | No | Logical FK → `auth-service.mechanics.id` | NOT NULL |
| findings | TEXT | No | Description of the diagnostic performed | NOT NULL |
| final_status | ENUM | No | Final outcome of the service (SRS RF4.3) | NOT NULL |
| completed_at | TIMESTAMP | No | Completion timestamp (`completionTime`) | DEFAULT NOW() |

**Indexes:**
| Name | Fields | Justification |
|---|---|---|
| idx_diagnostics_request_id | request_id | 1-to-1 lookup against the request (SRS RF4.4) |

---

## Firebase Realtime Database schema

> Real-time sync layer (SRS IS4, IC2). Not a source of truth — it's an ephemeral
> projection of data that lives in MySQL, plus the mechanic's live GPS position.

| Path | Content | Purpose |
|---|---|---|
| `/service_requests/{requestId}/status` | Current service status | Mirrors the MySQL `status` field in real time (SRS RF3.5) |
| `/mechanics/{mechanicId}/location` | `{lat, lng, updatedAt}` | Mechanic's live location, 15 m accuracy (INV-002, SRS RF4.2) |
| `/notifications/{userId}` | Queue of unread notifications | Real-time notifications (SRS RF4.5) |

---

## Auditing (SRS Module 7) — pending decision

SRS Module 7 requires querying audit logs by date range, user, action type, module, and
operation status. Currently **no table or service exists** for this. Two options for the
team to decide (not assumed here):

1. **Decentralized:** each service adds its own `audit_logs` table with
   `user_id, action, module, status, created_at`.
2. **Centralized:** a new `audit-service` with its own database, fed by events from the
   other services.

This decision should be recorded as an ADR before this section can be considered complete.

---

## Migrations

**Tool:** to be chosen per the team's stack — see `_stacks/[stack].md`.
**Naming convention:** `V{version}__{description}.sql`
**Policy:** migrations are forward-only; incompatible changes use a 2-phase approach
(example below).

**Compatible schema changes (non-breaking):**
```sql
ALTER TABLE vehicles ADD COLUMN notes TEXT;
ALTER TABLE service_requests ADD COLUMN priority VARCHAR(20) NOT NULL DEFAULT 'NORMAL';
CREATE INDEX idx_sr_status ON service_requests (status);
```

**Incompatible changes (require 2-phase migration):**
```sql
-- Phase 1 (release N): add new column, copy data
ALTER TABLE service_requests ADD COLUMN delivery_date TIMESTAMP;
UPDATE service_requests SET delivery_date = fecha_entrega;

-- Phase 2 (release N+1): drop old column
ALTER TABLE service_requests DROP COLUMN fecha_entrega;
```

---

## Open items for the team

- [ ] Document the hybrid MySQL + Firebase decision in an ADR.
- [ ] Update `02-domain/domain-map.md`: it currently lists a mixed MySQL/Firebase setup per
      context without specifics — align it with this document.
- [ ] Update `02-domain/entities-and-rules.md` with the expanded `ServiceRequest.status`
      enum (6 states instead of 4) and add the `Driver`, `Vehicle`, and `Diagnostic`
      entities, which currently only appear here.
- [ ] Decide the auditing model (SRS Module 7) — centralized vs. decentralized.
- [ ] Define the ER diagram (Mermaid) per service.

---

## Correlations

- Domain entities mapping to these tables → `02-domain/entities-and-rules.md`
- Bounded contexts and ownership → `02-domain/domain-map.md`
- Domain events (`DriverRegistered`, `ServiceRequested`, `MechanicMatched`, `ServiceCompleted`) → `02-domain/domain-events.md`
- Per-service detail → `09-microservices/services/XX/data-model.md`
- API contracts → `07-api/contracts/openapi/`
- Source SRS → Modules 1, 2, 3, and 7
