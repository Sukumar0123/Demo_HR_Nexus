# Authentication & Authorization

## Authentication Overview

The application uses a JWT-based authentication system with secure cookie storage. Authentication is implemented using two token types:

| Token Type  | Purpose                                                            | Lifetime    |
| ----------- | ------------------------------------------------------------------ | ----------- |
| Access JWT  | Authorizes API requests                                            | Short-lived |
| Refresh JWT | Obtains new Access JWTs without requiring the user to log in again | Long-lived  |

### Token Storage

JWT tokens are stored in secure cookies with the following configuration:

* **HttpOnly**: Prevents JavaScript access to tokens.
* **Secure**: Cookies are transmitted only over HTTPS.
* **SameSite**: Protects against CSRF attacks.
* **Frontend applications do not access, store, or manage raw JWT tokens directly.**
* Authentication is handled automatically through browser-managed cookies.

---

## Authentication Flow

### Login Flow

1. User submits valid credentials.
2. Backend validates credentials.
3. Backend generates:

   * Access JWT
   * Refresh JWT
4. Both tokens are stored in HttpOnly, Secure, SameSite cookies.
5. User is considered authenticated.
6. Frontend receives authentication status but does not receive raw JWT values.

```text
User Login
    ↓
Credential Validation
    ↓
Generate Access JWT + Refresh JWT
    ↓
Set Secure HttpOnly Cookies
    ↓
Authenticated Session Created
```

### Accessing Protected APIs

1. Browser automatically includes authentication cookies with requests.
2. Backend validates the Access JWT.
3. If valid:

   * Request proceeds.
4. If expired:

   * Refresh flow is triggered.

```text
Client Request
    ↓
Access JWT Validation
    ↓
Valid → Process Request
Expired → Refresh Flow
```

### Refresh Token Flow

When the Access JWT expires:

1. Client sends a refresh request.
2. Refresh JWT is validated.
3. If valid:

   * New Access JWT is generated.
   * Refresh JWT may be rotated according to security policy.
4. Updated cookies are sent back to the browser.
5. User session continues without requiring re-authentication.

```text
Expired Access JWT
        ↓
Refresh Endpoint
        ↓
Validate Refresh JWT
        ↓
Generate New Tokens
        ↓
Update Secure Cookies
        ↓
Continue Session
```

### Logout Flow

1. User initiates logout.
2. Backend invalidates the refresh session/token.
3. Authentication cookies are cleared.
4. Any future protected requests require authentication again.

```text
Logout Request
      ↓
Invalidate Refresh Session
      ↓
Clear Authentication Cookies
      ↓
Session Terminated
```

---

# Authorization Model

## Role-Based Access Control (RBAC)

Authorization is implemented using role-based access control.

### Authentication vs Authorization

* Authentication determines who the user is.
* Authorization determines what the user is allowed to do.

### Role Validation

For protected endpoints:

1. User must have a valid authenticated session.
2. Access JWT is validated.
3. User roles and permissions are extracted from trusted server-side authentication context.
4. Endpoint authorization rules are evaluated.
5. Access is granted or denied.

### Example Roles

| Role    | Description                   |
| ------- | ----------------------------- |
| Admin   | Full system access            |
| Manager | Access to management features |
| User    | Standard application access   |

> Actual role definitions should match the application's current role configuration.

### Protected Endpoints

Protected APIs require:

* Valid authenticated session.
* Valid Access JWT.
* Required role or permission.

Requests failing authentication return:

```http
401 Unauthorized
```

Requests failing authorization return:

```http
403 Forbidden
```

---

# Security Considerations

## JWT Security

* Access JWTs are short-lived.
* Refresh JWTs are used only for session renewal.
* Refresh tokens should be rotated when applicable.
* JWT signing keys must be securely managed.
* Expired or invalid tokens are rejected.

## Cookie Security

Authentication cookies must use:

* HttpOnly
* Secure
* SameSite

This prevents:

* Token theft through JavaScript
* Accidental token exposure
* Many CSRF attack scenarios

## Frontend Security

The frontend:

* Does not store JWTs in localStorage.
* Does not store JWTs in sessionStorage.
* Does not read JWT cookie values.
* Relies on browser-managed authentication cookies.
* Uses authenticated API requests without direct token handling.

## API Security Checklist

* [x] HTTPS enforced in all environments.
* [x] Access JWT validation on protected endpoints.
* [x] Refresh JWT validation before token renewal.
* [x] Secure HttpOnly cookie storage.
* [x] SameSite cookie protection enabled.
* [x] Frontend does not access raw JWT tokens.
* [x] Role-based authorization enforced.
* [x] Invalid and expired tokens rejected.
* [x] Logout clears authentication cookies.
* [x] Authentication failures return 401.
* [x] Authorization failures return 403.
* [x] Sensitive authentication events are logged.
* [x] Refresh token misuse detection implemented where applicable.

---

# Session Lifecycle Summary

1. User logs in.
2. Access JWT and Refresh JWT are issued.
3. Tokens are stored in Secure HttpOnly cookies.
4. Browser automatically sends cookies with requests.
5. Access JWT authorizes protected API access.
6. Refresh JWT renews expired access tokens.
7. Logout clears authentication cookies and terminates the session.

This implementation ensures secure authentication, minimizes token exposure, and supports role-based authorization across protected REST API endpoints.
