# Restic Backups and Healthchecks.io

Nightly deduplicated backups to an external drive, with a dead-man's
switch that alerts if a run is missed or fails.

## Initialise the repository, first time only
```bash
sudo mkdir -p /media/backup1/restic-repo
sudo restic init --repo /media/backup1/restic-repo
```

Store the resulting password in a password manager immediately. The
repository is unrecoverable without it. It must never appear in this
repo, in a script committed here, or in any documentation.

## Mounting the backup drive
If the drive has no partition table and ext4 was written to the raw
device, mount the whole disk, not a partition:

```bash
lsblk
sudo mkdir -p /media/backup1
sudo mount /dev/sdX /media/backup1
```

Add it to `/etc/fstab` using its UUID from `blkid`, with `nofail` so a
missing drive does not block boot:

```
UUID=<uuid> /media/backup1 ext4 defaults,nofail 0 2
```

## Healthchecks.io
Create a check with a period of 1 day and about an hour of grace time.
Copy its ping URL. That URL contains a UUID which acts as a bearer token
for the check, so treat it as a secret. Put it in an untracked file or
edit the script directly on the server, and keep the real value out of
version control.

## /etc/restic/backup.sh
```bash
#!/bin/bash
export RESTIC_REPOSITORY=/media/backup1/restic-repo
export RESTIC_PASSWORD_FILE=/etc/restic/password
export HOME=/root

HC_URL="https://hc-ping.com/YOUR-UUID-HERE"
LOGFILE="/var/log/restic-backup.log"

echo "=== Backup started: $(date) ===" >> "$LOGFILE"

BACKUP_OUTPUT=$(restic backup \
  /home/titan/adguardhome \
  /home/titan/audiobookshelf \
  /home/titan/bazarr/config \
  /home/titan/beszel \
  /home/titan/compose-backup \
  /home/titan/dockhand \
  /home/titan/glance \
  /home/titan/jellyfin/config \
  /home/titan/lidarr/config \
  /home/titan/navidrome/data \
  /home/titan/nginx \
  /home/titan/prowlarr/config \
  /home/titan/qbittorrent/config \
  /home/titan/radarr/config \
  /home/titan/searxng \
  /home/titan/seerr/config \
  /home/titan/sonarr/config \
  /home/titan/termix \
  /home/titan/vaultwarden \
  /home/titan/zerobyte 2>&1)

BACKUP_STATUS=$?
echo "$BACKUP_OUTPUT" >> "$LOGFILE"

restic forget --keep-daily 7 --keep-weekly 4 --keep-monthly 6 --prune >> "$LOGFILE" 2>&1

if [ $BACKUP_STATUS -eq 0 ]; then
  echo "=== Backup completed successfully: $(date) ===" >> "$LOGFILE"
  curl -fsS -m 10 --retry 5 -o /dev/null "$HC_URL"
else
  echo "=== Backup FAILED: $(date) ===" >> "$LOGFILE"
  curl -fsS -m 10 --retry 5 -o /dev/null "$HC_URL/fail"
fi
```

```bash
sudo chmod +x /etc/restic/backup.sh
```

## Script rules learned the hard way
- Never put a `#` comment line inside the backslash-continued path list.
  The trailing backslash on the previous line swallows the comment, and
  bash then tries to execute the next path as a command, producing a
  confusing `Is a directory` error. Delete lines instead of commenting
  them out.
- `--exclude-xattr` is not a valid restic flag.
- Only `/config` subpaths are covered for the *arr apps, Jellyfin,
  qBittorrent and Seerr, so their compose files are not in the snapshot.
  That is what the `compose-backup` folder in the list is for.
- Media files, movies, TV and torrent downloads, are deliberately
  excluded. They are re-downloadable and would inflate the repository for
  no real recovery benefit.

## systemd service and timer
`/etc/systemd/system/restic-backup.service`

```ini
[Unit]
Description=Restic Backup

[Service]
Type=oneshot
Environment=HOME=/root
ExecStart=/etc/restic/backup.sh
```

`Environment=HOME=/root` is mandatory. Without it restic fails with
`unable to open cache: unable to locate cache directory: neither
$XDG_CACHE_HOME nor $HOME are defined`.

`/etc/systemd/system/restic-backup.timer`

```ini
[Unit]
Description=Daily Restic Backup

[Timer]
OnCalendar=*-*-* 03:00:00
Persistent=true

[Install]
WantedBy=timers.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now restic-backup.timer
```

`Persistent=true` means a run missed while the machine was off fires on
the next boot rather than being skipped silently.

## Verification
```bash
systemctl list-timers | grep restic
sudo systemctl start restic-backup.service
tail -f /var/log/restic-backup.log

sudo restic -r /media/backup1/restic-repo --password-file /etc/restic/password snapshots
sudo restic -r /media/backup1/restic-repo --password-file /etc/restic/password stats
sudo restic -r /media/backup1/restic-repo --password-file /etc/restic/password check
```

The `snapshots` output includes a Paths column, which is how you confirm
that a newly added folder is actually being captured.

## Restore
```bash
# whole snapshot back to original absolute paths
sudo restic -r /media/backup1/restic-repo restore <ID> --target /

# a single folder
sudo restic -r /media/backup1/restic-repo restore <ID> --target / \
  --include "/home/titan/navidrome/data"

# then fix ownership
sudo chown -R $USER:$USER /home/titan
```

Using `--target /` restores each folder to its real absolute path. Using
a different target nests the full path underneath it, for example
`/home/titan/home/titan/...`, which then has to be moved by hand.

## Reading the backup drive from macOS
macOS cannot read ext4. Skip macFUSE, which needs Recovery Mode and
reduced security. Use anylinuxfs, which works through a microVM and needs
no kernel extensions:

```bash
brew tap nohajc/anylinuxfs
brew install anylinuxfs
sudo anylinuxfs list                # must be sudo, or the filesystem shows as Unknown
sudo anylinuxfs mount /dev/diskN    # whole disk, no partition suffix

brew install restic
mkdir -p ~/restic-restored
sudo restic -r /Volumes/diskN/restic-repo restore <ID> \
  --target ~/restic-restored --include "/path/inside/backup"
sudo chown -R $(whoami) ~/restic-restored
```

## Retention
| Flag | Effect |
|---|---|
| `--keep-daily 7` | Last 7 daily snapshots |
| `--keep-weekly 4` | 4 weekly snapshots beyond those |
| `--keep-monthly 6` | 6 monthly snapshots beyond those |
| `--prune` | Actually reclaims the disk space |

That gives roughly six months of recovery points. Deduplication keeps the
total size close to flat.
