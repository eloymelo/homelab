# Tailscale, Exit Node and Subnet Route

Two separate features are both required to reach LAN services from mobile
data. Confusing them is the single most common cause of "works on wifi,
fails on 5G".

An exit node routes all traffic through the server. A subnet route makes
the server's LAN addresses reachable from other devices on the tailnet.
Reaching a service by LAN IP needs the second one, not just the first.

## Server
```bash
tailscale up --advertise-exit-node \
  --advertise-routes=<your-lan-subnet>/24
```

Then approve both separately in the admin console, the exit node and the
advertised subnet route. Advertising alone does nothing until each is
approved.

## DNS
In the admin console under DNS:

- Enable Override DNS servers, pointed at the resolver that knows about
  your internal rewrites, which is the AdGuard host.
- Delete stale nameserver entries from previous installs. Old addresses
  left in that list cause silent resolution failures.

## Fedora firewalld
```bash
sudo firewall-cmd --permanent --add-masquerade
sudo firewall-cmd --permanent --zone=trusted --add-interface=tailscale0
sudo firewall-cmd --permanent --zone=trusted --add-port=22/tcp
sudo firewall-cmd --permanent --zone=trusted --add-port=53/tcp
sudo firewall-cmd --permanent --zone=trusted --add-port=53/udp
sudo firewall-cmd --permanent --zone=trusted --add-port=80/tcp
sudo firewall-cmd --permanent --zone=trusted --add-port=81/tcp
sudo firewall-cmd --permanent --zone=trusted --add-port=443/tcp
sudo firewall-cmd --reload
```

Masquerade is what allows the server to forward traffic on behalf of
other devices. Without it, subnet routing silently fails.

## Optional, UDP throughput fix that persists across reboots
`/etc/systemd/system/tailscale-ethtool.service`

```ini
[Unit]
Description=Tailscale UDP GRO Optimization
After=network-online.target
Wants=network-online.target

[Service]
Type=oneshot
ExecStart=/usr/sbin/ethtool -K <your-nic> rx-udp-gro-forwarding on rx-gro-list off

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl enable --now tailscale-ethtool.service
```

## Every client device
Each device must select the server as its exit node manually. This is
per-device, and is itself a frequent cause of "it works on my phone but
not my laptop". Also confirm that Use Tailscale DNS Settings is enabled
on each one.

## Debug ladder, in this order
1. `sudo tailscale status`, and look for the server offering an exit node.
2. From a phone on mobile data with the exit node selected, open
   `http://<tailscale-ip>:<port>`. If that fails, the problem is exit
   node or route approval. If it works, continue.
3. `sudo tcpdump -i tailscale0 port 53 -n`. You should see the client's
   DNS query answered with the server's LAN address. A correct answer but
   a page that still will not load means the problem is routing, so go to
   step 4.
4. `sudo tcpdump -i any port <service-port> -n`. Zero packets means the
   subnet route is missing or unapproved. Packets arriving but failing
   means firewalld zone or masquerade configuration.

## Already ruled out, do not spend time here again
- AdGuard binding on `0.0.0.0:53`. That was always correct.
- Docker's `DOCKER-USER`, `FORWARD` and NAT rules. Fine as they are, the
  mark-based masquerade rule already exists.
- Disabling firewalld entirely. This did not fix a routing problem on its
  own.
- `unexpected EOF` upstream DNS errors in the AdGuard log. A red herring,
  local rewrites never touch upstream DNS.
- Reinstalling the mobile app. No effect by itself.
