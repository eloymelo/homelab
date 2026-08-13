# FlareSolverr

Anti-bot challenge solver for indexers. Runs alongside
[Byparr](../byparr/) as a fallback, since some indexers work with one and
not the other.

## Ports
| Port | Protocol | Purpose |
|------|----------|---------|
| 8192 | TCP | API |

The container listens on 8191 internally, the same as Byparr, so the host
side is shifted to 8192 to avoid a collision.

## Usage
```bash
docker compose up -d
```
