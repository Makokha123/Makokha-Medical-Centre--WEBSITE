# Render Production Deployment

## 1. Create services
- Use the included `render.yaml` for Blueprint deploy, or create services manually.
- Provision a PostgreSQL database and attach its connection string as `DATABASE_URL`.

## 2. Required environment variables
- `APP_ENV=production`
- `SECRET_KEY=<long-random-secret>`
- `DATABASE_URL=<postgresql://...>`
- `ADMIN_USERNAME`
- `ADMIN_PASSWORD`
- `ADMIN_EMAIL`
- `ADMIN_NAME`
- `CRON_SECRET`

Optional but recommended:
- `SOCKETIO_CORS_ALLOWED_ORIGINS=https://<your-domain>`
- `RESEND_API_KEY`
- `RESEND_FROM_EMAIL`
- `RESEND_REPLY_TO` (optional)
- `RESEND_TIMEOUT_SECONDS` (optional, default `20`)

## 3. Start command
Use:

```bash
gunicorn --worker-class gthread --threads 8 --workers 1 --timeout 120 --bind 0.0.0.0:$PORT app:app
```

## 4. Database initialization
Run once after deploy:

```bash
python -c "from app import init_db; init_db()"
```

The included `render.yaml` already sets this as `postDeployCommand`.

## 5. Health checks
- Public health endpoint for Render: `GET /healthz`
- Protected keepalive endpoints (require `X-CRON-SECRET`):
  - `GET /health`
  - `GET /db-keepalive`
