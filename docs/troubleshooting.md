# Troubleshooting Index

Every problem actually hit on this setup, with its cause.

## Networking and proxy
| Symptom | Cause and fix |
|---|---|
| `Bind for 0.0.0.0:XXXX failed: port is already allocated` | Change the host port, the left side of the colon. The container port stays. Check with `sudo ss -tlnp \| grep LISTEN` |
| Service unreachable from the LAN, Fedora | firewalld. `sudo firewall-cmd --permanent --add-port=N/tcp` then reload |
| Nginx Proxy Manager 502 Bad Gateway | Forward hostname must be the server's LAN IP, never `127.0.0.1`, which resolves to the proxy container itself. Or the service is bound only to `127.0.0.1` on the host. Test with `docker exec nginx-proxy-manager curl -I http://<lan-ip>:PORT` |
| Page loads but never streams or updates | Websockets Support is off on that proxy host |
| `SSL_ERROR_UNRECOGNIZED_NAME_ALERT` | The wildcard certificate is not attached to that proxy host |
| Cloudflare error 1033 | The tunnel connector is not running or not connected. Reinstall the service using the current tunnel's token |
| Cloudflare error 1016 | The proxy host exists in NPM but the tunnel route was deleted. Re-add the public hostname, or remove the NPM host |

## Tailscale
| Symptom | Cause and fix |
|---|---|
| Works on wifi, fails on mobile data | Missing `--advertise-routes`, or the route was never approved in the admin console |
| Exit node not listed on a client | Not approved in the console, or not selected on that specific device |

## Media stack
| Symptom | Cause and fix |
|---|---|
| Jellyfin crash loop, `Access to the path '/cache/.jellyfin-cache' is denied` | The non-backed-up folders do not exist. `mkdir -p ~/jellyfin/{cache,movies,tv-shows}` then fix ownership |
| Jellyfin shows titles with unreadable names | Files were moved in by hand. Rename folders to `Title (Year)`, or re-import through Radarr |
| Playback hangs with no error | Almost always a transcode failure. Get the real reason with `docker logs jellyfin --tail 300 \| grep -B2 -A2 "FFmpeg exited"`. The exit code line alone is not the reason, the failing ffmpeg command is logged just above it |
| `Cannot load libcuda.so.1` in Jellyfin | GPU passthrough missing from the compose file |
| `10 bit encode not supported` | The source is 10-bit HEVC and H.264 encoding cannot handle it on this GPU. Use `hevc_nvenc` instead |
| Prowlarr cannot connect to an indexer, DNS or SSL error | Usually host or AdGuard DNS. Test with `docker exec prowlarr nslookup google.com` |
| Prowlarr will not sync an indexer to an app | Check Sync Categories, try matching tags on both sides, or use a different indexer |
| Radarr reports `API Key is invalid` but curl with the same key works | Prowlarr is too old for the app's API version. `docker compose pull` |

## DNS
| Symptom | Cause and fix |
|---|---|
| AdGuard will not start, port 53 in use, Fedora | The `systemd-resolved` stub listener. See [os-base-setup.md](os-base-setup.md) |
| AdGuard settings do not persist, or filtering silently stops | `confdir` and `workdir` are owned by the wrong user. They must be `root:root` |
| Ads still appear although `dig` returns `0.0.0.0` | The browser is using its own DNS-over-HTTPS. Or they are YouTube or in-app ads, which DNS filtering cannot block |

## Backups
| Symptom | Cause and fix |
|---|---|
| `unknown flag: --exclude-xattr` | That flag does not exist. Remove it |
| `<path>: Is a directory` during backup | A `#` comment inside the backslash-continued path list broke line continuation. Delete the commented lines |
| `unable to locate cache directory` | `Environment=HOME=/root` is missing from the systemd service |

## Hardware and drivers
| Symptom | Cause and fix |
|---|---|
| `nvidia-smi: couldn't communicate with the NVIDIA driver` | The driver stack was removed as a cascading dependency. Reinstall the full package list and run `akmods --force` |
| `libnvidia-ml.so.1: cannot open shared object` in a container | Same root cause as above |
| MCE or `[Hardware Error]` in dmesg | Count distinct events, not log lines: `sudo journalctl -k \| grep "mce: \[Hardware Error\]: Machine check events logged"`. A single event is often benign. Watch for recurrence, run memtest86+, and check for a BIOS update |

## macOS clients
| Symptom | Cause and fix |
|---|---|
| Finder: "The file couldn't be opened" over `sftp://` | Finder's SFTP is unreliable. Use Samba, see [samba.md](samba.md) |
| "The disk you attached was not readable" | The drive is ext4. Use anylinuxfs, see [restic-backups.md](restic-backups.md) |
