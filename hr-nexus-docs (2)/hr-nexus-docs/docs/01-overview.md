# 📦 Project Overview — HR Nexus

**HR Nexus** is a centralized HR management and workforce analytics platform designed to replace fragmented spreadsheets and paper-based HR processes with a single, secure, role-aware system.

## Purpose

HR Nexus helps an organization:

- Manage employees, departments, and designations
- Track attendance and leave
- Run recruitment pipelines
- Process payroll records
- Manage performance reviews and training
- Store employee documents securely
- Generate reports and workforce analytics
- Give management data-driven insight into the workforce

The goal is to reduce manual HR work, centralize employee records, automate recurring HR processes, and support decision-making with real numbers instead of guesswork.

## Product Framing

- **Product name:** HR Nexus
- **Tagline:** Workforce Management & Analytics
- **Positioning:** A professional, enterprise-grade SaaS product — not a college demo or a simple CRUD app.

## Who uses it

Four roles, each scoped to exactly what they need:

| Role | In short |
|---|---|
| **Super Admin** | System-level control — org settings, users, roles, permissions, audit logs |
| **HR Administrator** | Runs day-to-day HR operations across every module |
| **Manager** | Scoped to their own team — approvals, team performance, team reports |
| **Employee** | Self-service — own profile, attendance, leave, payslips, training |

Full detail on permissions lives in [User Roles & Permissions](03-roles-and-permissions.md).

## Design principle

This is explicitly a **modular monolith**, not microservices. HR data (employees, attendance, leave, payroll, etc.) is highly relational and shared constantly across modules — splitting that into separate services early would mean either duplicating core data or paying for distributed-transaction overhead on what are fundamentally simple relational operations. See [System Architecture](02-architecture.md) for the full reasoning.

⬅️ [Back to root README](../README.md)
