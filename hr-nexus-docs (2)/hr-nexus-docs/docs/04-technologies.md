# 🛠️ Technology Stack

| Layer | Technology | Why |
|---|---|---|
| Frontend framework | React + TypeScript + Vite | Type safety for a large, long-lived codebase with many roles/permissions; Vite for fast dev builds. |
| Styling/components | Tailwind CSS + shadcn/ui | Consistent design tokens, accessible primitives out of the box, avoids bespoke CSS sprawl. |
| Routing | React Router | Standard, supports nested/protected routes needed for role-based portals. |
| Server state | TanStack Query | Caching, refetching, and loading/error states for every API-backed screen. |
| Client state | Zustand | Lightweight store for UI/session state (current role, sidebar state) without Redux boilerplate. |
| Charts | Recharts | Declarative, composable, integrates cleanly with React + TS. |
| Backend framework | Django + DRF | Batteries-included ORM, admin, auth scaffolding, and DRF's serializer/permission model maps directly onto RBAC + object-level authorization needs. |
| Database | MySQL | Mandated; mature, widely operable, strong relational guarantees. No PostgreSQL, no MongoDB, no second database for the same core data. |
| Cache/queue | Redis | Backing store for Celery broker/result backend and general caching (e.g., dashboard aggregates). |
| Background jobs | Celery | Async/scheduled work (payslips, reports, reminders) off the request/response cycle. |
| Analytics | Python + Pandas | Aggregation/statistics computed server-side from real MySQL data, kept separate from CRUD views. |
| API docs | OpenAPI + Swagger UI (drf-spectacular) | Self-documenting, versionable contract for frontend/backend collaboration. |
| Auth | JWT + HttpOnly cookies + RBAC | Stateless auth, XSS-resistant storage, explicit role/permission model. |
| Deployment | Docker, Docker Compose, Nginx, Gunicorn | Reproducible environments, one-command startup, production-grade serving. |
| CI/CD | GitHub Actions | Native to the GitHub-based VCS workflow. |
| Testing | Pytest/pytest-django, Vitest/RTL | Standard, well-supported per stack choice. |
| Monitoring | Sentry + structured logging | Error visibility and auditability in production. |

## Explicit non-goals

To keep the architecture disciplined, the stack deliberately avoids:

- A Node.js or Flask backend (Django/DRF only)
- MongoDB or PostgreSQL (MySQL only, for one relational source of truth)
- Multiple databases for the same core HR data
- Microservices — until an actual scaling need proves the boundary is worth the operational cost

⬅️ [Back to root README](../README.md)
