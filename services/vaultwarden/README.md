# Vaultwarden

Lightweight Bitwarden-compatible password manager server.

## Ports
| Port | Protocol | Purpose |
|------|----------|---------|
| 8001 | TCP | Web and API |

Bind on `0.0.0.0`, not `127.0.0.1`, so the reverse proxy container can
reach it.

## Setup
```bash
cp .env.example .env
# edit .env with your real domain
docker compose up -d
```

## Creating the first account
1. Set `SIGNUPS_ALLOWED=true` in `.env`.
2. `docker compose restart`, then register in the web UI.
3. Set it back to `false` and restart.

Afterwards, "Registration not allowed or user already exists" is the
expected response, meaning the flag is working.

## Notes
- Vaultwarden has no built-in server-side 2FA gate on the login page
  itself. Security rests on a strong master password and not exposing the
  instance more widely than necessary.
- The `vw-data` folder holds the encrypted vault. It should be in your
  backup set, and must never be committed.
- Never commit a real `ADMIN_TOKEN`. Only `.env.example` with a
  placeholder belongs in version control.
