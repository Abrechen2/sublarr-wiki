---
title: Settings — General
description: General application settings — host, security, authentication, UI, backups
published: true
date: 2026-03-14T19:27:33.844Z
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

This page covers the UI-configurable settings in **Settings → General**. For the full environment variable reference, see [Configuration Reference](/getting-started/environment-variables).

## Host & Port

| Setting | Default | Env Variable | Description |
|---------|---------|-------------|-------------|
| Port | `5765` | `SUBLARR_PORT` | HTTP port — change if the port is already in use on your host |
| URL Base | *(empty)* | — | Set if running behind a reverse proxy at a subpath (e.g. `/sublarr`). Do not include a trailing slash. |
| Log Level | `INFO` | `SUBLARR_LOG_LEVEL` | Verbosity: `DEBUG`, `INFO`, `WARNING`, `ERROR` |

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

## Analytics

Sublarr does not collect analytics or telemetry. No usage data leaves your server.