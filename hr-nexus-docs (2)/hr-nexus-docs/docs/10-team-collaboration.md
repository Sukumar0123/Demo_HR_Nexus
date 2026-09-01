# 🤝 Team Collaboration & Best Practices

This doc covers how the team actually works together day to day — branching strategy, code review, communication, and the habits that keep a multi-module project like HR Nexus consistent as more people touch it.

## Branching Strategyggggggyyyyyyyy

HR Nexus uses a **trunk-based branching model with short-lived feature branches**:

```
main                        ← always deployable, protected branch
 ├── feature/leave-approval-flow
 ├── feature/payroll-pdf-export
 ├── fix/attendance-timezone-bug
 └── chore/update-eslint-config
```

Rules:

- `main` is protected — no direct pushes. Every change goes through a Pull Request.
- Branch names are prefixed by intent: `feature/`, `fix/`, `chore/`, `refactor/`, `docs/`.
- Branches are short-lived (days, not weeks). Long-lived branches drift from `main` and produce painful merge conflicts — if a feature is big, split it into smaller PRs behind a feature flag rather than one giant branch.
- Rebase your branch on `main` regularly (`git pull --rebase origin main`) instead of letting it fall far behind.

## Pull Request Workflow

1. **Open the PR early**, even as a draft, once the direction is clear — this invites feedback before too much is built on a wrong assumption.
2. **Keep PRs small.** One module, one concern. A PR that touches Employees, Leave, and Payroll at once is nearly impossible to review properly.
3. **Write a real description**: what changed, why, how to test it, and screenshots for anything UI-facing.
4. **Link it to the module's design note** (see [SDLC](09-sdlc.md)) so the reviewer can check the implementation against the agreed plan — not just against "does this look okay."
5. **At least one approval required** before merge. For anything touching auth, permissions, or payroll, two approvals.
6. **Squash-merge** into `main` so the history stays readable — one clean commit per feature, not fifteen "wip" commits.

## Code Review Standards

Reviewers check for, in this order:

1. **Correctness** — does it do what the design note says it should?
2. **Security** — is authorization enforced backend-side (never trust the frontend, per [API & Auth](06-api-and-auth.md))? Are secrets kept out of the diff?
3. **Consistency** — does it follow existing patterns (naming, folder structure, error shapes) rather than introducing a new one-off style?
4. **Tests** — is there test coverage for the new behavior, especially permission boundaries?
5. **Readability** — would someone unfamiliar with this PR understand it in six months?

Review etiquette:

- Comments are about the code, not the person. "This query will N+1 on large teams" not "why would you write it this way."
- The PR author responds to every comment (even just "done" or "good catch, fixed") rather than silently pushing new commits.
- Nitpicks are labeled as such ("nit: ...") so the author knows they're optional, not blocking.

## Commit Message Convention

HR Nexus follows a lightweight [Conventional Commits](https://www.conventionalcommits.org/) style:

```
feat(leave): add manager approval endpoint
fix(attendance): correct timezone offset in check-in logs
docs(readme): update setup instructions
refactor(payroll): extract payslip generation into its own service
test(employees): add object-level permission tests
chore(deps): bump drf-spectacular to 0.27
```

This makes `git log` scannable, and later enables auto-generated changelogs if the project adopts them.

## Communication Norms

- **Async by default.** Design notes, PR descriptions, and code comments should carry enough context that a teammate in a different timezone can pick up the thread without a meeting.
- **Daily standup (or async standup post)** — what you did, what's next, any blockers. Kept short; deeper discussion moves to a separate thread or call.
- **Design discussions happen before code, not in PR comments.** If a PR review reveals a fundamental disagreement about approach, that's a signal the design note (Phase in [SDLC](09-sdlc.md)) needed more review up front — take it back a step rather than resolving architecture in a code review thread.
- **Decisions get written down.** Any non-trivial technical decision (e.g., "we chose optimistic locking over pessimistic for leave balance updates") gets a short note in the relevant doc, not just a conclusion in a chat thread that disappears in a week.

## Working Across Modules Without Stepping on Each Other

Because HR Nexus is a modular monolith (see [Architecture](02-architecture.md)), multiple people typically work in different Django apps / React features at once. To avoid collisions:

- Each Django app (`employees/`, `leave/`, `payroll/`, etc.) and each React `features/` folder is effectively "owned" by whoever's working on it for that sprint — cross-app changes get a heads-up in standup first.
- Shared code (`common/permissions.py`, `components/`) changes get extra review scrutiny since they affect everyone — avoid modifying shared files inside a feature branch unless the feature genuinely needs it.
- Database migrations are reviewed carefully and always additive/backward-compatible where possible, since multiple branches may be migrating in parallel — a destructive migration in one branch can break everyone else's local environment.

## Definition of Done (per feature)

A feature isn't "done" when the code works locally. It's done when:

- [ ] Matches its design note (architecture, DB, endpoints, permissions, data flow, testing — per [SDLC](09-sdlc.md))
- [ ] Has test coverage, including permission-boundary tests
- [ ] Passed CI (lint + tests) — see [Deployment](07-deployment.md)
- [ ] Reviewed and approved per the PR workflow above
- [ ] Deployed to staging and manually verified
- [ ] Documented if it changes a workflow, API contract, or permission rule

⬅️ [Back to root README](../README.md)
