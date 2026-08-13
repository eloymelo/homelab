# SearXNG

Private metasearch engine. Aggregates results from other engines without
tracking or profiling the user.

## Ports
| Port | Protocol | Purpose |
|------|----------|---------|
| 8888 | TCP | Web UI and API |

## Usage
```bash
docker compose up -d
```

## Enabling JSON output
JSON is off by default, and anything using SearXNG as a search backend
needs it. Edit `config/settings.yml`:

```yaml
search:
  formats:
    - html
    - json
```

```bash
docker compose restart
curl "http://<server-ip>:8888/search?q=test&format=json"
```

## Notes
The `config` folder holds `settings.yml`, which contains a `secret_key`
generated on first run. Keep the real config out of version control.
