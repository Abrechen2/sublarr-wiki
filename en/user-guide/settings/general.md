---
title: Settings — General
description: General application settings — host, security, authentication, UI, backups
published: true
date: 2026-04-13
---

# Settings — General

> [!TIP]
> Most users only need to configure **Host & Port**, **Authentication**, and **Backups**. The Database, Redis, CORS, and Plugin sections are for advanced deployments and can be safely ignored.

This page covers the UI-configurable settings in **Settings → General**. For environment variable configuration, see [Environment Variables](/getting-started/environment-variables).

## Host & Port

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Host | `0.0.0.0` | — | Bind address — keep default unless on a multi-NIC server |
| Port | `5765` | `SUBLARR_PORT` | HTTP port — change if port is already in use |
| URL Base | _(empty)_ | — | Set if running behind a reverse proxy at a subpath (e.g. `/sublarr`) |

## Authentication

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Authentication enabled | `false` | — | Enable single-account login |
| Username | `admin` | — | Login username |
| Password | _(set on first run)_ | — | Login password |

See [Login Setup](/getting-started/quick-start#authentication) for the full setup flow.

### Session & Login Security

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Session Timeout (minutes) | `0` | `SUBLARR_SESSION_TIMEOUT_MINUTES` | Inactivity timeout before auto-logout. `0` = no timeout |
| Max Login Attempts | `20` | `SUBLARR_MAX_LOGIN_ATTEMPTS` | Failed login attempts before account lockout |
| Lockout Duration (minutes) | `60` | `SUBLARR_LOCKOUT_DURATION_MINUTES` | How long the account stays locked after exceeding max attempts |
| Allowed IP Ranges | _(empty)_ | `SUBLARR_ALLOWED_IP_RANGES` | Comma-separated CIDR ranges (e.g. `192.168.1.0/24` = all IPs from .1.0 to .1.255). Leave empty to allow all IPs. |

> [!TIP]
> For homelab setups behind a VPN, you can leave Allowed IP Ranges empty. Use it when exposing Sublarr to the internet to restrict access to trusted networks.

## Logging

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Log Level | `INFO` | `SUBLARR_LOG_LEVEL` | Verbosity level: `DEBUG`, `INFO`, `WARNING`, `ERROR`, `CRITICAL` |
| Log File | `log/sublarr.log` | `SUBLARR_LOG_FILE` | Log file path. Docker default: `/config/sublarr.log` |
| Log Format | `text` | `SUBLARR_LOG_FORMAT` | Output format: `text` (human-readable) or `json` (structured for log aggregation) |

> [!NOTE]
> Set `SUBLARR_LOG_FORMAT=json` when feeding logs into Loki, Elasticsearch, or similar systems. The `text` format is easier to read in `docker logs`.

## Database

> [!NOTE]
> Most users can skip this section. SQLite (the default) works without any configuration.

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| DB Path | `/config/sublarr.db` | `SUBLARR_DB_PATH` | SQLite database file location |
| Database URL | _(empty)_ | `SUBLARR_DATABASE_URL` | Full SQLAlchemy URL (e.g. `postgresql://user:pass@host/db`). Overrides DB Path when set |
| DB Pool Size | `5` | `SUBLARR_DB_POOL_SIZE` | SQLAlchemy connection pool size (ignored for SQLite) |
| DB Pool Max Overflow | `10` | `SUBLARR_DB_POOL_MAX_OVERFLOW` | Extra connections beyond pool size (ignored for SQLite) |
| DB Pool Recycle | `3600` | `SUBLARR_DB_POOL_RECYCLE` | Recycle connections after N seconds to avoid stale connections |

> [!WARNING]
> Switching from SQLite to PostgreSQL requires a data migration. See [PostgreSQL Setup](/development/postgresql) for instructions.

## CORS

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| CORS Origins | `http://localhost:5173,http://localhost:5765` | `SUBLARR_CORS_ORIGINS` | Comma-separated allowed origins for CORS and WebSocket connections |

> [!WARNING]
> Setting `SUBLARR_CORS_ORIGINS=*` allows any origin. Only use this in fully trusted environments.

## Redis

> [!NOTE]
> Redis is optional. Without it, Sublarr uses in-memory caching and processing — perfectly fine for most setups.

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Redis URL | _(empty)_ | `SUBLARR_REDIS_URL` | Redis connection URL (e.g. `redis://localhost:6379/0`). Empty = in-memory fallback |
| Redis Cache Enabled | `true` | `SUBLARR_REDIS_CACHE_ENABLED` | Use Redis for provider search result cache (requires `redis_url`) |
| Redis Queue Enabled | `true` | `SUBLARR_REDIS_QUEUE_ENABLED` | Use Redis+RQ for the job queue (requires `redis_url` and a separate worker process) |

> [!TIP]
> Redis is optional. Without it, Sublarr uses an in-memory job queue and provider cache. Redis is recommended for multi-container deployments or when you need persistent job state across restarts.

## Plugins

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Plugins Directory | `/config/plugins` | `SUBLARR_PLUGINS_DIR` | Directory where custom provider plugins are loaded from |
| Plugin Hot Reload | `false` | `SUBLARR_PLUGIN_HOT_RELOAD` | Watch the plugins directory for changes and reload automatically |

See [Plugin Development](/development/plugin-development) for writing custom providers.

## Interface Preferences

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Interface Language | `en` | `SUBLARR_INTERFACE_LANGUAGE` | UI language: `en` (English) or `de` (German) |
| Items Per Page | `25` | `SUBLARR_ITEMS_PER_PAGE` | Number of items shown per page in list views |
| Default Library View | `grid` | `SUBLARR_DEFAULT_LIBRARY_VIEW` | Library display mode: `grid` or `list` |
| Default Library Sort | `alpha` | `SUBLARR_DEFAULT_LIBRARY_SORT` | Default sort order: `alpha`, `date`, or `score` |
| Date/Time Format | `relative` | `SUBLARR_DATETIME_FORMAT` | Timestamp display: `relative` (e.g. "2 hours ago") or `absolute` (e.g. "2026-04-13 14:30") |

## Quiet Hours

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Quiet Hours Enabled | `false` | `SUBLARR_QUIET_HOURS_ENABLED` | Pause automated searches and downloads during defined hours |
| Start Time | `23:00` | `SUBLARR_QUIET_HOURS_START` | Start of quiet period (24h format) |
| End Time | `07:00` | `SUBLARR_QUIET_HOURS_END` | End of quiet period (24h format) |
| Timezone | `UTC` | `SUBLARR_QUIET_HOURS_TIMEZONE` | Timezone for quiet hours (e.g. `Europe/Berlin`, `America/New_York`) |

> [!NOTE]
> Quiet hours suppress all automated provider searches and translation jobs. Manual actions from the UI are not affected.

## Disk Monitoring

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Disk Warning Threshold (%) | `90` | `SUBLARR_DISK_WARNING_THRESHOLD_PERCENT` | Disk usage percentage that triggers a warning |
| Disk Warning Notify | `true` | `SUBLARR_DISK_WARNING_NOTIFY` | Send a notification when disk usage exceeds the threshold |

## Updates

Sublarr checks GitHub releases for newer versions. Notification appears in the sidebar when an update is available. Auto-update is not supported — pull the new Docker image manually.

## Backups

Sublarr automatically backs up its SQLite database to `/config/backups/` on a configurable schedule.

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Auto Backup Enabled | `false` | `SUBLARR_BACKUP_AUTO_ENABLED` | Enable scheduled automatic backups |
| Backup Interval (hours) | `24` | `SUBLARR_BACKUP_AUTO_INTERVAL_HOURS` | Time between automatic backups |
| Backup on Startup | `false` | `SUBLARR_BACKUP_AUTO_ON_STARTUP` | Create a backup when the container starts |
| Notify on Failure | `true` | `SUBLARR_BACKUP_NOTIFY_ON_FAILURE` | Send a notification if a scheduled backup fails |

Restore by replacing `/config/sublarr.db` and restarting the container. You can also use the built-in restore feature under **Settings → Backup → Restore**.

## Analytics

Sublarr does not collect analytics or telemetry. No data leaves your server.
