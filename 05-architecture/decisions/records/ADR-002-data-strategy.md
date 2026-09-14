# ADR-002 — Real-Time Geolocation and Relational Persistence Strategy

| Field | Value |
|---|---|
| ID | ADR-002 |
| Date | 2026-09-11 |
| Status | Accepted |
| Authors | Johan — Tech Lead |
| Reviewers | Gabriel Tijaro — Mateo Ramirez — Development team |

---

## Context

FixGo requires sub-second GPS coordinate synchronization to satisfy the sub-3-second SLA for roadside emergency matchmaking, alongside robust storage with AES-256 encryption at rest for user credentials, vehicle profiles, and service history. Failing to establish a clear persistence architecture risks database connection exhaustion during high-concurrency dispatch events and potential latency violations on mobile networks.

**Known constraints:**
- Dispatch response latency must remain under 3 seconds (SLA target).
- User identity, transaction history, and credentials require ACID compliance and AES-256 encryption at rest.
- Mobile client battery and bandwidth consumption must be minimized during live vehicle tracking.

---

## Decision

**We decided:** Implement a hybrid persistence strategy using Firebase Realtime Database for active geolocation and ephemeral dispatch events, combined with a relational SQL database utilizing AES-256 encryption at rest for transactional data and account records.

**Justification:**
Firebase Realtime Database handles high-frequency location streams without relational row locking or connection pool exhaustion. Relational SQL provides strong consistency, complex queries, and data integrity guarantees for critical profile, billing, and audit records.

---

## Evaluated alternatives

| Alternative | Pros | Cons | Reason for discarding |
|------------|------|------|-----------------------|
| Hybrid: Firebase RTDB + SQL (chosen) | Meets sub-3s SLA, isolates dynamic GPS traffic, enforces ACID on sensitive records | Dual storage systems to orchestrate | — (chosen) |
| Single Relational SQL with Polling | Single database engine, simple backup routines | High connection saturation, excessive mobile battery drain, latency spikes > 3s | Fails the real-time matchmaking SLA |
| Pure NoSQL (MongoDB / Firestore) | Flexible document schema, rapid initial setup | Weaker relational constraints, complex distributed transactions for billing records | Insufficient transactional integrity for financial and audit logs |

---

## Consequences

**Positive:**
- Consistently satisfies the sub-3-second emergency dispatch latency target.
- Protects client identity and vehicle data under enterprise-grade AES-256 encryption.
- Reduces database connection load on internal containerized services.

**Negative / Trade-offs:**
- Backend services must maintain dual repository adapters and handle cross-database synchronization logic.
- Cloud dependency on Google Firebase availability for live tracking features.

**Impact on the system:**
- Affected services: `dispatch-service`, `auth-service`, mobile client application.
- Documents that must be updated: `05-architecture/overview.md`, `05-architecture/deployment.md`, `06-data/models.md`.

---

## Risks

| Risk | Probability | Impact | Mitigation |
|------|------------|--------|-----------|
| Firebase service disruption or network timeout | Low | High | Implement local cache on mobile clients and retry fallback via API Gateway |
| Data inconsistency between ephemeral state and SQL records | Medium | Medium | Persist completed dispatch events into relational SQL as immutable audit logs once tickets close |

---

## References

- Deployment Infrastructure Guide → `05-architecture/deployment.md`
- System Architecture Overview → `05-architecture/overview.md`
- Related to: ADR-001 (Documentation Language), ADR-002 (Authentication Strategy)
