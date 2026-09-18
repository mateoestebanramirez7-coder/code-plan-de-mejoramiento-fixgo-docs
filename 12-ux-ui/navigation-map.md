# Navigation Map

> Defines the screen structure of the system, how screens connect to each other, and what routes
> exist. It is the reference when frontend and backend discuss what endpoints exist
> or how to reach a feature.

---

## Frontend route structure

```
/                           → Home / landing page
├── /auth
│   ├── /login              → Authentication form
│   ├── /register           → New user registration (Driver or Mechanic)
│   └── /forgot-password    → Password recovery
│
├── /dashboard               → Main panel (authenticated)
│   ├── /overview           → Summary and key metrics
│   └── /notifications      → Notification center (dispatch alerts)
│
├── /requests                → Driver: service request list
│   ├── /new                → New breakdown request (GPS + failure type)
│   └── /:id
│       ├── /               → Request detail with live mechanic tracking
│       └── /cancel          → Cancel active request
│
├── /dispatch                 → Mechanic: incoming request queue
│   └── /:id                → Accept/reject a dispatched request, update status
│
├── /admin                  → Administration panel (role: ADMIN)
│   ├── /verifications      → Mechanic credential verification queue
│   ├── /sla-monitoring     → SLA metrics console (3-second matching benchmark)
│   └── /users              → Driver/Mechanic account management
│
└── /profile                → Authenticated user's profile
```

---

## Screen map

| Screen | Route | Component | Minimum role | Backend service |
|--------|-------|-----------|--------------|----------------|
| Home | `/` | `HomePage` | Public | — |
| Login | `/auth/login` | `LoginPage` | Public | auth-service |
| Register | `/auth/register` | `RegisterPage` | Public | auth-service |
| Dashboard | `/dashboard` | `DashboardPage` | Driver / Mechanic | dispatch-service |
| Request list | `/requests` | `RequestListPage` | Driver | dispatch-service |
| New request | `/requests/new` | `RequestFormPage` | Driver | dispatch-service |
| Request detail (live tracking) | `/requests/:id` | `RequestDetailPage` | Driver | dispatch-service, tracking-service |
| Dispatch queue | `/dispatch` | `DispatchQueuePage` | Mechanic | dispatch-service |
| Dispatch detail | `/dispatch/:id` | `DispatchDetailPage` | Mechanic | dispatch-service |
| Verification queue | `/admin/verifications` | `VerificationQueuePage` | Administrator | auth-service |
| SLA monitoring console | `/admin/sla-monitoring` | `SlaMonitoringPage` | Administrator | dispatch-service |
| Admin panel | `/admin` | `AdminDashboard` | Administrator | auth-service |

---

## Main user flows

### Flow 1 — Roadside assistance request (Driver)

```
Dashboard (/dashboard)
    │
    ▼ Driver taps "Request assistance"
New request (/requests/new)
    │
    ├── GPS + failure type submitted ──► Request detail (/requests/:id)
    │                                     shows live-tracked matched mechanic
    │
    └── No mechanic available in range ► Error screen: "No mechanics nearby,
                                          try again in a few minutes"
```

**Related HUs:** HU-DISPATCH-001, HU-DISPATCH-002

### Flow 2 — Dispatch acceptance (Mechanic)

```
Dispatch queue (/dispatch)
    │
    ▼ Mechanic taps an incoming request
Dispatch detail (/dispatch/:id)
    │
    ├── Accept ──► Status updates to "En route", driver sees live tracking
    │
    └── Reject ─► Request returns to matching pool for next nearest mechanic
```

**Related HUs:** HU-DISPATCH-003, HU-DISPATCH-004

### Flow 3 — Authentication

```
Landing (/)
    │
    ▼ Click "Sign in"
Login (/auth/login)
    │
    ├── Valid credentials ──► Dashboard (/dashboard)
    │
    └── Invalid credentials ► Login with error message (max. 5 attempts)
```

**Related HUs:** HU-AUTH-001, HU-AUTH-002

---

## Navigation rules

| Rule | Description |
|------|-------------|
| Authentication | Routes under `/dashboard`, `/requests`, `/dispatch`, `/admin` redirect to `/auth/login` if no session |
| Authorization | Routes under `/dispatch` require Mechanic role; routes under `/admin` require Administrator role — otherwise redirect to `/dashboard` |
| 404 | Undefined routes show the 404 screen with a link to dashboard |
| Confirmation | Cancelling an active request or rejecting a dispatch shows a confirmation dialog before executing |

---

## Correlations

- Design system (visual components) → `12-ux-ui/design-system.md`
- Wireframes → `12-ux-ui/wireframes/` (if applicable)
- Frontend API contracts → `07-api/contracts/openapi/`
- Roles and permissions → `00-governance/security-policy.md`
