# Dockhand

Lightweight container management dashboard.

## Ports
| Port | Protocol | Purpose |
|------|----------|---------|
| 3002 | TCP | Web UI |

## Usage
```bash
docker compose up -d
```

## Notes
The Docker socket is mounted read-write here, since the point of the tool
is starting, stopping and updating containers. Anything with read-write
access to the socket effectively has root on the host, so this should
stay on the LAN or behind an authenticated tunnel, never exposed
publicly.
