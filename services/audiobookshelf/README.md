# Audiobookshelf

Audiobook and podcast server with progress syncing across devices.

## Ports
| Port | Protocol | Purpose |
|------|----------|---------|
| 13378 | TCP | Web UI |

## Usage
```bash
docker compose up -d
```

## Notes
Unlike the video and music libraries, this one is fully backed up,
config, metadata and content. Audiobooks are harder to re-acquire than
films, and the listening progress stored in metadata is not replaceable.
