# Glance

Config-driven homepage dashboard with widgets for bookmarks, container
status, server stats and feeds.

## Ports
| Port | Protocol | Purpose |
|------|----------|---------|
| 8095 | TCP | Web UI |

## Setup
```bash
cp .env.example .env
mkdir -p config assets
docker compose up -d
```

The compose file declares `env_file: .env`, so the container will not
start if that file is missing, even when it has nothing in it.

## Notes
- The read-only Docker socket mount is what powers the
  `docker-containers` widget. Remove it if you do not use that widget.
- Configuration lives in `config/glance.yml` and `config/home.yml`. A
  real dashboard config lists internal hostnames and service URLs, so
  consider committing a sanitized example rather than your live config.
