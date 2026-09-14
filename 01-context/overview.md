# System Overview

> **Instructions:** Replace this content with your project's description.
> This is the first page someone new reads. They must be able to understand the system in 5 minutes.
> Remove these instructions when the document is complete.

---

## What is FixGo?

FixGo is a mobile roadside assistance platform that instantly connects stranded drivers with nearby verified mechanics for on-site vehicle repairs. The system bridges the gap between drivers needing immediate mechanical diagnostics and local workshops ready to dispatch help, minimizing roadside wait times through automated location-based matching.

## Problem it solves

**Before the system:** Stranded drivers had to manually search the internet or place random phone calls to find available workshops, often waiting hours for traditional towing services with zero visibility on cost, availability, or arrival time. Independent mechanics lost revenue due to a lack of digital dispatch channels.

**With the system:** [How the process improves. What value it delivers.]

## Main users

| Role | Description | What they do in the system |
| :--- | :--- | :--- |
| **Driver** | Vehicle operator facing an unexpected breakdown or maintenance issue. | Requests roadside assistance, broadcasts live GPS coordinates, views matched mechanic status, and confirms job completion. |
| **Mechanic** | Certified automotive professional or registered workshop technician. | Receives dispatch notifications, reviews service requests, navigates to the breakdown location, and logs diagnostic and repair updates. |
| **Administrator** | System operator supervising platform health and verification. | Audits provider credentials, oversees SLA metrics via the monitoring console, and ensures overall data compliance. |

## Technology stack

| Layer | Technology | Justification |
| :--- | :--- | :--- |
| **Frontend** | Flutter / Android Mobile | Provides cross-platform native performance and native access to mobile GPS geolocation services. |
| **Backend** | Java (Maven / Spring Boot) | Delivers robust, enterprise-grade architecture for handling business rules, dispatch logic, and API endpoints. |
| **Database** | Firebase Realtime Database & SQL | Enables instant bidirectional data sync for tracking and matchmaking, backed by relational data persistence. |
| **Message broker** | Firebase Cloud Messaging  / Kafka | Ensures instantaneous push notifications and event-driven updates between drivers and mechanics. |
| **Infrastructure** | Cloud-based hosting with AES-256 | Ensures continuous service availability, strict p95 latency control, and robust data encryption at rest. |

## Current status

- **Phase:** In development MVP
- **Current version:** v0.1.0
- **Last release:** September 2026
- **Next milestone:** Complete end-to-end GPS matchmaking and Firebase authentication integration October 2026.

## Project contacts

|     Role       | Name               | Contact                  |
| :------------- | :----------------- | :-------------------------|
| **Tech Lead** | Johan Andres Liñan | [johan117herli@gmail.com] |
| **Product Owner** | SENA | owner.email@domain.com |
| **DevOps / Backend** | Gabriel Tijaro | [tijarojimenezgabriel@gmail.com] |
| **Frontend / QA** | Juan David Romero | [juanchoromerocalderon2022@gmail.com] |
