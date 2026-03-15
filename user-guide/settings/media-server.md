---
title: Settings — Media Server
description: Connect Sublarr to Jellyfin or Emby for subtitle refresh notifications
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

# Settings — Media Server

![Settings — Media Server](/img/screen-settings-media-server.png)

Sublarr can notify Jellyfin or Emby to refresh their subtitle metadata whenever a subtitle is downloaded, translated, or deleted. Without this integration, your media server may not display new subtitles until its next scheduled library scan.

## Supported Media Servers

| Server | Type | Notes |
|--------|------|-------|
| Jellyfin | Jellyfin / Emby | Uses the Jellyfin API (`/Items/{id}/Refresh`) |
| Emby | Jellyfin / Emby | API-compatible with Jellyfin |

## Adding a Server

Click **Add Server** to configure a new media server connection. Each server entry has:

| Setting | Description |
|---------|-------------|
| **Type** | Select: Jellyfin or Emby |
| **Name** | Display name (e.g. "Jellyfin Home") |
| **Host URL** | Full URL including port, no trailing slash. Example: `http://192.168.178.36:8096` |
| **API Key** | Found in Jellyfin/Emby under **Dashboard → API Keys → Add**. |
| **Enabled** | Toggle to temporarily disable without deleting the configuration |

## How It Works

When Sublarr finishes a subtitle operation (download, translation, deletion), it calls the Jellyfin/Emby API to trigger a metadata refresh for the affected item. This causes the media server to re-scan the episode's subtitle tracks immediately.

> [!TIP]
> Multiple media servers can be configured simultaneously — useful if you run both Jellyfin and Emby, or have separate Jellyfin instances for different library sections.

> [!NOTE]
> Sublarr matches items by their file path. Path mapping (configured in **Settings → General → Path Mapping**) must be set correctly for Sublarr's paths to resolve to the correct Jellyfin/Emby library items.
