# System Scope

> **Why this document exists:** Scope prevents scope creep and aligns expectations.
> It is equally important to define what the system does NOT do as what it does.
> Review this document at the start of each planning cycle.

---

## In Scope

What the system **DOES build and maintain**:

### MVP Features

| # | Feature | Description | Responsible service |
|---|---------|-------------|---------------------|
| 1 | User Registration & Auth | Secure login for Drivers and Mechanics using Firebase. | User Management |
| 2 | Real-time Geolocation | Live GPS tracking to pinpoint stranded drivers and mechanics. | Matchmaking & Services |
| 3 | Matchmaking & Dispatch | Algorithm to pair breakdown requests with the nearest available workshop. | Matchmaking & Services |
| 4 | Status Tracking | In-app updates detailing the mechanic's ETA and service completion. | Monitoring & Tracking |

### Included integrations

| External system | Integration type | Purpose |
|----------------|-----------------|---------|
| Google Maps API | REST API / SDK | Rendering maps, geocoding coordinates, and calculating routes. |
| Firebase | SDK | Authentication, real-time database updates, and cloud messaging. |

### Environments being built

| Environment | Purpose |
|-------------|---------|
| Local | Development on the developer's machine |
| Development (dev) | Continuous integration and development testing |
| Staging | Pre-production, PO acceptance testing |
| Production | Production environment |

---

## Out of Scope

What the system **does NOT build** in this version and why:

| # | What is out of scope | Reason | Future version? |
|---|---------------------|--------|----------------|
| 1 | In-app Online Payments | Out of MVP budget and scope; transactions will be handled in cash locally. | Yes — V2.0 |
| 2 | Spare Parts E-commerce | Outside the core domain of emergency dispatching. | No |
| 3 | Insurance Integrations | Providers lack standard public APIs; requires heavy legal compliance. | Pending provider |
| 4 | Voice Assistant Support | Too complex for initial 3-second SLA response benchmark. | N/A |

### What another system / team handles (and why not us)

| Feature | Who builds it | Why not us |
|---------|--------------|-----------|
| Hardware Diagnostics (OBD2) | Third-party scanners | We are a software matchmaking platform, not diagnostic hardware manufacturers. |
| Background Checks | External verification agency | Liability and legal compliance outside our academic/technical scope. |

---

## Scope assumptions

> These assumptions are taken to be true. If they change, the scope must be renegotiated.

| # | Assumption | Consequence if false |
|---|-----------|---------------------|
| 1 | Users have mobile devices with active GPS and internet connections | The system cannot perform real-time matchmaking or tracking. |
| 2 | Google Maps API remains accessible within free-tier limits | We would have to migrate to Mapbox or OSM, impacting the schedule. |
| 3 | The initial launch targets the Neiva region | Geographic routing and mechanic onboarding strategies would need to expand. |

---

## Constraints

| Type | Description |
|------|-------------|
| **Time** | MVP must be ready by the end of the current SENA academic term. |
| **Budget** | $0 infrastructure budget; must rely on Firebase and Google Cloud free tiers. |
| **Technology** | Must use the agreed stack: Java, Firebase, and Android 8.0+ compatibility. |
| **Regulatory** | Must comply with basic data privacy laws regarding user location tracking. |
| **Team** | 4 developers available (Johan, Juan David, Gabriel, and Mateo). |

---

## External dependencies

| Dependency | Team / Provider | Required date | Status |
|-----------|----------------|--------------|--------|
| Google Maps API Keys | Google Cloud Platform | Sprint 1 | 🟢 Available |
| Firebase Project Setup | Google Firebase | Sprint 1 | 🟢 Available |
| Test mobile devices | Development Team | Sprint 2 | 🟡 In progress |

---

## How to update the scope

The scope can change, but the change has a process:

1. Document the proposed change in this file
2. Evaluate the impact on schedule and effort
3. Obtain approval from the Product Owner and Tech Lead
4. Update the roadmap in `03-product/vision.md`
5. Create or update HUs in `04-requirements/user-stories.md`

---

## Correlations

- Vision and roadmap → `03-product/vision.md`
- Term glossary → `01-context/glossary.md`
- System overview → `01-context/overview.md`
- Scope-related risks → `15-project-control/risks.md`
