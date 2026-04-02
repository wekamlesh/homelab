# Homelab Stack

This repository manages a small self-hosted homelab built with Docker Compose.

Current stack:

- Traefik for reverse proxy and TLS
- Authelia for authentication and route protection
- Homarr for the main dashboard
- Prometheus, Grafana, Alertmanager, Blackbox Exporter, and Node Exporter for observability

## Repo Layout

- `docker-compose.yml`: top-level compose include file
- `proxy/`: Traefik config