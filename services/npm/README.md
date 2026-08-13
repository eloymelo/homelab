# Nginx Proxy Manager

Reverse proxy sitting in front of every other service, with a wildcard
Let's Encrypt certificate issued via DNS challenge.

## Ports
| Port | Protocol | Purpose |
|------|----------|---------|
| 443 | TCP | HTTPS |
| 81 | TCP | Admin UI |

Port 80 is commented out in the compose file. Uncomment it if you need
plain HTTP or HTTP-01 certificate challenges. The wildcard certificate
here uses a DNS challenge instead, which does not require port 80.

The image is pinned to a specific version rather than `latest`, so an
upgrade is a deliberate edit rather than something that happens on the
next `docker compose pull`.

## Usage
```bash
docker compose up -d
```

## Wildcard certificate (once)
SSL Certificates, then Add SSL Certificate, then Let's Encrypt:

- Domain Names: `*.your-domain.com` and `your-domain.com`
- Key Type: ECDSA 256
- Use DNS Challenge: yes (wildcards require it)
- DNS Provider: whichever hosts your domain
- Credentials: your DNS provider API token

Enter that token in the NPM UI only. It gets stored inside NPM's own
`data` volume, which is not tracked in this repo, so it never needs to
appear in a file here.

## Adding a proxy host
- Forward Hostname/IP: the server's LAN IP, never `127.0.0.1`. NPM runs
  inside a container, so `127.0.0.1` resolves to the NPM container
  itself, not the host.
- Forward Port: the service's host port.
- Websockets Support: on for anything that streams or live-updates.
- Block Common Exploits: off, since it breaks some apps.
- SSL tab: attach the wildcard certificate, Force SSL on, HTTP/2 on.
  Leave HSTS off unless you are sure, it is awkward to undo.
- No TLS Verify: on only for backends that speak HTTPS with a
  self-signed certificate.

## Common errors
| Symptom | Cause |
|---|---|
| 502 Bad Gateway | Wrong forward IP or port, or the service is bound only to `127.0.0.1` on the host. Test with `docker exec nginx-proxy-manager curl -I http://<lan-ip>:PORT` |
| Page loads but never streams or updates | Websockets Support was off |
| `cannot load certificate .../fullchain.pem` | A proxy host references a deleted certificate. Reissue and reassign it, or restore the `data` folder from backup |
