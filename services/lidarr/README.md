# Lidarr

Music collection manager. Feeds the same library that Navidrome serves.

## Ports
| Port | Protocol | Purpose |
|------|----------|---------|
| 8686 | TCP | Web UI |

## Usage
```bash
docker compose up -d
```

## Notes
- Root folder under Settings, then Media Management, should be `/music`.
- Same `/downloads` path rule as Radarr and Sonarr.
- Lidarr's upstream metadata provider has known outages. An occasional
  "cannot find artist" is sometimes that, not a local misconfiguration.
