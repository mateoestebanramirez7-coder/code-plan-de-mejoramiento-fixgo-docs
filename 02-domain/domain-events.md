# Domain Events in FIXGO

This document registers all immutable facts that occur in the business domain, written in the past tense, driving asynchronous event-driven communication between bounded contexts.

---

## Event Catalog

### Event: DriverRegistered[cite: 10]
* **Triggered by:** Driver completes account sign-up via Firebase authentication[cite: 10].
* **Data:** `driverId`, `email`, `timestamp`[cite: 10]
* **Consumers:** Matchmaking Context, Notification Service[cite: 10]
* **Bounded Context:** User Management[cite: 10]

### Event: ServiceRequested[cite: 10]
* **Triggered by:** Driver triggers an emergency breakdown request with GPS coordinates[cite: 10].
* **Data:** `requestId`, `driverId`, `location`, `timestamp`[cite: 10]
* **Consumers:** Matchmaking & Dispatch Context[cite: 10]
* **Bounded Context:** Matchmaking & Dispatch[cite: 10]

### Event: MechanicMatched[cite: 10]
* **Triggered by:** Algorithm successfully pairs a service request with the nearest workshop within the 3s SLA[cite: 10].
* **Data:** `requestId`, `mechanicId`, `eta`, `timestamp`[cite: 10]
* **Consumers:** Service Execution Context, Driver UI[cite: 10]
* **Bounded Context:** Matchmaking & Dispatch[cite: 10]

### Event: ServiceCompleted[cite: 10]
* **Triggered by:** Mechanic finishes on-site repair and submits final diagnostic record[cite: 10].
* **Data:** `requestId`, `diagnosticId`, `completionTime`[cite: 10]
* **Consumers:** Billing/History Logs, User Management[cite: 10]
* **Bounded Context:** Service Execution[cite: 10]

---

## Event Summary Table

| Event | Origin Context | Topic | Consumers | Version |
|-------|---------------|-------|-----------|---------|
| DriverRegistered | User Management | `fixgo.users.driver-registered` | Matchmaking, Notifications | v1 |
| ServiceRequested | Matchmaking & Dispatch | `fixgo.services.requested` | Matchmaking & Dispatch | v1 |
| MechanicMatched | Matchmaking & Dispatch | `fixgo.services.mechanic-matched` | Service Execution, UI | v1 |
| ServiceCompleted | Service Execution | `fixgo.services.completed` | History Logs, Billing | v1 |
