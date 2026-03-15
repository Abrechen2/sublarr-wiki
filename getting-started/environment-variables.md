---
title: Environment Variables
description: True .env configuration options for Sublarr — settings that cannot be changed via the UI
published: true
date: 2026-03-15
tags: getting-started, configuration
editor: markdown
dateCreated: 2026-03-15
---

# Environment Variables

These are the **true environment variables** — options that must be set in your `.env` file or `docker-compose.yml` because they are read at startup before the UI is available. All other settings (providers, language profiles, translation backends, automation schedules, etc.) are configured through the [Settings UI](/user-guide/settings/general).

## Core Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `SUBLARR_PORT` | `5765` | HTTP port Sublarr listens on. Change if the port conflicts with another service on your host. |
| `SUBLARR_CONFIG_DIR` | `/config` | Directory for the SQLite database, config exports, and backup files. Mount a persistent volume here. |
| `SUBLARR_MEDIA_PATH` | `/media` | Root path of your media library inside the container. All subtitle files are written relative to this directory. |
| `SUBLARR_LOG_LEVEL` | `INFO` | Logging verbosity. One of: `DEBUG`, `INFO`, `WARNING`, `ERROR`. Use `DEBUG` temporarily when diagnosing issues. |
| `SUBLARR_DB_PATH` | `/config/sublarr.db` | Explicit path to the SQLite database file. Override only if you need the database on a separate volume from the rest of the config. |

## Optional Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `SUBLARR_API_KEY` | *(empty)* | Static API key for `X-Api-Key` authentication. When set, all API requests must include this header. Leave empty to run without API key protection on trusted local networks. |
| `SUBLARR_REDIS_URL` | *(empty)* | Redis connection URL (e.g. `redis://localhost:6379/0`). When provided, Sublarr uses Redis for the task queue instead of the built-in in-process queue. Recommended for larger libraries with concurrent translation workloads. |
| `SUBLARR_POSTGRES_URL` | *(empty)* | PostgreSQL connection URL. When set, Sublarr uses Postgres instead of SQLite. Example: `postgresql://user:pass@localhost:5432/sublarr`. SQLite is sufficient for most deployments. |

## Example Docker Compose

```yaml
services:
  sublarr:
    image: ghcr.io/abrechen2/sublarr:latest
    container_name: sublarr
    ports:
      - "5765:5765"
    volumes:
      - /path/to/config:/config
      - /path/to/media:/media
    environment:
      - SUBLARR_PORT=5765
      - SUBLARR_CONFIG_DIR=/config
      - SUBLARR_MEDIA_PATH=/media
      - SUBLARR_LOG_LEVEL=INFO
      - SUBLARR_API_KEY=your-secret-key-here
    restart: unless-stopped
```

> [!TIP]
> Keep sensitive values like `SUBLARR_API_KEY` in a `.env` file alongside your `docker-compose.yml` and reference them with `${SUBLARR_API_KEY}`. Never commit credentials to version control.

> [!NOTE]
> For all other configuration (providers, language profiles, translation settings, Sonarr/Radarr integration, notifications, etc.) use the **Settings UI** at `http://<host>:5765/settings`. Those settings are persisted to the database and do not require a container restart.

## Further Reading

- [Installation Guide](/getting-started/installation) — full Docker Compose setup
- [Settings — General](/user-guide/settings/general) — UI-configurable application settings
- [Settings — Integrations](/user-guide/settings/integrations) — Sonarr, Radarr, Jellyfin, Emby connections
