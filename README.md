# Homelab Stack

This repository manages a small self-hosted homelab built with Docker Compose.

Current stack:

- Traefik for reverse proxy and TLS
- Authelia for authentication and route protection
- Homarr for the main dashboard
- Prometheus, Grafana, Alertmanager, Blackbox Exporter, and Node Exporter for observability

Backup is not currently active in this repo. The `backup/` slice is not present and its include is commented out in `docker-compose.yml`.

## Repo Layout

- `docker-compose.yml`: top-level compose include file
- `proxy/`: Traefik config
- `auth/`: Authelia config, users, and secrets layout
- `dashboard/`: Homarr service
- `monitoring/`: Prometheus, Grafana, Alertmanager, Blackbox Exporter, and Node Exporter

## Prerequisites

- Docker Engine with Docker Compose plugin
- A Linux server with ports `80` and `443` open
- DNS records pointing these hosts at your server:
  - `auth.<your-domain>`
  - `home.<your-domain>`
  - `traefik.<your-domain>`
  - `metrics.<your-domain>`
  - `grafana.<your-domain>`
  - `alerts.<your-domain>`

If you are starting from a fresh server, follow the full prep guide first:

## First-Time Setup

### 1. Create `.env`

Copy the example file:

```bash
cp .env.example .env
```

Edit at least:

- `DOMAIN`
- `ACME_EMAIL`
- `TZ`
- `HOMARR_SECRET_ENCRYPTION_KEY`

### 2. Prepare Traefik certificate storage

Create the ACME file with the right permissions:

```bash
mkdir -p proxy
touch proxy/acme.json
chmod 600 proxy/acme.json
```

### 3. Prepare Authelia secrets

Create the secrets directory and generate the required files:

```bash
mkdir -p auth/secrets
openssl rand -hex 32 > auth/secrets/JWT_SECRET
openssl rand -hex 32 > auth/secrets/SESSION_SECRET
openssl rand -hex 32 > auth/secrets/STORAGE_ENCRYPTION_KEY
chmod 600 auth/secrets/JWT_SECRET auth/secrets/SESSION_SECRET auth/secrets/STORAGE_ENCRYPTION_KEY
cp auth/secrets/users_database.example.yml auth/secrets/users_database.yml
chmod 600 auth/secrets/users_database.yml
```

Then replace the example password hash in `auth/secrets/users_database.yml`.

If you want to generate a bcrypt hash with the Authelia image:

```bash
docker run --rm authelia/authelia:4.39.16 authelia crypto hash generate bcrypt --password 'ChangeMeNow' --no-confirm
```

### 4. Start the stack

```bash
docker compose up -d
```