# Homelab

Personal homelab running self-hosted services on a Linux server.  
Built and maintained as a hands-on DevOps learning environment and portfolio.

## Stack

| Service | Purpose | Port |
|---------|---------|------|
| [AdGuard Home](services/adguard/) | Network-wide DNS ad blocking | 3000 |
| [Nextcloud AIO](services/nextcloud/) | Self-hosted cloud storage | 8080 |
| [ZeroByte](services/zerobyte/) | File manager with backup support | 4096 |
| [SearXNG](services/searxng/) | Private metasearch engine | 8888 |
| [Audiobookshelf](services/audiobookshelf/) | Audiobook and podcast server | 13378 |
| [Immich](services/immich/) | Self-hosted photo and video backup | 2283 |

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
    └── Immich         → Self-hosted photo and video backup
```

## Structure
```
homelab/
├── docs/
│   ├── cloudflare-tunnel.md
│   └── sshfs-windows.md
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