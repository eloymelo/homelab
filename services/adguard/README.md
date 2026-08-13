# AdGuard Home

Network-wide DNS ad blocking and privacy filtering.

## Ports
| Port | Protocol | Purpose |
|------|----------|---------|
| 53 | TCP/UDP | DNS |
| 3000 | TCP | First-run setup wizard only |
| 3001 | TCP | Web UI |
| 853 | TCP/UDP | DNS over TLS |
| 784, 8853 | UDP | DNS over QUIC |
| 5443 | TCP/UDP | DNSCrypt |

## Usage
```bash
docker compose up -d
```

## Port 80 and 443 are deliberately unmapped
A default AdGuard setup binds 80 and 443. On this host those belong to
the reverse proxy, so both lines stay commented out. Run the first-boot
wizard on port 3000 and set the web UI to 3001 during setup, so AdGuard
never competes for 80 or 443 at all.

## Ownership requirement
The container runs internally as root (uid 0), so `confdir` and `workdir`
must be owned by `root:root`. This is an exception to the PUID/PGID 1000
pattern used by most other services here.

```bash
docker exec adguardhome id
sudo chown -R root:root /home/titan/adguardhome/confdir \
                        /home/titan/adguardhome/workdir
```

If settings stop persisting, or filtering appears to silently switch off,
ownership drift is the usual cause.

## LAN wildcard DNS rewrite
Filters, then DNS rewrites, then add `*.your-domain.com` pointing at the
server's LAN IP. Every `service.your-domain.com` then resolves locally
without leaving the network.

## Verifying that blocking works
```bash
dig doubleclick.net @<server-ip>     # expect 0.0.0.0
dig google.com @<server-ip>          # expect a real address
```

If a device still shows ads despite the above, it is usually the browser
using its own DNS-over-HTTPS and bypassing the network resolver entirely.
Check `chrome://settings/security` or Firefox's `about:preferences#privacy`.
DNS filtering also cannot block YouTube or most in-app ads, since those
are served from the same domains as the content itself.
