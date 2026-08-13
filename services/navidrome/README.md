# Navidrome

Music streaming server, compatible with Subsonic clients.

## Ports
| Port | Protocol | Purpose |
|------|----------|---------|
| 4533 | TCP | Web UI and API |

## Usage
```bash
docker compose up -d
```

## Notes
- The music library is mounted read-only. Lidarr writes to it, Navidrome
  only reads from it.
- Only `data`, the database and config, is worth backing up. The music
  files themselves are excluded from backup as re-acquirable.
- `ND_SCANSCHEDULE` controls how often the library is rescanned.
- `ND_BASEURL` is only needed when serving under a subpath. It is left
  commented out since this runs on its own subdomain.
