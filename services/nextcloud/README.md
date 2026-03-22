# Nextcloud All-in-One

Self-hosted cloud storage and productivity platform.

## Ports
| Port | Purpose |
|------|---------|
| 8080 | AIO master container web UI |
| 11000 | Apache (internal) |

## Usage
docker compose up -d

## Notes
- Access AIO interface at https://your-server-ip:8080
- Set NEXTCLOUD_URL to your domain before starting
- SKIP_DOMAIN_VALIDATION=true required for local/reverse proxy setups