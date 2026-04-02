# Traefik

This directory contains the reverse proxy layer for the homelab.

## What It Does

- Terminates TLS on ports `80` and `443`
- Requests and stores Let's Encrypt certificates in `acme.json`
- Exposes the Traefik dashboard
- Publishes Prometheus metrics on the internal `:8082` metrics entrypoint
- Routes protected apps through Authelia

## Files

- `compose.yml`: Traefik service definition and labels
- `acme.json`: runtime ACME storage file

## Important Environment Variables

- `TRAEFIK_IMAGE`
- `ACME_EMAIL`
- `DOMAIN`
- `TZ`

## First-Time Setup

Create the ACME storage file:

```bash
touch proxy/acme.json
chmod 600 proxy/acme.json
```

## Start Or Recreate

```bash
docker compose up -d --force-recreate traefik
```

## URLs

- Dashboard: `https://traefik.<your-domain>/dashboard/`

The root route redirects to `/dashboard/`.

## Notes

- The dashboard is protected by Authelia.
- Prometheus scrapes Traefik internally on `traefik:8082`.
- `acme.json` is sensitive and should stay out of version control.
