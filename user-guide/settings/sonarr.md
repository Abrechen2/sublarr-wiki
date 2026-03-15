---
title: Settings — Sonarr
description: Connect Sublarr to one or more Sonarr instances for TV/anime library sync
published: true
date: 2026-03-15
tags: settings
editor: markdown
dateCreated: 2026-03-15
---

<div class="settings-tabs">
<a class="settings-tab " href="/user-guide/settings/general"><span class="mdi mdi-cog-outline"></span> General</a>
<a class="settings-tab " href="/user-guide/settings/media-management"><span class="mdi mdi-folder-cog-outline"></span> Media Management</a>
<a class="settings-tab " href="/user-guide/settings/profiles"><span class="mdi mdi-translate"></span> Profiles</a>
<a class="settings-tab " href="/user-guide/settings/providers"><span class="mdi mdi-cloud-search-outline"></span> Providers</a>
<a class="settings-tab " href="/user-guide/settings/translation"><span class="mdi mdi-brain"></span> Translation</a>
<a class="settings-tab " href="/user-guide/settings/integrations"><span class="mdi mdi-connection"></span> Integrations</a>
</div>

# Settings — Sonarr

![Settings — Sonarr](/img/screen-settings-sonarr.png)

Sublarr connects to Sonarr to sync your TV and anime library. Once connected, Sonarr sends webhook events when episodes are downloaded, and Sublarr uses the Sonarr API to resolve metadata and episode paths.

## Connection

| Setting | Description |
|---------|-------------|
| **Sonarr URL** | Full URL including port, no trailing slash. Example: `http://192.168.178.36:8989` |
| **Sonarr API Key** | Found in Sonarr under **Settings → General → Security**. Stored encrypted. |

Click **Test Connection** to verify — a green checkmark confirms Sublarr can reach Sonarr and authenticate.

> [!NOTE]
> The connection status indicator (Configured / Not configured) updates automatically after a successful test.

## Multiple Instances

Sublarr supports multiple Sonarr instances — useful if you run separate instances for anime and live-action TV.

Click **Add Instance** to add a second instance. Each instance has its own URL, API key, and an optional tag filter (only sync series with a specific Sonarr tag).

When multiple instances are configured, the legacy URL/Key fields above are ignored. All instances are polled for library sync and webhooks.

## Webhook Setup

To receive real-time events from Sonarr, add a webhook in Sonarr:

1. Go to **Sonarr → Settings → Connect → Add → Webhook**
2. Set the URL to: `http://<sublarr-host>:5765/api/webhooks/sonarr`
3. Enable: **On Download**, **On Upgrade**, **On Rename**
4. Save

> [!TIP]
> If Sonarr and Sublarr run in separate Docker containers, use the Docker bridge network hostname (e.g. `http://sublarr:5765`) rather than `localhost`.

## Path Mapping

If Sonarr uses different paths to access your media files than Sublarr does (common when one runs on Windows and the other in Docker), configure a path mapping in **Settings → General → Path Mapping**.

Example:
- Sonarr sees: `/data/media/anime/`
- Sublarr sees: `/media/anime/`
- Mapping: Remote Path = `/data/media` → Local Path = `/media`
