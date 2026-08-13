# Sonarr

TV series collection manager. Same pipeline as Radarr, for shows.

## Ports
| Port | Protocol | Purpose |
|------|----------|---------|
| 8989 | TCP | Web UI |

## Usage
```bash
docker compose up -d
```

## Notes
- `/downloads` must map to the same host path here as in qBittorrent,
  Radarr and Lidarr.
- Root folder under Settings, then Media Management, should be `/tv`.
- Add indexers through Prowlarr, not directly in Sonarr.
