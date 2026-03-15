---
title: Settings — Radarr
description: Connect Sublarr to one or more Radarr instances for movie library sync
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

# Settings — Radarr

![Settings — Radarr](/img/screen-settings-radarr.png)

Sublarr connects to Radarr to sync your movie library. Once connected, Radarr sends webhook events when movies are downloaded, and Sublarr resolves metadata and file paths via the Radarr API.

## Connection

| Setting | Description |
|---------|-------------|
| **Radarr URL** | Full URL including port, no trailing slash. Example: `http://192.168.178.36:7878` |
| **Radarr API Key** | Found in Radarr under **Settings → General → Security**. Stored encrypted. |

Click **Test Connection** to verify connectivity and authentication.

## Multiple Instances

Radarr supports multiple instances — useful for separate anime movie and live-action movie libraries.

Click **Add Instance** to configure additional Radarr servers. When multiple instances are configured, the legacy URL/Key fields are ignored.

## Webhook Setup

To receive real-time download events from Radarr:

1. Go to **Radarr → Settings → Connect → Add → Webhook**
2. Set the URL to: `http://<sublarr-host>:5765/api/webhooks/radarr`
3. Enable: **On Download**, **On Upgrade**, **On Rename**
4. Save

## Path Mapping

If Radarr and Sublarr use different paths for the same files, configure a path mapping in **Settings → General → Path Mapping**.

> [!TIP]
> Sublarr treats Sonarr and Radarr path mappings from the same Global mapping table — one mapping entry covers both apps if their remote paths share the same prefix.
