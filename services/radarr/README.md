# Radarr

Movie collection manager. Searches through Prowlarr's indexers, hands
downloads to qBittorrent, then renames and moves finished files into the
Jellyfin library.

## Ports
| Port | Protocol | Purpose |
|------|----------|---------|
| 7878 | TCP | Web UI |

## Usage
```bash
docker compose up -d
```

## The path rule
`/downloads` must map to the same host path across qBittorrent, Radarr,
Sonarr and Lidarr. If it does not, the import and move step fails
silently, with no obvious error.

## Setup
- Settings, then Download Clients, then add qBittorrent with its host,
  port and credentials.
- Settings, then Media Management, then set the root folder to `/movies`.

Jellyfin should never watch the downloads folder directly. Radarr owns
the rename and move into `/movies`.
