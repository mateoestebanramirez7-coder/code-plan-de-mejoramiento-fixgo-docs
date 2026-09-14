# Domain Map — Bounded Contexts

## 1. Domain overview

FIXGO is a real-time roadside assistance platform designed to instantly connect drivers experiencing vehicle breakdowns with nearby mechanics and workshops. The system manages the entire lifecycle of emergency assistance, from precise GPS location capture and automated mechanic matching under a strict 3-second SLA, to on-site service execution and secure encrypted data handling.

---

## 2. Identified Bounded Contexts

### Bounded Context: User Management

| Field | Value |
|-------|-------|
| **Name** | User Management |
| **Responsibility** | Manages user registrations, role definitions, profiles, and Firebase authentication for both drivers and mechanics. |
| **Owning team** | Johan Andrés Liñan Esquivel, Juan David Romero Calderon, Gabriel Tijaro Jimenez, and Mateo Esteban Ramirez Garzon[cite: 10] |
| **Microservice(s)** | `auth-service` |
| **Database** | MySQL |
| **Ubiquitous Language** | Driver, Mechanic, UserProfile, Credentials, Role |

**Context-specific terms (Ubiquitous Language):**

| Term | Meaning in THIS context | Different in another context? |
|------|------------------------|-------------------------------|
| User | Registered entity with active authentication credentials | Yes — in Dispatch it is referenced as "Driver" or "Mechanic" |
| Profile | Personal details and contact data | No |

---

### Bounded Context: Matchmaking & Dispatch

| Field | Value |
|-------|-------|
| **Name** | Matchmaking & Dispatch |
| **Responsibility** | Handles real-time geolocation tracking, 15-meter accuracy calculations, and the algorithmic pairing of driver breakdown requests with nearby workshops[cite: 10]. |
| **Owning team** | Juan David Romero Calderon, Johan Andrés Liñan Esquivel, Gabriel Tijaro Jimenez, and Mateo Esteban Ramirez Garzon[cite: 10] |
| **Microservice(s)** | `service-request`, `geolocation-service` |
| **Database** | MySQL & Firebase |
| **Ubiquitous Language** | ServiceRequest, GPSLocation, MatchResult, WorkshopAvailability |

**Context-specific terms (Ubiquitous Language):**

| Term | Meaning in THIS context | Different in another context? |
|------|------------------------|-------------------------------|
| Request | An active cry for mechanical help with coordinates | Yes — in Billing it's an invoice item |

---

### Bounded Context: Service Execution

| Field | Value |
|-------|-------|
| **Name** | Service Execution |
| **Responsibility** | Manages the lifecycle of the on-site repair, including diagnostic updates, service confirmation, and status tracking[cite: 10]. |
| **Owning team** | Gabriel Tijaro Jimenez, Johan Andrés Liñan Esquivel, Juan David Romero Calderon, and Mateo Esteban Ramirez Garzon[cite: 10] |
| **Microservice(s)** | `service-execution` |
| **Database** | MySQL |
| **Ubiquitous Language** | Diagnostic, ServiceSession, RepairStatus, CompletionRecord |

---

## 3. Context Map

```text
┌─────────────────────┐        ┌──────────────────────────────┐
│  User Management    │        │  Matchmaking & Dispatch      │
│                     │──────▶│                              │
│  Domain:            │  U→D   │  Domain:                     │
│  Profiles & Auth    │        │  GPS Matching & SLA (3s)     │
└─────────────────────┘        └──────────────┬───────────────┘
                                              │
                                              │ U→D
                                              ▼
                               ┌──────────────────────────────┐
                               │  Service Execution           │
                               │                              │
                               │  Domain:                     │
                               │  On-site repair & Diagnostic │
                               └──────────────────────────────┘
```

### Relationships table

| Context A | Relationship | Context B | Communication channel | Contract |
|-----------|-------------|-----------|----------------------|---------|
| User Management | U → D | Matchmaking & Dispatch | REST / Event | OpenAPI / AsyncAPI |
| Matchmaking & Dispatch | U → D | Service Execution | REST / Event | OpenAPI / AsyncAPI |

---

## 4. Core Domain, Supporting, Generic

| Bounded Context | Type | Justification |
|----------------|------|---------------|
| Matchmaking & Dispatch | Core | The algorithmic real-time pairing and 15-meter accuracy engine is FIXGO's main competitive advantage. |
| Service Execution | Supporting | Necessary to complete the assistance flow but supports the core matching mechanism. |
| User Management | Generic | Standard authentication and profile management using Firebase and MySQL. |
