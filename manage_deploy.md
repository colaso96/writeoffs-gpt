
## VPS deployment with Docker

This repo includes a production Docker stack with:

- `app`: Express API + built React frontend served from the same container/origin.
- `postgres`: PostgreSQL 16 with persistent Docker volume.
- `caddy`: reverse proxy + automatic TLS certificates for HTTPS.

### 1. Prepare production env file

1. Copy `.env.production.example` to `.env.production`.
2. Fill in all secrets and production values.
3. Keep these aligned:
   - `APP_ORIGIN=https://beforeyouradvisor.com`
   - `GOOGLE_CALLBACK_URL=https://beforeyouradvisor.com/auth/google/callback`
   - `STRIPE_SUCCESS_URL=https://beforeyouradvisor.com/?checkout_session_id={CHECKOUT_SESSION_ID}`
   - `STRIPE_CANCEL_URL=https://beforeyouradvisor.com/?checkout_cancelled=1`
   - `DATABASE_URL=postgres://<user>:<password>@postgres:5432/<db>`
4. Set `POSTGRES_USER`, `POSTGRES_PASSWORD`, and `POSTGRES_DB` to match `DATABASE_URL`.

### 2. Build and run on VPS

```bash
docker compose --env-file .env.production -f docker-compose.prod.yml up -d --build
```

Check services:

```bash
docker compose --env-file .env.production -f docker-compose.prod.yml ps
docker compose --env-file .env.production -f docker-compose.prod.yml logs -f app
docker compose --env-file .env.production -f docker-compose.prod.yml logs -f caddy
```

Stop stack:

```bash
docker compose --env-file .env.production -f docker-compose.prod.yml down
```

The Postgres volume (`beforeyouradvisor_pgdata`) keeps data between restarts.

### 3. Update external providers

- Google OAuth allowed origin: `https://beforeyouradvisor.com`
- Google OAuth callback: `https://beforeyouradvisor.com/auth/google/callback`
- Stripe checkout return URLs should match the values in `.env.production`
