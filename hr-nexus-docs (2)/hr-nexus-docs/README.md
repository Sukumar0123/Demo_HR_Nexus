# 🏢 HR Nexus — Phase 1 Technical Documentation

**HR Nexus** is a centralized HR management and workforce analytics platform. This documentation set covers the Phase 1 architecture-only deliverable — no application code, just the design that's been approved before Phase 2 (Foundation) begins.

---

## 📖 Documentation Index

| # | File | What's inside |
|---|---|---|
| 1 | [Overview](docs/01-overview.md) | What HR Nexus is, its purpose, who uses it, design principle |
| 2 | [System Architecture](docs/02-architecture.md) | Modular monolith reasoning, request flow, key architectural decisions |
| 3 | [User Roles & Permissions](docs/03-roles-and-permissions.md) | Full permission matrix, two-layer authorization model, audit trail |
| 4 | [Technology Stack](docs/04-technologies.md) | Every layer of the stack and why it was chosen, explicit non-goals |
| 5 | [Database Schema](docs/05-database-schema.md) | All tables by domain, indexing/constraints, ER diagram |
| 6 | [REST API, Auth & Authorization](docs/06-api-and-auth.md) | API conventions, JWT/cookie auth flow, security checklist |
| 7 | [Deployment Architecture](docs/07-deployment.md) | Docker composition, deployment flow, CI/CD, monitoring |
| 8 | [Development Roadmap](docs/08-roadmap.md) | Phase-by-phase plan, MVP-first slice, current status |
| 9 | [Software Development Life Cycle](docs/09-sdlc.md) | The phase-gated SDLC model, per-module process, principles enforced throughout |
| 10 | [Team Collaboration & Best Practices](docs/10-team-collaboration.md) | Branching strategy, PR workflow, code review standards, commit conventions, communication norms |

Start with **Overview**, then move through in order — each doc builds on the one before it.

---

## 📁 Project Files

```
hr-nexus-docs/
├── README.md                        ← you are here
└── docs/
    ├── 01-overview.md
    ├── 02-architecture.md
    ├── 03-roles-and-permissions.md
    ├── 04-technologies.md
    ├── 05-database-schema.md
    ├── 06-api-and-auth.md
    ├── 07-deployment.md
    ├── 08-roadmap.md
    ├── 09-sdlc.md
    └── 10-team-collaboration.md
```

---

## Status

📋 **Phase 1 — Blueprint.** Architecture-only. No implementation code has been written. On approval, Phase 2 begins with Authentication + Users + Roles + Permissions + Employee Management.
