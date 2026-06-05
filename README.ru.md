[🇬🇧 English version](README.md)

# monitoring-stack

![Prometheus](https://img.shields.io/badge/Prometheus-E6522C?logo=prometheus&logoColor=white)
![Grafana](https://img.shields.io/badge/Grafana-F46800?logo=grafana&logoColor=white)
![Alertmanager](https://img.shields.io/badge/Alertmanager-E6522C?logo=prometheus&logoColor=white)
![Docker](https://img.shields.io/badge/Docker_Compose-2496ED?logo=docker&logoColor=white)

Готовый стек мониторинга для Linux-хостов: **Prometheus** собирает метрики с **node_exporter**, **Alertmanager** управляет алертами, а **Grafana** поставляется с преднастроенным источником данных и дашбордом. Одна команда `docker compose up` — и мониторинг работает.

## Стек

| Компонент | Порт | Роль |
|---|---|---|
| Prometheus | 9090 | Сбор метрик, хранение, оценка правил алертинга |
| node_exporter | 9100 | Метрики хоста (CPU, память, диск, сеть) |
| Alertmanager | 9093 | Маршрутизация и отправка алертов |
| Grafana | 3000 | Дашборды (преднастроены автоматически) |

## Быстрый старт

```bash
docker compose up -d
```

| Сервис | URL | Логин |
|---|---|---|
| Grafana | http://localhost:3000 | admin / admin |
| Prometheus | http://localhost:9090 | — |
| Alertmanager | http://localhost:9093 | — |

Дашборд **Linux Host Overview** (CPU, память, использование диска, сеть) добавляется автоматически — открой Grafana и он уже там.

## Алерты

Определены в [`prometheus/alerts.yml`](prometheus/alerts.yml) и оцениваются Prometheus:

| Алерт | Условие |
|---|---|
| `InstanceDown` | цель node_exporter недоступна более 1 минуты |
| `HighCpuLoad` | загрузка CPU > 85% в течение 5 минут |
| `LowDiskSpace` | корневая ФС занята более чем на 85% в течение 5 минут |

Alertmanager настроен с пустым получателем — подключи Slack, email или webhook в [`alertmanager/alertmanager.yml`](alertmanager/alertmanager.yml).

## Мониторинг нескольких хостов

Установи node_exporter на каждый хост и добавь его в задание `node` в [`prometheus/prometheus.yml`](prometheus/prometheus.yml):

```yaml
  - job_name: "node"
    static_configs:
      - targets: ["node-exporter:9100", "10.0.0.10:9100", "10.0.0.11:9100"]
```

## Структура

```
.
├── docker-compose.yml
├── prometheus/
│   ├── prometheus.yml        # конфигурация scrape и алертинга
│   └── alerts.yml            # правила алертов
├── alertmanager/
│   └── alertmanager.yml      # маршрутизация и получатели
└── grafana/
    ├── provisioning/         # провайдеры источника данных и дашбордов
    └── dashboards/           # дашборд Linux Host Overview
```

---

Часть [инфраструктурного портфолио](https://github.com/NickelFace?tab=repositories) · работает в лаборатории из [homelab-infrastructure](https://github.com/NickelFace/homelab-infrastructure)
