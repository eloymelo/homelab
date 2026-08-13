# Kiwix

Serves offline ZIM archives, most usefully full Wikipedia dumps, over the
local network. Useful when the internet connection is down, and as a
private way to read reference material.

## Ports
| Port | Protocol | Purpose |
|------|----------|---------|
| 8199 | TCP | Web UI |

## Usage
Download the ZIM files you want into the data folder, then start:

```bash
docker compose up -d
```

The `command: "*.zim"` glob tells kiwix-serve to load every archive in
`/data`, so adding a new one is a matter of dropping the file in and
restarting the container.

## Notes
- ZIM files are large, a full English Wikipedia with images runs to tens
  of gigabytes. They are excluded from backup since they are downloadable
  again from the Kiwix library.
- The image is pinned to a specific version rather than `latest`.
