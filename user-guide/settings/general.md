---
title: Settings — General
description: General application settings — host, security, authentication, UI, backups
published: true
date: 2026-03-16
tags: settings
editor: markdown
dateCreated: 2026-03-14T18:04:54.250Z
---

<div class="settings-tabs">
<a class="settings-tab active" href="/user-guide/settings/general"><span class="mdi mdi-cog-outline"></span> General</a>
<a class="settings-tab " href="/user-guide/settings/media-management"><span class="mdi mdi-folder-cog-outline"></span> Media Management</a>
<a class="settings-tab " href="/user-guide/settings/profiles"><span class="mdi mdi-translate"></span> Profiles</a>
<a class="settings-tab " href="/user-guide/settings/providers"><span class="mdi mdi-cloud-search-outline"></span> Providers</a>
<a class="settings-tab " href="/user-guide/settings/translation"><span class="mdi mdi-brain"></span> Translation</a>
<a class="settings-tab " href="/user-guide/settings/integrations"><span class="mdi mdi-connection"></span> Integrations</a>
</div>

# Settings — General

![Settings — General](/img/screen-settings-general.png)

This page covers the UI-configurable settings in **Settings → General**. For the full environment variable reference, see [Configuration Reference](/getting-started/environment-variables).

## Host & Port

| Setting | Default | Env Variable | Description |
|---------|---------|-------------|-------------|
| Port | `5765` | `SUBLARR_PORT` | HTTP port — change if the port is already in use on your host |
| URL Base | *(empty)* | — | Set if running behind a reverse proxy at a subpath (e.g. `/sublarr`). Do not include a trailing slash. |
| Log Level | `INFO` | `SUBLARR_LOG_LEVEL` | Verbosity: `DEBUG`, `INFO`, `WARNING`, `ERROR` |
| Database Path | `/config/sublarr.db` | `SUBLARR_DB_PATH` | SQLite database file location. For PostgreSQL, set `SUBLARR_DATABASE_URL` instead. |

> **Note:** The bind address defaults to `0.0.0.0` (all interfaces). On a multi-NIC server, you can restrict this via the `SUBLARR_CORS_ORIGINS` environment variable if needed.

## API Key Authentication

Sublarr supports an optional static API key to protect all API endpoints.

| Setting | Default | Env Variable | Description |
|---------|---------|-------------|-------------|
| API Key | *(empty)* | `SUBLARR_API_KEY` | When set, every API request must include the `X-Api-Key: <key>` header |

When the API key is empty, the API is open — suitable for trusted local networks, but not recommended if exposed to the internet.

Clients and integrations must pass the header:

```http
X-Api-Key: your-api-key-here
```

## Single-Account Login

Sublarr includes a simple session-based authentication layer for the web UI. This is separate from the API key — it protects the browser interface with a username and password.

| Setting | Default | Description |
|---------|---------|-------------|
| Authentication enabled | `false` | Toggle to require login before accessing the web UI |
| Username | `admin` | Login username |
| Password | *(set on first run)* | Login password — set during the onboarding wizard |

**Setup flow:**

1. Go to **Settings → General → Authentication**
2. Toggle **Authentication enabled** to on
3. Set a username and a strong password
4. Click **Save**
5. The next page load will redirect to the login screen

> **Warning:** If you lose your password and are locked out, you can reset authentication by setting `SUBLARR_API_KEY` in your `.env` file and using the API to clear the auth config, or by editing the `config_entries` table directly in the SQLite database.

## Media Path

| Setting | Default | Env Variable | Description |
|---------|---------|-------------|-------------|
| Media Path | `/media` | `SUBLARR_MEDIA_PATH` | Root path of your media library inside the container |

This path must match the volume mount in your `docker-compose.yml`. All subtitle sidecar files are written relative to this root, and all path-traversal security checks (`is_safe_path()`) are scoped to this directory.

## Automatic Tasks

Sublarr runs several background jobs on a schedule.

| Setting | Default | Description |
|---------|---------|-------------|
| Wanted scan interval | `0` (off) | Hours between automatic Wanted scans. `0` = event-driven only (webhooks) |
| Wanted scan on startup | `false` | Run a Wanted scan immediately when the container starts |
| Wanted search interval | `24` | Hours between automatic provider searches for Wanted items |
| Upgrade enabled | `true` | Automatically replace low-quality subtitles when a better match is found |
| Upgrade minimum score delta | `50` | A new subtitle must score at least this much higher to trigger an upgrade |

## Notification Settings

Sublarr uses [Apprise](https://github.com/caronc/apprise) for notifications (Telegram, Pushover, Discord, Slack, email, and more).

```bash
tgram://bottoken/chatid        # Telegram
pover://userkey@apptoken       # Pushover
discord://webhookid/token      # Discord
```

| Setting | Default | Description |
|---------|---------|-------------|
| Notify on download | `true` | Send notification when a subtitle is downloaded |
| Notify on upgrade | `true` | Send notification when an existing subtitle is upgraded |
| Notify on batch complete | `true` | Notify when a batch search or translation run finishes |
| Notify on error | `true` | Notify on backend errors or provider failures |

## Backup Settings

| Setting | Default | Description |
|---------|---------|-------------|
| Backup directory | `/config/backups` | Where backup ZIP files are stored |
| Daily backups to keep | `7` | Retention count for daily backups |
| Weekly backups to keep | `4` | Retention count for weekly backups |
| Monthly backups to keep | `3` | Retention count for monthly backups |

## Updates

Auto-update is not supported — pull the new Docker image manually:

```bash
docker compose pull sublarr
docker compose up -d sublarr
```

## Logging

Sublarr writes structured logs to `/config/log/sublarr.log` inside the container.

| Setting | Default | Env Variable | Description |
|---------|---------|-------------|-------------|
| Log Level | `INFO` | `SUBLARR_LOG_LEVEL` | Verbosity: `DEBUG`, `INFO`, `WARNING`, `ERROR` |
| Max log size | `10 MB` | — | Log file rotated when it reaches this size |
| Backup count | `3` | — | Number of rotated log files kept (`.log.1`, `.log.2`, `.log.3`) |
| Log File | `/config/sublarr.log` | `SUBLARR_LOG_FILE` | Path to the log file inside the container |
| Log Format | `text` | `SUBLARR_LOG_FORMAT` | Output format: `text` (human-readable) or `json` (structured JSON for tools like Loki/Grafana) |

### Viewing Logs

**In the UI:** Go to **Settings → General → Logs** to view the last 200 log lines. Use the level filter to show only `WARNING` or `ERROR` entries.

**Via API:**
```http
GET /api/v1/logs?lines=500&level=ERROR
```

### Downloading Logs

Download the full log file for offline analysis or to attach to a support request:

```http
GET /api/v1/logs/download
```

Returns `sublarr.log` as a text file attachment.

### Log Rotation

```http
GET  /api/v1/logs/rotation               # get current config
PUT  /api/v1/logs/rotation               # update: {"max_size_mb": 10, "backup_count": 3}
```

> [!TIP]
> Change the log level via **Settings → General → Log Level** and save — takes effect immediately without a container restart.

---

## Analytics

Sublarr does not collect analytics or telemetry. No usage data leaves your server.

---

## Advanced

> [!NOTE]
> These settings are for advanced deployments. The default SQLite + in-process configuration is sufficient for most homelab use cases.

### Scan / Media Processing

| Setting | Default | Env Variable | Description |
|---------|---------|-------------|-------------|
| ffmpeg Timeout | `120` seconds | `SUBLARR_FFMPEG_TIMEOUT` | Seconds before ffmpeg subtitle-extraction is killed |
| Metadata Engine | `auto` | `SUBLARR_SCAN_METADATA_ENGINE` | Metadata reader: `ffprobe`, `mediainfo`, or `auto` (tries ffprobe first, falls back to mediainfo) |
| Metadata Workers | `4` | `SUBLARR_SCAN_METADATA_MAX_WORKERS` | Parallel workers for batch metadata scans |

### Plugin System

| Setting | Default | Env Variable | Description |
|---------|---------|-------------|-------------|
| Plugins Directory | `/config/plugins` | `SUBLARR_PLUGINS_DIR` | Directory scanned for installed plugins |
| Plugin Hot Reload | `false` | `SUBLARR_PLUGIN_HOT_RELOAD` | Enable watchdog file watcher for the plugins directory. When enabled, plugins are reloaded automatically when files change — useful during plugin development. |

### Database (PostgreSQL / Connection Pool)

By default, Sublarr uses SQLite at `SUBLARR_DB_PATH`. To switch to PostgreSQL, set `SUBLARR_DATABASE_URL` and leave `SUBLARR_DB_PATH` empty.

> [!NOTE]
> PostgreSQL support requires the `psycopg2` package in the Docker image. The default SQLite database is sufficient for most single-user homelab setups.

| Setting | Default | Env Variable | Description |
|---------|---------|-------------|-------------|
| Database URL | *(empty)* | `SUBLARR_DATABASE_URL` | Empty = SQLite. Set to `postgresql://user:pass@host/db` for PostgreSQL. |
| Pool Size | `5` | `SUBLARR_DB_POOL_SIZE` | SQLAlchemy `pool_size` (ignored for SQLite) |
| Pool Max Overflow | `10` | `SUBLARR_DB_POOL_MAX_OVERFLOW` | Maximum overflow connections above `pool_size` |
| Pool Recycle | `3600` seconds | `SUBLARR_DB_POOL_RECYCLE` | Recycle connections after this many seconds to avoid stale connection errors |

### Redis (Job Queue / Cache)

Redis is optional. Without Redis, Sublarr uses an in-process queue and in-memory cache. Redis is recommended for multi-worker deployments or high-volume setups.

| Setting | Default | Env Variable | Description |
|---------|---------|-------------|-------------|
| Redis URL | *(empty)* | `SUBLARR_REDIS_URL` | Empty = no Redis. Set to `redis://host:6379/0` to enable. |
| Redis Cache Enabled | *(auto)* | `SUBLARR_REDIS_CACHE_ENABLED` | Enable Redis-backed response cache. Automatically enabled when `SUBLARR_REDIS_URL` is set. |
| Redis Queue Enabled | *(auto)* | `SUBLARR_REDIS_QUEUE_ENABLED` | Enable Redis-backed job queue. Automatically enabled when `SUBLARR_REDIS_URL` is set. |