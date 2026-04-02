# Authelia

This directory contains the authentication layer for the homelab.

## What It Does

- Hosts the Authelia portal at `auth.<domain>`
- Provides Traefik forward-auth middleware
- Uses Redis for session storage
- Uses a local SQLite database for Authelia state
- Uses a file-based user database

## Files

- `compose.yml`: Authelia and Redis services
- `config/configuration.yml`: main Authelia config and access policy
- `secrets/`: file-based secrets and user database
- `secrets/users_database.example.yml`: example user file

## Secret Layout

Real secrets belong in `auth/secrets/`:

- `JWT_SECRET`
- `SESSION_SECRET`
- `STORAGE_ENCRYPTION_KEY`
- `users_database.yml`

These are mounted into the container at `/secrets`.

The main config in `config/configuration.yml` references the user database from `/secrets/users_database.yml`, and the Authelia container reads the other secrets using `_FILE` environment variables.

## Access Policy

Authelia currently protects:

- `home.<domain>`
- `traefik.<domain>`
- `metrics.<domain>`
- `grafana.<domain>`
- `alerts.<domain>`

If you add another protected app, update `config/configuration.yml`.

## First-Time Setup

Create secrets:

```bash
mkdir -p auth/secrets
openssl rand -hex 32 > auth/secrets/JWT_SECRET
openssl rand -hex 32 > auth/secrets/SESSION_SECRET
openssl rand -hex 32 > auth/secrets/STORAGE_ENCRYPTION_KEY
chmod 600 auth/secrets/JWT_SECRET auth/secrets/SESSION_SECRET auth/secrets/STORAGE_ENCRYPTION_KEY
```

Create a user database from the example:

```bash
cp auth/secrets/users_database.example.yml auth/secrets/users_database.yml
chmod 600 auth/secrets/users_database.yml
```

Generate a password hash:

```bash
docker run --rm authelia/authelia:4.39.16 authelia crypto hash generate bcrypt --password 'ChangeMeNow' --no-confirm
```

Paste the resulting hash into `auth/secrets/users_database.yml`.

## Start Or Recreate

```bash
docker compose up -d --force-recreate authelia redis
docker compose logs -f authelia
```

## Common Checks

Show live Authelia logs:

```bash
docker compose logs -f authelia
```

Verify the config inside the container:

```bash
docker compose exec authelia sh -c 'sed -n "1,220p" /config/configuration.yml'
```

Verify the user database inside the container:

```bash
docker compose exec authelia sh -c 'sed -n "1,220p" /secrets/users_database.yml'
```

## Notes

- `auth/config/db.sqlite3` is runtime state and is intentionally not tracked.
- `auth/config/notification.txt` is the local notifier output file and is intentionally not tracked.
- The `users_database.yml` file is treated as secret material and should stay out of git.
