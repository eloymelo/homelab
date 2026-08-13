# Seerr

Media request portal, the merged successor to Jellyseerr and Overseerr.
Lets other people request titles without touching Radarr or Sonarr.

## Ports
| Port | Protocol | Purpose |
|------|----------|---------|
| 5055 | TCP | Web UI |

## Usage
```bash
docker compose up -d
```

## Notes
- `init: true` is required by the current image. Leaving it out causes
  startup problems.
- Sign in with a Jellyfin account, then under Settings, then Services,
  add Radarr and Sonarr with their host, port and API keys.
