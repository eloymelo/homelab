# ZeroByte

Self-hosted file manager with backup support.

## Ports
| Port | Purpose |
|------|---------|
| 4096 | Web UI |

## Usage
docker compose up -d

## Notes
- Requires SYS_ADMIN cap and /dev/fuse for filesystem access
- Set BASE_URL to your server IP before starting
- Generate a random string and replace APP_SECRET before running