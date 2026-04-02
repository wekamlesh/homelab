# Monitoring

This directory contains the observability stack.

## Included Services

- Prometheus
- Grafana
- Alertmanager
- Blackbox Exporter
- Node Exporter

## What It Does

- Prometheus scrapes service and host metrics
- Grafana provides dashboards and exploration
- Alertmanager stores alert routing and silences
- Blackbox Exporter performs internal HTTP probe checks
- Node Exporter exposes host CPU, memory, disk, and filesystem metrics

## Files

- `compose.yml`: service definitions
- `prometheus/prometheus.yml`: scrape jobs
- `prometheus/alerts.yml`: Prometheus alert rules
- `alertmanager/alertmanager.yml`: Alertmanager routing config
- `blackbox/blackbox.yml`: probe module config
- `grafana/provisioning/datasources/prometheus.yml`: provisioned Grafana datasources

## URLs

- Prometheus: `https://metrics.<your-domain>`
- Grafana: `https://grafana.<your-domain>`
- Alertmanager: `https://alerts.<your-domain>`

Blackbox Exporter and Node Exporter are internal only.

## Start Or Recreate

```bash
docker compose up -d --force-recreate prometheus grafana alertmanager blackbox-exporter node-exporter
docker compose logs -f prometheus grafana alertmanager blackbox-exporter node-exporter
```

## Default Scrapes

Prometheus currently scrapes:

- Prometheus
- Traefik
- Grafana
- Alertmanager
- Blackbox Exporter
- Node Exporter

It also runs Blackbox HTTP probes against:

- Homarr
- Authelia
- Grafana
- Prometheus
- Alertmanager

## Grafana

Grafana is provisioned with:

- `Prometheus` as the default datasource
- `Alertmanager` as a datasource for alert visibility

If provisioning does not appear after a recreate, verify the mounted datasource file:

```bash
docker compose exec grafana sh -c 'sed -n "1,220p" /etc/grafana/provisioning/datasources/default.yml'
```

## Alerting Notes

- Alertmanager currently has a placeholder receiver only.
- Alerts can appear in the UI, but no external notifications are configured yet.

## Common Checks

Check Prometheus targets:

```bash
docker compose exec prometheus wget -qO- http://localhost:9090/api/v1/targets
```

Check Node Exporter metrics:

```bash
docker compose exec prometheus wget -qO- http://node-exporter:9100/metrics | head
```

## Notes

- Node Exporter uses `pid: host` and mounts `/` read-only for host-level metrics.
- Prometheus data is stored in the named volume `prometheus-data`.
- Grafana data is stored in the named volume `grafana-data`.
- Alertmanager data is stored in the named volume `alertmanager-data`.
