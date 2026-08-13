# Actual Budget

Self-hosted personal budgeting, envelope method.

## Ports
| Port | Protocol | Purpose |
|------|----------|---------|
| 3004 | TCP | Web UI |

## Important: this is the development compose file

The compose file here is the one shipped inside the Actual Budget source
repository for building the app from source. It has three properties that
make it unsuitable as a standalone homelab service:

- `build: .` expects a `Dockerfile` next to it, which is not in this repo.
- `volumes: - '.:/app'` bind-mounts the current directory as the app
  source, so it expects a full source checkout.
- `restart: 'no'` means it will not come back after a reboot.

It is committed as-is because it reflects what is actually running, but
it should be replaced with a production deployment using the published
server image. Check the Actual Budget self-hosting documentation for the
current image name and recommended compose file, then confirm where the
data volume should be mounted so it can be added to the backup set.

## Notes
- Data persistence is the open question here. Under this file, budget
  data lives inside the bind-mounted source directory rather than a
  named volume or a clean host path, which makes it easy to lose during a
  rebuild and easy to miss when configuring backups.
