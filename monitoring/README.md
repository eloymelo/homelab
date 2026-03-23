# Monitoring

A monitoring stack using Prometheus, cAdvisor and Grafana to track container and system metrics.

## Services

| Service | Purpose | Port |
|---------|---------|------|
| Prometheus | Metrics collection and storage | 9090 |
| cAdvisor | Container metrics exporter | 8081 |
| Grafana | Metrics visualization and dashboards | 3001 |

## How It Works
```
Docker containers
      │
      ▼
cAdvisor (exposes container metrics)
      │
      ▼
Prometheus (scrapes and stores metrics)
      │
      ▼
Grafana (visualizes metrics)
```

## Usage
```bash
docker compose up -d
```

Access Grafana at `http://your-server-ip:3001`

Default credentials: `admin` / `admin` — change on first login.

## Notes

- Grafana data and Prometheus data are persisted via named volumes
- Prometheus scrapes metrics every 15 seconds
- Ensure prometheus.yml is readable before starting: `chmod 644 prometheus/prometheus.yml`
- cAdvisor and Grafana are local access only, not exposed via Cloudflare Tunnel