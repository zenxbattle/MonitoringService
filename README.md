# ZenXBattle Monitoring

Observability stack for the ZenXBattle platform. Prometheus metrics collection + Grafana dashboards.

## Architecture

```
Service metrics (/metrics) ←─ Prometheus ─→ Grafana (dashboards)
                                    │
                              AlertManager (rules)
                                    │
                              Discord/Email alerts
```

## Components

| Component | Port | Description |
|-----------|------|-------------|
| **Prometheus** | `:9090` | Metrics collection, alert rules |
| **Grafana** | `:3000` | Dashboards, alerting UI |

## Scraped Services

All microservices expose Prometheus metrics at `/metrics`:
- ApiGateway
- AuthUserAdminService
- ChallengeService
- ProblemService
- CodeExecutionEngine

## Quick Start

```bash
docker-compose up -d
# Prometheus → http://localhost:9090
# Grafana    → http://localhost:3000 (admin/admin)
```

## Grafana Dashboards

Provisioned via `grafana/provisioning/`:
- **Service Overview** — request rates, errors, latency per service
- **gRPC Metrics** — method call rates, status codes
- **Infrastructure** — node CPU, memory, disk (k3s nodes)
- **Business Metrics** — active users, battles/minute, submissions

## Prometheus Rules

See `prometheus/prometheus.yml` for scrape configs and alert rules.

Alert conditions:
- Service down for >1 minute
- Error rate >5%
- p99 latency >2s
- Disk usage >85%

## Deploy (K3s)

```bash
kubectl apply -k https://github.com/zenxbattle/infrastructure/tree/k3s/k3s/monitoring
```

## Related Services

- [infrastructure](https://github.com/zenxbattle/infrastructure) — K3s manifests
- All microservices expose `/metrics` endpoints
