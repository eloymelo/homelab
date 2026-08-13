# Beszel Hub

Monitoring hub. Collects metrics from one or more agents and serves the
dashboard. The agent runs as a separate stack, see
[beszel-agent](../beszel-agent/).

## Ports
| Port | Protocol | Purpose |
|------|----------|---------|
| 8090 | TCP | Web UI |

## Usage
```bash
docker compose up -d
```

## Ownership requirement
The hub's data folder must be owned by `root:root`. This is one of the
two services here with that requirement, the other being AdGuard Home.
If ownership drifts after a restore, the hub will not start cleanly.

## Adding a system
Add the system in the hub UI. The hub generates a public key and a token,
which then go into the agent's configuration.
