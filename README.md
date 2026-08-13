# Homelab

Personal homelab running self-hosted services on a Linux server.
Built and maintained as a hands-on infrastructure learning environment
and portfolio.

Around 25 containerised services behind a single reverse proxy with
wildcard TLS, network-wide DNS filtering, private remote access over a
mesh VPN, and automated nightly backups with failure alerting.

## Stack

### Core infrastructure
| Service | Purpose | Port |
|---------|---------|------|
| [AdGuard Home](services/adguard/) | Network-wide DNS filtering | 3001 |
| [Nginx Proxy Manager](services/npm/) | Reverse proxy and wildcard TLS | 443, 81 |
| [Portainer](services/portainer/) | Container management UI | 9443 |
| [Dockhand](services/dockhand/) | Lightweight container dashboard | 3002 |
| [Glance](services/glance/) | Homepage dashboard | 8095 |
| [Beszel](services/beszel/) | Monitoring hub | 8090 |
| [Beszel Agent](services/beszel-agent/) | Metrics collector | 45876 |
| [Termix](services/termix/) | Browser-based SSH and RDP client | 8081 |

### Media
| Service | Purpose | Port |
|---------|---------|------|
| [Jellyfin](services/jellyfin/) | Media server, NVIDIA transcoding | 8096 |
| [Prowlarr](services/prowlarr/) | Indexer manager | 9696 |
| [Radarr](services/radarr/) | Movie collection manager | 7878 |
| [Sonarr](services/sonarr/) | TV collection manager | 8989 |
| [Lidarr](services/lidarr/) | Music collection manager | 8686 |
| [Bazarr](services/bazarr/) | Subtitle manager | 6767 |
| [qBittorrent](services/qbittorrent/) | Download client | 8091 |
| [Seerr](services/seerr/) | Media request portal | 5055 |
| [Byparr](services/byparr/) | Anti-bot solver for indexers | 8191 |
| [FlareSolverr](services/flaresolverr/) | Anti-bot solver, fallback | 8192 |
| [Navidrome](services/navidrome/) | Music streaming | 4533 |
| [Audiobookshelf](services/audiobookshelf/) | Audiobooks and podcasts | 13378 |

### Storage, productivity and reference
| Service | Purpose | Port |
|---------|---------|------|
| [Nextcloud AIO](services/nextcloud/) | File sync and collaboration | 8080, 11000 |
| [Zerobyte](services/zerobyte/) | Restic backup management UI | 4096 |
| [Vaultwarden](services/vaultwarden/) | Password manager | 8001 |
| [Actual](services/actual/) | Personal budgeting | 3004 |
| [Kiwix](services/kiwix/) | Offline Wikipedia and ZIM archives | 8199 |

### Search and AI
| Service | Purpose | Port |
|---------|---------|------|
| [SearXNG](services/searxng/) | Private metasearch engine | 8888 |

Ollama and Open WebUI run alongside this stack rather than as part of it,
Ollama natively for GPU access and Open WebUI as a standalone container.
See [docs/local-ai-stack.md](docs/local-ai-stack.md).

## Guides
| Guide | Description |
|-------|-------------|
| [Rebuild checklist](docs/rebuild-checklist.md) | Order of operations after a fresh OS install |
| [OS base setup](docs/os-base-setup.md) | Fedora and Debian first-boot differences |
| [Docker install](docs/docker-install.md) | Docker Engine and Compose |
| [Port map](docs/port-map.md) | Every host port in use |
| [Restic backups](docs/restic-backups.md) | Backup script, timer, retention and restore |
| [Cloudflare Tunnel](docs/cloudflare-tunnel.md) | Exposing services without opening router ports |
| [Tailscale](docs/tailscale.md) | Exit node and subnet routing, with a debug ladder |
| [Samba](docs/samba.md) | Browsing the server from macOS Finder |
| [SELinux notes](docs/selinux-notes.md) | Relabeling after restores on Fedora |
| [Local AI stack](docs/local-ai-stack.md) | Ollama, Open WebUI and SearXNG |
| [Game streaming](docs/game-streaming.md) | Headless Steam streaming, and what failed first |
| [SSHFS on Windows](docs/sshfs-windows.md) | Mounting remote folders on Windows |
| [Troubleshooting](docs/troubleshooting.md) | Every real problem hit, and its cause |
| [Lessons](docs/lessons.md) | Cross-cutting takeaways |

## Network overview
```
Internet
    │
    ▼
Cloudflare Tunnel                    Tailscale (private access)
    │                                    │
    ▼                                    ▼
Nginx Proxy Manager  ◄───────────────────┘
    │  wildcard TLS, one subdomain per service
    ▼
Docker services on the LAN
    │
    ├── AdGuard Home    → DNS for every device on the network
    ├── Jellyfin + *arr → Media server and acquisition pipeline
    ├── Nextcloud       → File sync
    ├── Vaultwarden     → Password manager
    ├── SearXNG         → Private search
    └── Beszel, Glance  → Monitoring and dashboards
            │
            ▼
    restic → external drive, nightly, with Healthchecks.io alerting
```

## Conventions
- Paths use `/home/titan` as a stand-in for the service user's home
  directory. Adjust to match your own.
- Domains appear as `your-domain.com`, and the server address as
  `<server-ip>` or `your-server-ip`.
- Any service needing a credential ships a `.env.example`. Copy it to
  `.env`, which is gitignored, and fill in real values there. No real
  secret belongs in a compose file.
- Most containers run as PUID and PGID 1000. The exceptions are listed
  in [docs/lessons.md](docs/lessons.md) and in the relevant service
  README.

## Prerequisites
- A Linux server. This stack is built on Fedora Server, with Debian
  differences noted throughout the docs.
- Docker and Docker Compose.
- An NVIDIA GPU with the Container Toolkit, only for Jellyfin
  transcoding and the local AI stack.

## Running a service
Each service folder holds a `docker-compose.yml` and a `README.md`. Some
also hold a `.env.example`.

```bash
cd services/jellyfin
docker compose up -d
```

Where a `.env.example` exists, copy it first:

```bash
cp .env.example .env
```
