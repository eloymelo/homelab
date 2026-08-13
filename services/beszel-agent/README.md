# Beszel Agent

Reports system and container metrics to the [Beszel hub](../beszel/).

## Ports
| Port | Protocol | Purpose |
|------|----------|---------|
| 45876 | TCP | Agent listener |

Runs with `network_mode: host` so it can read host-level network and
disk metrics, so the listen port is not published through a mapping.

## Setup
1. Add the system in the hub UI first. The hub generates a public key
   and a token for it.
2. ```bash
   cp .env.example .env
   # paste the KEY and TOKEN from the hub UI, set HUB_URL
   docker compose up -d
   ```

## Notes
- `TOKEN` is a registration credential and belongs in `.env`, not in the
  compose file. `KEY` is a public key and is less sensitive, but it is
  kept alongside the token for convenience.
- The Docker socket is mounted read-only so the agent can report
  per-container stats.
