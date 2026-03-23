# Immich

Self-hosted photo and video backup solution.

## Ports
| Port | Purpose |
|------|---------|
| 2283 | Web UI |

## Usage
1. Copy `.env.example` to `.env` and fill in your values
2. `docker compose up -d`

## Notes
- Generate a secure DB_PASSWORD with `openssl rand -hex 32`
- UPLOAD_LOCATION is where your photos will be stored
- DB_DATA_LOCATION should be on an SSD, not a network share