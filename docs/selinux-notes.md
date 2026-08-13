# SELinux Notes, Fedora Only

restic does not preserve SELinux contexts, so after a restore you will
see alerts along the lines of:

```
SELinux is preventing sshd-session from search access on the
directory /home/titan
```

## Fix, always relabel rather than writing a policy module
```bash
sudo restorecon -Rv /home/titan
ls -Zd /home/titan      # expect system_u:object_r:user_home_dir_t:s0
```

Do not use the `audit2allow -M ... && semodule -i ...` workaround that
the SELinux troubleshooter suggests. It grants an exception around
mislabeled files instead of fixing the labels, and it hides genuine
permission problems later.

## Samba needs its own label
See [samba.md](samba.md). A Samba share requires a separate
`semanage fcontext` rule beyond the general `restorecon`.

## Docker and firewalld nftables issue
If containers cannot reach each other on Fedora:

```bash
sudo sed -i 's/FirewallBackend=nftables/FirewallBackend=iptables/g' \
  /etc/firewalld/firewalld.conf
sudo systemctl restart firewalld docker
```
