# Zerobyte

Web UI for managing restic backups.

## Ports
| Port | Protocol | Purpose |
|------|----------|---------|
| 4096 | TCP | Web UI |

## Setup
```bash
cp .env.example .env
# edit .env, set BASE_URL and generate a real APP_SECRET
docker compose up -d
```

## Notes
- `APP_SECRET` is a real credential. It is kept in `.env`, which is
  gitignored, rather than inline in the compose file.
- `SYS_ADMIN` and `/dev/fuse` are required so the container can mount
  restic snapshots for browsing. This is a broad capability, which is a
  reason to keep this service off the public internet.
- The `/home` mount is the data being backed up, and `/backup1` is the
  destination drive. Adjust both to match your layout.
- The image is pinned to a specific version rather than `latest`, so
  upgrades are deliberate.
