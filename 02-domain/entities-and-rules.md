# Entities, Value Objects, and Business Rules in FIXGO

## 1. System Entities

### Entity: Mechanic[cite: 10]

**Context:** User Management[cite: 10]

**Description:** Represents a registered technical expert or workshop owner available to provide roadside assistance.

**Attributes:**

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| `mechanicId` | UUID | Unique identifier for the mechanic[cite: 10] | Yes | Must be generated automatically[cite: 10] |
| `workshopName` | String | Commercial name of the workshop[cite: 10] | Yes | Cannot be empty[cite: 10] |
| `status` | Enum | Availability status (AVAILABLE, BUSY, OFFLINE)[cite: 10] | Yes | Defaults to OFFLINE[cite: 10] |
| `location` | GPSLocation | Current coordinates of the mechanic[cite: 10] | Yes | Must maintain 15-meter accuracy[cite: 10] |

**Invariants (Business rules):**
- INV-001: A mechanic must pass system verification before accepting service requests[cite: 10].
- INV-002: The location coordinate must be updated dynamically to support real-time matching[cite: 10].

---

### Entity: ServiceRequest[cite: 10]

**Context:** Matchmaking & Dispatch[cite: 10]

**Description:** Represents an active vehicle breakdown assistance ticket created by a driver.

**Attributes:**

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| `requestId` | UUID | Unique transaction identifier[cite: 10] | Yes | Must be unique[cite: 10] |
| `driverId` | UUID | Identifier of the driver requesting help[cite: 10] | Yes | Must reference a valid Driver[cite: 10] |
| `mechanicId` | UUID | Identifier of the assigned mechanic[cite: 10] | No | Assigned after matchmaking[cite: 10] |
| `status` | Enum | Request state (PENDING, MATCHED, COMPLETED, CANCELLED)[cite: 10] | Yes | Must follow strict lifecycle order[cite: 10] |

**Invariants (Business rules):**
- INV-003: The matching process must complete within the strict 3-second SLA benchmark[cite: 10].
- INV-004: A service request cannot be marked as completed without a registered diagnostic record[cite: 10].

---

## 2. Value Objects

### Value Object: GPSLocation
* **Description:** Represents geographic coordinates (latitude and longitude) with high precision.
* **Validation rules:** Latitude must be between -90 and 90; longitude must be between -180 and 180.

### Value Object: Money
* **Description:** Represents currency values for service fees.
* **Validation rules:** Amount must be greater than or equal to 0.
