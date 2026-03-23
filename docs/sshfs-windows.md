# SSHFS Windows — Access Homelab Server from File Explorer

Mount the server's home directory as a Windows drive letter over SSH.

## Requirements

Install in this order on Windows:
- [WinFsp](https://github.com/winfsp/winfsp/releases) — FUSE for Windows
- [SSHFS-Win](https://github.com/winfsp/sshfs-win/releases) — SSHFS client

## SSH Key Setup

Generate a key on Windows (PowerShell):
```powershell
ssh-keygen -t ed25519
```

Copy the public key to the server:
```powershell
type $env:USERPROFILE\.ssh\id_ed25519.pub | ssh SERVER_USER@SERVER_IP "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"
```

Update `C:\Users\WIN_USER\.ssh\config`:
```
Host SERVER_IP
  HostName SERVER_IP
  User SERVER_USER
  IdentityFile ~/.ssh/id_ed25519
```

## Mount

**File Explorer:** Right-click This PC → Map network drive → folder:
```
\\sshfs.r\SERVER_USER@SERVER_IP\home\SERVER_USER
```
Check **Reconnect at sign-in**.

**PowerShell (after hibernate):**
```powershell
net use Z: \\sshfs.r\SERVER_USER@SERVER_IP\home\SERVER_USER
```

## Unmount

```powershell
net use Z: /delete
```

## Notes

- Use `sshfs.r` (not `sshfs`) — reads key directly from SSH config, more reliable after hibernate
- Drive disconnects after hibernate — remount with the `net use` command above
- Full read/write access to `/home/SERVER_USER` on the server
