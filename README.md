# monitoring-stack

![Prometheus](https://img.shields.io/badge/Prometheus-E6522C?logo=prometheus&logoColor=white)
![Grafana](https://img.shields.io/badge/Grafana-F46800?logo=grafana&logoColor=white)
![Alertmanager](https://img.shields.io/badge/Alertmanager-E6522C?logo=prometheus&logoColor=white)
![Docker](https://img.shields.io/badge/Docker_Compose-2496ED?logo=docker&logoColor=white)

A ready-to-run observability stack for Linux hosts: **Prometheus** scrapes metrics from **node_exporter**, **Alertmanager** handles alerting, and **Grafana** ships with a pre-provisioned datasource and dashboard. `docker compose up` and you have monitoring — no manual clicking.

## Stack

| Component | Port | Role |
|---|---|---|
| Prometheus | 9090 | Metrics collection, storage, alert evaluation |
| node_exporter | 9100 | Host metrics (CPU, memory, disk, network) |
| Alertmanager | 9093 | Alert routing and notification |
| Grafana | 3000 | Dashboards (auto-provisioned) |

## Quick start

```bash
docker compose up -d
```

| Service | URL | Login |
|---|---|---|
| Grafana | http://localhost:3000 | admin / admin |
| Prometheus | http://localhost:9090 | — |
| Alertmanager | http://localhost:9093 | — |

The **Linux Host Overview** dashboard (CPU, memory, root-FS usage, network) is provisioned automatically — open Grafana and it's already there.

## Alerts

Defined in [`prometheus/alerts.yml`](prometheus/alerts.yml) and evaluated by Prometheus:

| Alert | Condition |
|---|---|
| `InstanceDown` | a node_exporter target is unreachable for 1m |
| `HighCpuLoad` | CPU usage > 85% for 5m |
| `LowDiskSpace` | root filesystem < 15% free for 5m |

Alertmanager ships with a no-op receiver — wire a Slack/email/webhook notifier in [`alertmanager/alertmanager.yml`](alertmanager/alertmanager.yml).

## Monitoring more hosts

Install node_exporter on each target host and add it to the `node` job in [`prometheus/prometheus.yml`](prometheus/prometheus.yml):

```yaml
  - job_name: "node"
    static_configs:
      - targets: ["node-exporter:9100", "10.0.0.10:9100", "10.0.0.11:9100"]
```

## Layout

```
.
├── docker-compose.yml
├── prometheus/
│   ├── prometheus.yml        # scrape + alerting config
│   └── alerts.yml            # alert rules
├── alertmanager/
│   └── alertmanager.yml      # routing + receivers
└── grafana/
    ├── provisioning/         # datasource + dashboard providers
    └── dashboards/           # Linux Host Overview dashboard
```

---

Part of my [infrastructure & DevOps portfolio](https://github.com/NickelFace?tab=repositories) · runs on the lab from [homelab-infrastructure](https://github.com/NickelFace/homelab-infrastructure).
