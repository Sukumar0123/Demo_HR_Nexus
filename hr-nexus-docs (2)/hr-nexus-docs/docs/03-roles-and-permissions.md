# 👥 User Roles & Permissions

Legend: **F** = Full access, **T** = Team/scoped access only, **O** = Own-record only, **–** = No access

| Module | Super Admin | HR Administrator | Manager | Employee |
|---|---|---|---|---|
| Org/system settings | F | – | – | – |
| User & role management | F | – | – | – |
| Department/designation management | F | F (view+manage per HR scope) | – (view only) | – (view only) |
| Employee records | F | F | T (view team) | O |
| Attendance | F (view/audit) | F | T (view + review) | O (check-in/out, view own) |
| Leave | F (view/audit) | F | T (approve/reject team) | O (apply/view/cancel own) |
| Recruitment | F (view/audit) | F | – (unless hiring manager, then scoped) | – |
| Payroll | F (view/audit) | F | – | O (view/download own) |
| Performance | F (view/audit) | F (oversight) | T (create goals, review team) | O (self-review, view own) |
| Training | F (view/audit) | F | T (view team progress) | O (view/track own) |
| Documents | F (view/audit) | F | – | O |
| Notifications | F | F | T | O |
| Reports | F | F | T (team reports) | – |
| Analytics | F | F | T (team analytics) | – |
| Audit logs | F | – | – | – |
| Holidays/shifts | F | F (assign) | – (view) | – (view) |

## Enforcement rule

Every row above is enforced in **DRF permission classes and queryset filtering — never in the frontend alone.** The frontend hides UI a role can't use; the backend independently refuses the request regardless of what the frontend sends.

## Two authorization layers

**1. Role-level (coarse-grained)** — DRF permission classes such as `IsHRAdmin`, `IsManager`, `IsSuperAdmin` gate entire viewsets/actions.

**2. Object-level (fine-grained)** — every queryset is scoped to what the requester is allowed to see *before* any row reaches serialization:

```python
# illustrative, not implementation code
def get_queryset(self):
    user = self.request.user
    if user.role == "super_admin" or user.role == "hr_admin":
        return Employee.objects.all()
    if user.role == "manager":
        return Employee.objects.filter(manager=user.employee)
    return Employee.objects.filter(id=user.employee.id)
```

Payroll, documents, and performance data additionally check **field-level visibility** — an Employee serializer for a manager omits salary fields entirely, rather than returning and hiding them client-side. This guarantees `GET /api/employees/` never returns another employee's confidential data even if the frontend were bypassed entirely.

## Audit trail

Every audit-relevant action (login, logout, employee CRUD, leave approval/rejection, payroll updates, permission/role changes) triggers a Django signal that writes an `audit_logs` row with actor, action, target, before/after values, and IP — independent of which view triggered it, so it can't be forgotten module-by-module. Only Super Admin can access audit logs.

⬅️ [Back to root README](../README.md)
