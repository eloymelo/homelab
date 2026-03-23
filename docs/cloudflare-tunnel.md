# Cloudflare Tunnel

Cloudflare Tunnel is used as a reverse proxy to securely expose services to the internet without opening ports on the router.

The `cloudflared` daemon runs as a systemd service on the server and connects outbound to Cloudflare's network.

## Exposed Services

| Domain | Internal Service | Internal Address |
|--------|-----------------|------------------|
| nextcloud.your-domain.com | Nextcloud AIO | http://localhost:11000 |
| immich.your-domain.com | Immich | http://localhost:2283 |

## How It Works
```
Browser
    │
    ▼
Cloudflare (DNS + Tunnel)
    │  encrypted outbound connection
    ▼
cloudflared (systemd on server)
    │
    ▼
Internal service (localhost)
```

## Installation

Install cloudflared on Debian (64-bit):
```bash
# Add Cloudflare GPG key
sudo mkdir -p --mode=0755 /usr/share/keyrings
curl -fsSL https://pkg.cloudflare.com/cloudflare-public-v2.gpg | sudo tee /usr/share/keyrings/cloudflare-public-v2.gpg >/dev/null

# Add repository
echo 'deb [signed-by=/usr/share/keyrings/cloudflare-public-v2.gpg] https://pkg.cloudflare.com/cloudflared any main' | sudo tee /etc/apt/sources.list.d/cloudflared.list

# Install
sudo apt-get update && sudo apt-get install cloudflared
```

Install and run as a systemd service using your tunnel token from the Cloudflare Zero Trust dashboard:
```bash
sudo cloudflared service install YOUR_TUNNEL_TOKEN
```
The tunnel token is generated in the Cloudflare Zero Trust dashboard under Tunnels. Keep it private, anyone with this token can route traffic through your tunnel.


## Notes

- Tunnel configuration is managed via the Cloudflare Zero Trust dashboard
- Credentials are stored in `/etc/cloudflared/` on the server