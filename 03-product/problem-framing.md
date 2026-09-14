# Problem Framing — Problem Definition

> **Why this document exists:** Before designing solutions, the team must be aligned on the problem it solves. This document captures that alignment[cite: 11].

---

## 1. The problem in one sentence

**Stranded drivers experiencing unexpected vehicle breakdowns on the road** who **need immediate mechanical support** struggle with **severe delays, lack of reliable real-time tracking, and inefficient manual dispatching** because **current alternatives rely on random unverified phone calls and directory listings**, resulting in **wasted time, financial loss, and severe frustration**[cite: 10, 12].

---

## 2. Affected users

| Segment | Description | Estimated size | Priority |
|---------|-------------|---------------|---------|
| **Stranded Drivers** | Vehicle owners facing unexpected mechanical failures on highways or urban roads[cite: 12]. | High | High |
| **Local Mechanics / Workshops** | Independent mechanics and workshop operators seeking reliable client acquisition[cite: 12]. | Medium | High |

### Jobs-to-be-done (JTBD)

**When** my vehicle breaks down unexpectedly on the road[cite: 11],
**I want** to instantly locate, contact, and dispatch the nearest verified mechanic with real-time tracking[cite: 11],
**so that** I can resume my journey safely and minimize downtime[cite: 11].

---

## 3. Evidence of the problem

| Evidence type | Source | Date | Key finding |
|--------------|--------|------|------------|
| Direct observation | Field research in local transit routes[cite: 10] | 2026[cite: 10] | Average wait time for manual roadside help exceeds 45 minutes without tracking. |
| Benchmarking | Market analysis of local directory listings[cite: 10] | 2026[cite: 10] | No centralized real-time platform exists for immediate local mechanical dispatching. |

---

## 4. Current user solution (and its problems)

| Current solution | Limitations | Cost/Friction |
|-----------------|------------|--------------|
| Random phone calls to directory listings[cite: 12] | Slow, unverified availability, no GPS tracking[cite: 12] | High time consumption and uncertainty[cite: 12] |

---

## 5. Solution hypothesis

**We believe that** an automated real-time geolocation matching platform (FIXGO)[cite: 10, 12]
**for** stranded drivers and local mechanics[cite: 12],
**will achieve** instant dispatching under a 3-second SLA response target[cite: 12].
**We will know we succeeded when** matching success rate reaches over 95% within the SLA threshold.

---

## 6. Success metrics (North Star)

| Metric | Current baseline | 6-month target | How to measure it |
|--------|----------------|---------------|-------------------|
| **Average Match Time** | 45 minutes | < 3 seconds[cite: 12] | System timestamp logs |
| **User Satisfaction** | Low (fragmented) | > 90% positive | Post-service rating app |

**North Star Metric:** Successful mechanic assignments completed within the 3-second SLA timeframe[cite: 12].
