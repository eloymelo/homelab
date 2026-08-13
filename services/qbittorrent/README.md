# qBittorrent

Download client for the *arr stack.

## Ports
| Port | Protocol | Purpose |
|------|----------|---------|
| 8091 | TCP | Web UI |
| 6881 | TCP/UDP | Peer traffic |

The web UI port is set in two places, `WEBUI_PORT` and the port mapping.
Both have to agree.

## Usage
```bash
docker compose up -d
```

## First run
The image generates a temporary password on first start. Read it from the
logs and change it in the web UI immediately:

```bash
docker logs qbittorrent | grep -i password
```

## Firewall
```bash
sudo firewall-cmd --permanent --add-port=6881/tcp
sudo firewall-cmd --permanent --add-port=6881/udp
sudo firewall-cmd --reload
```

## On VPNs
A mesh VPN such as Tailscale connects your own devices to each other. It
does not anonymize torrent traffic, and peers still see your home IP
address. Actual anonymization requires routing the client through a
commercial VPN that permits P2P, usually via a container such as Gluetun.
Most free VPNs block P2P outright.
