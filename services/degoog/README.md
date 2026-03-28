# Degoog

Self-hosted search aggregator that queries multiple engines and displays results in one place. Supports custom search engines, bang-command plugins, and slot plugins.

## Ports
| Port | Purpose |
|------|---------|
| 4444 | Web UI |

## Usage
1. Create the data folder and set the correct ownership:
```bash
mkdir -p ./data
sudo chown -R 1000:1000 ./data
```
2. Start the container:
```bash
docker compose up -d
```

## Notes
- Data is persisted in `./data` (do not delete this folder)
- Still in beta, not intended for production use
- Full documentation at https://fccview.github.io/degoog