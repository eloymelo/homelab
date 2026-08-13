# Samba, Browsing the Server from macOS Finder

Finder's built-in SFTP support is unreliable and fails with "The file
couldn't be opened". Samba is the more dependable option.

```bash
sudo dnf install samba samba-client -y     # or apt install on Debian
sudo nano /etc/samba/smb.conf
```

Append a share definition:

```ini
[home-share]
   path = /home/titan
   browseable = yes
   writable = yes
   valid users = titan
   create mask = 0664
   directory mask = 0775
```

```bash
sudo smbpasswd -a titan
sudo systemctl enable --now smb
sudo firewall-cmd --permanent --add-service=samba   # Fedora only
sudo firewall-cmd --reload
```

The Samba password is separate from the system login password, which is
what `smbpasswd` sets up.

## Fedora and SELinux: shares appear but subfolders look empty
This needs a relabel that is distinct from the general `restorecon` on
the home directory:

```bash
sudo dnf install policycoreutils-python-utils -y
sudo semanage fcontext -a -t samba_share_t "/home/titan(/.*)?"
sudo restorecon -Rv /home/titan
```

Without the `samba_share_t` label, the share mounts and looks valid while
every subfolder reads as empty, which looks like a permissions problem
rather than an SELinux one.

## Connecting from macOS
In Finder, press Cmd+K and enter `smb://<server-lan-ip>`, or the
Tailscale address when away from home.

## One-off copy without Samba
```bash
scp -r titan@<server-ip>:~/compose-backup ~/Desktop/
```
