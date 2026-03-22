# AdGuard Home

Network-wide DNS ad blocker and privacy filter.

## Ports
| Port | Protocol | Purpose |
|------|----------|---------|
| 53 | TCP/UDP | DNS |
| 80 | TCP | HTTP |
| 443 | TCP/UDP | HTTPS |
| 3000 | TCP | Web UI (initial setup) |
| 853 | TCP/UDP | DNS over TLS |

## Usage
docker compose up -d

## Notes
- Access web UI on port 3000 on first run, moves to port 80 after setup
- Config and work data persisted in /home/titan/adguardhome