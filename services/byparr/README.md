# Byparr

Drop-in FlareSolverr API replacement. Solves anti-bot challenges so
Prowlarr can reach protected indexers.

## Ports
| Port | Protocol | Purpose |
|------|----------|---------|
| 8191 | TCP | API |

## Usage
```bash
docker compose up -d
```

## Notes
- No web UI. API docs are served at `:8191/docs`.
- Register it in Prowlarr under Settings, then Indexer Proxies, as a
  FlareSolverr proxy, then tag the specific protected indexers with it.
- Running Byparr and FlareSolverr side by side is deliberate. Which one
  works varies per indexer, and both get broken periodically as anti-bot
  measures change. Expect partial success.
