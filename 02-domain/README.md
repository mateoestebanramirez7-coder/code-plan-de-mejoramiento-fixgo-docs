# 02 — Problem Domain (FIXGO)

> **What is this?** The mental model of the FIXGO business[cite: 10]. It captures the core problem the system solves before writing any code, following Domain-Driven Design (DDD) principles[cite: 10].

## Why this section exists

The most costly mistakes in software are domain misunderstandings, not coding bugs[cite: 10]. For FIXGO, if developers do not deeply understand how roadside assistance, real-time GPS tracking, and mechanic matchmaking work, the architecture fails. 

This section captures domain knowledge beforehand so that:
1. **Entities and invariants** match real-world constraints (like the strict 3-second SLA).
2. **Microservice boundaries** are drawn correctly based on clear Bounded Contexts.
3. **Ubiquitous language** is shared equally among developers, product owners, and business stakeholders.

---

## What is inside this folder

### `domain-map.md` ⭐
The central DDD artifact defining the system's boundaries[cite: 7].
* **FIXGO Bounded Contexts:** 
  - **User Management:** Handles registrations, roles, profiles, and Firebase authentication for drivers and mechanics[cite: 10].
  - **Matchmaking & Dispatch:** Handles real-time geolocation tracking, 15-meter accuracy, and the algorithmic pairing of requests[cite: 10].
  - **Service Execution:** Manages on-site repair lifecycles, diagnostic updates, and service completions[cite: 10].

### `entities-and-rules.md` ⭐
The catalog of tactical building blocks.
* **Key Entities & Aggregates:** `Mechanic`, `ServiceRequest`, and Value Objects like `GPSLocation` and `Money`[cite: 10].
* **Business Invariants (Rules):** Constraints that must never be violated, such as maintaining real-time coordinates and enforcing the 3s SLA benchmark[cite: 10].

### `domain-events.md` ⭐
The backbone of asynchronous communication between contexts[cite: 9].
* **Key Events (Past Tense):** `DriverRegistered`, `ServiceRequested`, `MechanicMatched`, and `ServiceCompleted`[cite: 10].

---

## Quick Navigation Index

| Document | Purpose | Phase |
|---|---|---|
| [`domain-map.md`](./domain-map.md) | Bounded contexts, context map, and core domain classification[cite: 7, 10] | Discovery |
| [`entities-and-rules.md`](./entities-and-rules.md) | Tactical entities, value objects, invariants, and behaviors[cite: 8, 10] | Discovery |
| [`domain-events.md`](./domain-events.md) | Immutable facts, payloads, event catalog, and policies[cite: 9, 10] | Discovery |
