# Termix

Browser-based SSH, RDP and VNC client with saved connections. Uses
Guacamole's `guacd` daemon for protocol handling.

## Ports
| Port | Protocol | Purpose |
|------|----------|---------|
| 8081 | TCP | Web UI |
| 4822 | TCP | guacd, internal dependency only |

The host port is 8081 because Nextcloud AIO already holds 8080.

## Usage
```bash
docker compose up -d
```

## Notes
- `guacd` does not need to be published on the host at all. It is
  reachable over the shared `termix-net` bridge. The mapping is present
  for debugging convenience and can be removed to reduce exposure.
- `termix-data` holds saved connections and any stored credentials, so it
  belongs in the backup set.
