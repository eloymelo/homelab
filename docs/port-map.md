# Port Map

Every host port in use. Check this before adding a service, and verify
against the live host with:

```bash
sudo ss -tlnp | grep LISTEN
docker ps --format "{{.Names}}\t{{.Ports}}"
```

| Port | Service |
|------|---------|
| 53 | AdGuard Home, DNS |
| 81 | Nginx Proxy Manager, admin UI |
| 443 | Nginx Proxy Manager, HTTPS |
| 784, 853, 5443, 8853 | AdGuard Home, encrypted DNS protocols |
| 2283 | Immich |
| 3000 | AdGuard Home, first-run wizard only |
| 3001 | AdGuard Home, web UI |
| 3002 | Dockhand |
| 3003 | Open WebUI |
| 3004 | Actual Budget |
| 4096 | Zerobyte |
| 4533 | Navidrome |
| 4822 | guacd, Termix dependency |
| 5055 | Seerr |
| 6767 | Bazarr |
| 6881 | qBittorrent, peer traffic, TCP and UDP |
| 7878 | Radarr |
| 8000, 9443 | Portainer |
| 8001 | Vaultwarden |
| 8080 | Nextcloud AIO mastercontainer |
| 8081 | Termix |
| 8090 | Beszel hub |
| 8091 | qBittorrent, web UI |
| 8095 | Glance |
| 8096 | Jellyfin |
| 8191 | Byparr |
| 8192 | FlareSolverr |
| 8199 | Kiwix |
| 8686 | Lidarr |
| 8888 | SearXNG |
| 8989 | Sonarr |
| 9090 | Cockpit, built into Fedora, not a container |
| 9696 | Prowlarr |
| 11000 | Nextcloud Apache, the reverse proxy target |
| 11434 | Ollama |
| 13378 | Audiobookshelf |
| 45876 | Beszel agent |
| 47984 to 47990, 47998 to 48000, 48010 | Game streaming |

## Note on port 80
Port 80 is currently unmapped on both AdGuard Home and Nginx Proxy
Manager. Nothing binds it. This works because the wildcard certificate
uses a DNS challenge rather than HTTP-01, and external traffic arrives
through the Cloudflare tunnel rather than a forwarded port. If you ever
switch to HTTP-01 challenges or want plain HTTP redirects, uncomment the
`80:80` mapping in the Nginx Proxy Manager compose file, not in AdGuard.

## Rule
Never reuse a host port. When two containers want the same internal port,
shift the host side, as done with Byparr on 8191 and FlareSolverr on
8192.
