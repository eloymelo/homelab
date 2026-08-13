# Lessons

Cross-cutting things worth reading before debugging anything.

1. Always read the full package list before confirming `dnf remove`. One
   seemingly unrelated removal wiped an entire NVIDIA driver stack here.
   `dnf history info <ID>` is how you find out what actually happened.

2. Ownership is not uniform across services. Most run as PUID and PGID
   1000, but AdGuard Home and the Beszel hub need `root:root`, and
   Nextcloud needs uid 33 and gid 0 applied from inside the container.
   Three different rules, and a restore resets all of them.

3. Bash line continuation and `#` comments do not mix. Delete lines from
   multi-line backslash-continued blocks, never comment them out.

4. An exit node is not a subnet route. Reaching LAN addresses over
   Tailscale needs both flags, both approvals, and DNS override enabled.

5. The reverse proxy runs in a container, so `127.0.0.1` means the proxy
   itself. Always forward to the host's real LAN address.

6. Websockets Support is the fix for "it loads but never streams or
   updates". This has come up with more than one service.

7. Match a downloaded package to the exact OS release. A build for the
   wrong version produces dependency errors that look like broken
   software but are not.

8. Measure before optimizing. A widely recommended DNS-over-HTTPS
   provider measured roughly five times slower here than a plain IP
   resolver. Generic best practice lost to local data.

9. Let Radarr and Sonarr move files into the library. Moving them by hand
   reliably ends in unmatched metadata.

10. A restore is not a working system. Expect to fix ownership, relabel
    SELinux contexts, recreate runtime-only folders, and copy compose
    files back from the compose backup, every time.

11. Keep the compose backup current and pushed to Git. The compose files
    are the actual recipe. Everything else is either replaceable data or
    covered by restic.

12. When one symptom shows up in two services at once, look for the
    shared dependency before debugging either one individually. DNS is
    usually the shared dependency.

13. Count distinct events, not log lines, before treating a hardware
    error as urgent.

14. Some problems are upstream and not yours. Metadata server outages,
    indexers refusing to sync, anti-bot bypasses getting broken. Timebox
    them and move on.
