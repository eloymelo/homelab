# Bazarr

Subtitle manager for the Radarr and Sonarr libraries.

## Ports
| Port | Protocol | Purpose |
|------|----------|---------|
| 6767 | TCP | Web UI |

## Usage
```bash
docker compose up -d
```

## Setup
Connect Bazarr to Radarr and Sonarr using each app's API key, then choose
subtitle languages and providers. Note that Bazarr mounts the media
libraries directly, so its paths must match what Radarr and Sonarr use.
