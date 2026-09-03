# 🏗️ System Architecture

## Overview

HR Nexus is built as a **modular monolith**: one Django backend composed of clearly bounded apps, one React SPA, one MySQL database.

This is deliberate. An HR platform's modules (employees, attendance, leave, payroll, etc.) share the same core entities — `User`, `Employee`, `Department` — constantly. Splitting these into microservices early would mean either duplicating that core data across services or paying constant network-call/consistency overhead for what are fundamentally transactional, relational operations.

A modular monolith gets us:

- **Single source of truth** in one MySQL instance → strong referential integrity via real foreign keys, not eventual consistency.
- **One deployable unit** → simpler CI/CD, simpler local dev (`docker compose up`), fewer moving parts to secure and monitor.
- # **Clean app boundaries inside Django** → if a specific module (analytics, recruitment) later needs independent scaling, it can be extracted because the boundary already exists in code.

<<<<<<< HEAD
## Request Flowsssssssssssssssss
=======
## Request Flow
>>>>>>> 477464b (spell correction)

> > > > > > > b4265d8 (change)

```
Browser (React SPA)
   │  HTTPS
   ▼
Nginx (reverse proxy, TLS termination, static files, gzip)
   │
   ▼
Gunicorn (WSGI workers)
   │
   ▼
Django REST Framework
   ├── Auth middleware (JWT from HttpOnly cookie)
   ├── Permission classes (role + object-level)
   ├── App: employees / attendance / leave / recruitment / payroll / ...
   ▼
MySQL (primary datastore)

Django ──► Redis ──► Celery workers ──► background jobs (email, payslip gen, scheduled reports)

MySQL ──► Pandas extraction layer ──► Analytics endpoints ──► React charts (Recharts)
```

## Why this shape, explicitly

| Decision                                             | Reasoning                                                                                                                                                                                                        |
| ---------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Modular monolith over microservices                  | Core HR data is highly relational and shared across every module; microservices would force distributed transactions for basic workflows like "create employee → create leave balance → create payroll profile." |
| MySQL only                                           | One relational engine, strong FK/constraint support, mature Django ORM support, avoids the operational cost of running two database technologies for the same domain.                                            |
| JWT in HttpOnly cookies (not localStorage)           | Removes JWT from JS-accessible storage, closing the most common XSS-to-token-theft path, while still allowing stateless auth on the API.                                                                         |
| Redis + Celery                                       | Anything slow, bulk, or schedulable (payslip PDF generation, scheduled reports, reminder emails) is offloaded so API requests stay fast and predictable.                                                         |
| Separate analytics layer (Pandas, read-only queries) | Keeps CRUD write-paths simple and un-cluttered by aggregation logic; analytics failures/slowness can never block core HR operations.                                                                             |
| Nginx + Gunicorn, not `runserver`                    | Django's dev server is single-threaded and not hardened for production traffic or TLS.                                                                                                                           |

⬅️ [Back to root README](../README.md)
