# Game Streaming

Goal: stream Steam and Proton games from the server to a laptop, with no
desktop environment installed on the server.

## What did not work
Documented so the same paths are not retried.

| Approach | Outcome |
|---|---|
| Sunshine with bare KMS capture | Pairs and encodes correctly, NVENC works, but Steam never renders. There is no compositor for OpenGL or Vulkan to draw into, producing endless `graphics.cpp:620` GL errors |
| Sway | wlroots and the proprietary NVIDIA driver are a poor match |
| Wolf, games-on-whales | Its own documentation flags the NVIDIA container-toolkit path as not recommended |
| SteamOS on desktop | AMD GPUs only as of 2026, which rules out an NVIDIA card |
| KDE with X11 | Works today, but upcoming Plasma releases drop X11 entirely, so it is not worth building on |

## Moonshine, the approach that worked
Moonshine spins up an isolated Wayland compositor per stream, so it needs
no monitor, no dummy plug and no persistent desktop session.

1. Install Steam first, from RPM Fusion on Fedora:
   ```bash
   sudo dnf install \
     https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm \
     https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
   sudo dnf install steam
   which steam
   ```

2. Install Moonshine, checking the releases page for the current version:
   ```bash
   cd /tmp
   curl -LO https://github.com/hgaiser/moonshine/releases/download/vX.Y.Z/moonshine-X.Y.Z-1.x86_64.rpm
   sudo dnf install ./moonshine-*.rpm
   ```
   The package's post-install step handles udev rules, kernel modules and
   the `moonshine` group.

3. Enable the service. Lingering is what lets it run with no active login:
   ```bash
   sudo loginctl enable-linger $USER
   sudo systemctl enable --now moonshine@$USER
   ```

4. Firewall:
   ```bash
   sudo firewall-cmd --permanent --add-port=47984/tcp \
     --add-port=47989/tcp --add-port=47990/tcp --add-port=48010/tcp
   sudo firewall-cmd --permanent --add-port=47998-48000/udp
   sudo firewall-cmd --reload
   ```

5. Add the Steam scanner to `~/.config/moonshine/config.toml`:
   ```toml
   [[application_scanner]]
   type = "steam"
   library = "$HOME/.local/share/Steam"
   command = ["/usr/bin/steam", "-bigpicture", "steam://rungameid/{game_id}"]
   ```
   ```bash
   sudo systemctl restart moonshine@$USER
   ```

6. Pair with Moonlight. Headless pairing means the desktop notification
   never appears, which is expected. Use the pin page instead:
   `http://<server-ip>:47989/pin`

7. Launch Steam once through Moonlight and log in. This creates
   `libraryfolders.vdf`, without which the scanner sees no games.

8. Set resolution in the Moonlight client, not on the server. Moonshine
   sizes its virtual compositor to whatever the client requests.

### Troubleshooting
| Symptom | Cause |
|---|---|
| `503 Application failed to start` | The log shows `steam not found in PATH`. Steam is not installed |
| `Failed to list Steam libraries` | Harmless until you log into Steam once |
| `Failed to show PIN notification: ServiceUnknown` | Harmless. There is no notification daemon on a headless box. Use the pin page |
| Stream stuck at low resolution | Set it client-side in Moonlight |

```bash
systemctl status moonshine@$USER
journalctl -u moonshine@$USER -n 100 --no-pager -f
moonshine healthcheck --config ~/.config/moonshine/config.toml
```

## Fallback that also worked, Sunshine with Xorg, Openbox and picom
Only worth using if Moonshine is unavailable, since it needs a persistent
X session. The missing piece for a long time was picom. Without a
compositor, Steam Big Picture renders black even though the process is
alive and streaming.

```bash
sudo dnf install openbox xorg-x11-server-Xorg xorg-x11-xinit picom -y
```

`/etc/X11/Xwrapper.config`:
```
allowed_users=anybody
needs_root_rights=yes
```

`~/start-openbox.sh`:
```bash
#!/bin/bash
export DISPLAY=:0
Xorg :0 vt7 -novtswitch -sharevts &
sleep 3
openbox &
sleep 1
picom --backend glx &      # the backend must be explicit on Fedora
sleep 1
sunshine
```

This path requires a dummy DisplayPort plug, after which Sunshine
captures through NvFBC. Stop the systemd user unit first to avoid an RTSP
port clash on 48010:

```bash
systemctl --user stop app-dev.lizardbyte.app.Sunshine.service
```

Match the Sunshine package to the exact OS release. A package built for a
different Fedora version produces dependency errors that look like broken
software but are only a version mismatch. Note that the service is a user
unit, not a system one:

```bash
systemctl --user enable --now app-dev.lizardbyte.app.Sunshine.service
```

For CSRF errors in the web UI, set this in
`~/.config/sunshine/sunshine.conf`:

```
csrf_allowed_origins=https://<server-ip>:47990
```
