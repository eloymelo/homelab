# Homelab

Personal homelab running self-hosted services on a Linux server.  
Built and maintained as a hands-on DevOps learning environment and portfolio.

## Stack

### Services
| Service | Purpose | Port |
|---------|---------|------|
| [AdGuard Home](services/adguard/) | Network-wide DNS ad blocking | 3000 |
| [Nextcloud AIO](services/nextcloud/) | Self-hosted cloud storage | 8080 |
| [ZeroByte](services/zerobyte/) | File manager with backup support | 4096 |
| [SearXNG](services/searxng/) | Private metasearch engine | 8888 |
| [Audiobookshelf](services/audiobookshelf/) | Audiobook and podcast server | 13378 |
| [Immich](services/immich/) | Self-hosted photo and video backup | 2283 |

### Monitoring
| Service | Purpose | Port |
|---------|---------|------|
| [Prometheus](monitoring/) | Metrics collection and storage | 9090 |
| [cAdvisor](monitoring/) | Container metrics exporter | 8081 |
| [Grafana](monitoring/) | Metrics visualization and dashboards | 3001 |

## Network Overview
```
Internet
    │
    ▼
Router
    │
    ▼
Linux Server (your-server-ip)
    │
    ├── AdGuard Home   → DNS for all devices on the network
    ├── Nextcloud      → Cloud storage (exposed via nextcloud.your-domain.com)
    ├── ZeroByte       → File manager + backup drives
    ├── SearXNG        → Private search
    ├── Audiobookshelf → Media server
    ├── Immich         → Self-hosted photo and video backup
    └── Grafana        → Container and system monitoring (local only)
```

## Structure
```
homelab/
├── docs/
│   ├── cloudflare-tunnel.md
│   └── sshfs-windows.md
├── monitoring/
│   └── prometheus/
├── services/
│   ├── adguard/
│   ├── audiobookshelf/
│   ├── immich/
│   ├── nextcloud/
│   ├── searxng/
│   └── zerobyte/
└── dns/
```

## Prerequisites

- Linux server (Debian/Ubuntu recommended)
- Docker and Docker Compose installed
- Basic familiarity with the terminal

## Running a Service

Each service folder contains a `docker-compose.yml` and a `README.md`.
```bash
cd services/adguard
docker compose up -d
```

## Guides

| Guide | Description |
|-------|-------------|
| [Cloudflare Tunnel](docs/cloudflare-tunnel.md) | Exposing services to the internet without opening router ports |
| [SSHFS on Windows](docs/sshfs-windows.md) | Mounting remote server folders on Windows over SSH |