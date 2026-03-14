---
title: Settings — General
description: General application settings — host, security, authentication, UI, backups
published: true
date: 2026-03-14
---

# Settings — General

This page covers the UI-configurable settings in **Settings → General**. For environment variable configuration, see [Environment Variables](/getting-started/environment-variables).

## Host & Port

| Setting | Default | Description |
|---------|---------|-------------|
| Host | `0.0.0.0` | Bind address — keep default unless on a multi-NIC server |
| Port | `5765` | HTTP port — change if port is already in use |
| URL Base | _(empty)_ | Set if running behind a reverse proxy at a subpath (e.g. `/sublarr`) |

## Authentication

| Setting | Default | Description |
|---------|---------|-------------|
| Authentication enabled | `false` | Enable single-account login |
| Username | `admin` | Login username |
| Password | _(set on first run)_ | Login password |

See [Login Setup](/getting-started/quick-start#authentication) for the full setup flow.

## Updates

Sublarr checks GitHub releases for newer versions. Notification appears in the sidebar when an update is available. Auto-update is not supported — pull the new Docker image manually.

## Backups

Sublarr automatically backs up its SQLite database to `/config/backups/` on a configurable schedule. Default: daily, keep 7 backups. Restore by replacing `/config/sublarr.db` and restarting the container.

## Analytics

Sublarr does not collect analytics or telemetry. No data leaves your server.
