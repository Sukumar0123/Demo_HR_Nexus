# 🔄 Software Development Life Cycle (SDLC)

HR Nexus follows a **specification-first, phase-gated SDLC** — each phase produces a concrete deliverable that must be reviewed and approved before the next phase starts. No implementation code is written until its phase's design has been signed off.

## Why this SDLC model

An HR platform touches sensitive personal and financial data (salaries, personal documents, performance reviews) across many interdependent modules. Building fast and fixing architecture later is expensive here — a wrong decision on roles/permissions or database structure ripples through every module built on top of it. So the process favors **getting the design right before writing code**, rather than an ad-hoc "build first, structure later" approach.

## The Phases

| Phase | Name                                      | Deliverable                                                                                 | Gate to pass before moving on                                  |
| ----- | ----------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------------------- |
| 1     | **Requirements & Product Definition**     | Roles, permissions, workflows, business rules                                               | Stakeholder agreement on scope and roles                       |
| 2     | **Architecture (this Phase 1 blueprint)** | System architecture, DB schema, ER diagram, sitemap, tech stack                             | Blueprint reviewed and approved — see [Roadmap](08-roadmap.md) |
| 3     | **UX/UI Design**                          | Wireframes, design system, responsive layouts, dashboard designs                            | Design review                                                  |
| 4     | **Foundation**                            | Django + React scaffolds, MySQL, Docker Compose, JWT auth, roles/permissions                | Working auth + role routing                                    |
| 5     | **Core Feature Development**              | Module-by-module: Employees → Attendance/Leave → Recruitment/Payroll → Performance/Training | Each module's own pre-implementation note approved first       |
| 6     | **Testing & Security Hardening**          | Unit tests, API tests, frontend tests, security testing, UAT                                | All tests passing, no known critical vulnerabilities           |
| 7     | **Deployment**                            | Docker prod config, Nginx, CI/CD, monitoring, backups                                       | Successful staging deployment                                  |
| 8     | **Maintenance & Iteration**               | Bug fixes, incremental features, future AI/ML modules                                       | Ongoing                                                        |

## Per-module process (repeats for every feature module)

Before any module's code is written, its own short design note covers:

1. **Architecture changes** — does this module need anything new structurally?
2. **Database changes** — new tables/columns, migrations
3. **Endpoints** — what the API surface looks like
4. **Frontend pages/components** — what the user actually sees
5. **Permissions** — who can do what (checked against the [Roles & Permissions matrix](03-roles-and-permissions.md))
6. **Data flow** — how data moves from DB → API → UI, and back
7. **Testing strategy** — what "done and correct" means for this module

This keeps every module consistent with the rest of the system instead of each one reinventing conventions.

## SDLC principles enforced throughout

- **No skipping ahead.** Database design, authorization, validation, testing, and error handling are never skipped to "move faster" — they're part of the definition of done for every module.
- **Backend is the source of truth for security.** The frontend hides UI a role can't use; the backend independently re-verifies every request, regardless of process speed pressure.
- **Never invent data.** Every number shown in analytics traces back to a real, testable query — nothing is hardcoded or estimated to look good in a demo.
- **Fail loudly in CI, not silently in production.** The CI/CD pipeline fails the build on any lint/test failure — there are no silent-pass placeholder steps.

## How this maps to common SDLC modelsadasdsdfdsfda

HR Nexus's process is closest to a **Waterfall-with-gates model at the macro level** (Phase 1 → 2 → 3 ... each fully gated) combined with an **iterative/Agile approach inside each feature phase** (each module gets its own short design-review-build-test cycle rather than one big-bang release). This hybrid is deliberate: the macro architecture (roles, DB schema, auth model) is expensive to change late, so it's locked down early — but individual feature modules (Recruitment, Payroll, Training, etc.) are built and reviewed incrementally so problems surface early and often, module by module.

⬅️ [Back to root README](../README.md)
