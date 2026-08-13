# OS Base Setup

## Fedora: free port 53 before installing AdGuard
Fedora runs `systemd-resolved`, which binds port 53 and blocks AdGuard
from starting.

```bash
sudo mkdir -p /etc/systemd/resolved.conf.d/
echo -e "[Resolve]\nDNS=1.1.1.1\nDNSStubListener=no" | \
  sudo tee /etc/systemd/resolved.conf.d/no-stub.conf

sudo rm -f /etc/resolv.conf
echo "nameserver 1.1.1.1" | sudo tee /etc/resolv.conf

sudo systemctl restart systemd-resolved
```

Verify. Nothing should be listening on 53, though 5353 and 5355 are fine:

```bash
ss -tulpn | grep :53
```

## Fedora: firewalld
Firewalld is enabled by default, so every new service port needs an
explicit rule:

```bash
sudo firewall-cmd --permanent --add-port=<PORT>/tcp
sudo firewall-cmd --reload
```

This is the most common reason a container starts fine but is
unreachable from another machine on the LAN.

## Debian differences
- No firewall by default, `ufw` is not installed, so skip every
  `firewall-cmd` step.
- No SELinux, so skip every `restorecon` step.
- The `systemd-resolved` port 53 conflict is usually absent.
- Use `apt` instead of `dnf`.
