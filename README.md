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
    ├── AdGuard Home  → DNS for all devices on the network
    ├── Nextcloud     → Cloud storage (exposed via nextcloud.your-domain.com)
    ├── ZeroByte      → File manager + backup drives
    ├── SearXNG       → Private search
    └── Audiobookshelf→ Media server
```

## Structure
```
homelab/
├── services/
│   ├── adguard/
│   ├── nextcloud/
│   ├── zerobyte/
│   ├── searxng/
│   └── audiobookshelf/
└── dns/
```

## Running a Service

Each service folder contains a `docker-compose.yml` and a `README.md`.
```bash
cd services/adguard
docker compose up -d
```

## Prerequisites

- Linux server (Debian/Ubuntu recommended)
- Docker and Docker Compose installed
- Basic familiarity with the terminal