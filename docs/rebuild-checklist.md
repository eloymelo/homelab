# Quick Rebuild Checklist

The order that has worked cleanly after a fresh OS install. Deviating
from it tends to reproduce the exact problems listed in
[troubleshooting.md](troubleshooting.md).

1. Set a static DHCP reservation in the router for the server's MAC
   address, so its LAN IP does not move.
2. Free port 53 on Fedora, see [os-base-setup.md](os-base-setup.md).
3. Install Docker, see [docker-install.md](docker-install.md).
4. Mount the external backup drive and add it to `/etc/fstab`.
5. Restore folders from restic, see [restic-backups.md](restic-backups.md).
6. Fix ownership. Everything under the home directory goes to your user,
   with two exceptions that must stay `root:root`: AdGuard's `confdir`
   and `workdir`, and Beszel's hub folder.
7. On Fedora, run `sudo restorecon -Rv $HOME`, see
   [selinux-notes.md](selinux-notes.md).
8. Copy any missing compose files from the compose backup folder. The
   restic snapshot only covers `/config` subfolders for several services,
   so their compose files are not in it.
9. Bring up every service with `docker compose up -d`.
10. Create Jellyfin's non-backed-up folders, `cache`, `movies` and
    `tv-shows`, before starting it.
11. Install and configure Tailscale, see [tailscale.md](tailscale.md).
12. Install and configure the Cloudflare tunnel, see
    [cloudflare-tunnel.md](cloudflare-tunnel.md).
13. Re-enable the restic timer and its Healthchecks.io ping.
14. Verify the Nginx Proxy Manager proxy hosts and wildcard certificate
    survived the restore.

## A restore is not the same as a working system
After every restic restore, expect to fix ownership, relabel SELinux
contexts on Fedora, recreate runtime-only folders, and copy compose files
back from the compose backup. Budget time for all four.
