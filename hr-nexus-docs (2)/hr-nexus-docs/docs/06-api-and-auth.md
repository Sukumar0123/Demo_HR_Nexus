# 🔐 REST API, Authentication & Authorization

## REST API Architecture

- Base path: `/api/v1/` (versioned from day one).
- Resource-oriented, DRF `ViewSets` + `Routers` for standard CRUD; explicit `@action` routes for workflow verbs (`approve`, `reject`, `check-in`, `check-out`).
- Consistent list envelope: `{ count, next, previous, results }` (DRF pagination default).
- Consistent error shape: `{ error: { code, message, fields? } }`, mapped via a shared DRF exception handler — this is what guarantees no internal stack traces leak to clients.
- Filtering via `django-filter` (`?department=&status=&date_from=&date_to=`), sorting via `?ordering=`, search via `?search=`.
- OpenAPI schema auto-generated (drf-spectacular), served at `/api/schema/` with Swagger UI at `/api/docs/`.

## Authentication Flow

```
POST /api/v1/auth/login/
  → validate credentials (Django password hasher)
  → issue access JWT (short-lived, ~15 min) + refresh JWT (longer-lived, ~7 days)
  → set both as HttpOnly, Secure, SameSite=Strict cookies
  → response body contains only non-sensitive user/role info (no token in JSON)

Subsequent requests
  → DRF authentication class reads JWT from cookie
  → validates signature + expiry
  → attaches request.user

POST /api/v1/auth/refresh/  → validates refresh cookie → issues new access cookie
POST /api/v1/auth/logout/   → clears both cookies, optionally blacklists refresh token
```

The frontend **never sees or handles the raw JWT** — it just carries cookies automatically. The login response's user object includes the resolved role(s), and the SPA routes to the matching portal based on that server-provided value only — never a client-side role picker.

## Authorization Model

Two layers, both mandatory, neither sufficient alone:

1. **Role-level (coarse-grained):** DRF permission classes gate entire viewsets/actions.
2. **Object-level (fine-grained):** querysets are scoped per-request before serialization (see [Roles & Permissions](03-roles-and-permissions.md) for the code example).

**Rule of thumb baked into every endpoint:** never trust the frontend. Every protected API call must independently verify authentication, role, permission, and object ownership/access — regardless of what the UI shows or hides.

## Security checklist

- Secure authentication: JWT + HttpOnly cookies + password hashing
- RBAC + object-level authorization + field-level visibility on sensitive data (salary, documents)
- Input validation on every endpoint
- Secure, authenticated file access (no public URLs for documents/payslips)
- CORS configuration and CSRF protection appropriate to the cookie-based JWT flow
- Rate limiting on auth endpoints
- Secrets via environment variables — never committed to Git
- No leakage of passwords, JWT secrets, DB credentials, API keys, or stack traces

⬅️ [Back to root README](../README.md)
