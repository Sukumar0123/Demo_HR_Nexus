# 🚢 Deployment Architecture

## Docker Composition

```yaml
services:
  nginx:       # reverse proxy, TLS, serves built frontend, proxies /api to backend
  frontend:    # build stage only in prod (static output served by nginx); dev: Vite dev server
  backend:     # Gunicorn + Django
  celery:      # same image as backend, different entrypoint (celery worker)
  celery-beat: # scheduled tasks (nightly leave-balance accrual, scheduled reports)
  redis:       # broker + cache
  mysql:       # persistent named volume, env-driven credentials
```

- MySQL uses a **named Docker volume** (`mysql_data:/var/lib/mysql`) — never anonymous/temporary storage.
- Separate `docker-compose.yml` (dev, hot-reload/mounted volumes) and `docker-compose.prod.yml` (built images, no source mounts, Gunicorn workers tuned for CPU count).
- Backend and Celery share one image to avoid drift between task code and API code.

## Deployment Flow

```
Internet
   │ HTTPS (TLS terminated at Nginx, cert via Let's Encrypt/ACM)
   ▼
Nginx
   ├── /              → serves built React static assets
   ├── /api/*         → proxy_pass → Gunicorn (Django)
   └── /media/*       → authenticated file serving (via Django view, not direct static exposure)
   ▼
Gunicorn (sync or gevent workers, count = 2×CPU+1)
   ▼
Django app
   ▼
MySQL (managed service or self-hosted with backup strategy)

Redis + Celery workers run alongside as independent services/containers.
```

## CI/CD (GitHub Actions)

```
push → install deps → lint (ruff/eslint) → test (pytest, vitest)
     → build frontend → build & tag Docker images → push to registry
     → deploy (SSH/compose pull+up or orchestrator of choice)
```

Pipeline fails the build on any lint/test failure — no silent-pass placeholder steps.

## Health & Monitoring

- `GET /api/v1/health/` verifies DB connectivity and Redis connectivity, used by container orchestration/uptime monitoring.
- Sentry + structured logging for error visibility and auditability in production.

⬅️ [Back to root README](../README.md)
