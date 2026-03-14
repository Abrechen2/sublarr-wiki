---
title: Settings — Integrations
description: Sonarr, Radarr, Jellyfin, Emby, and Plex integration settings
published: true
date: 2026-03-14T19:29:04.494Z
tags: settings
editor: markdown
dateCreated: 2026-03-14T18:04:55.960Z
---

<div class="settings-tabs">
<a class="settings-tab " href="/user-guide/settings/general"><span class="mdi mdi-cog-outline"></span> General</a>
<a class="settings-tab " href="/user-guide/settings/media-management"><span class="mdi mdi-folder-cog-outline"></span> Media Management</a>
<a class="settings-tab " href="/user-guide/settings/profiles"><span class="mdi mdi-translate"></span> Profiles</a>
<a class="settings-tab " href="/user-guide/settings/providers"><span class="mdi mdi-cloud-search-outline"></span> Providers</a>
<a class="settings-tab " href="/user-guide/settings/translation"><span class="mdi mdi-brain"></span> Translation</a>
<a class="settings-tab active" href="/user-guide/settings/integrations"><span class="mdi mdi-connection"></span> Integrations</a>
</div>

# Settings — Integrations

Configure connections to Sonarr, Radarr, and media servers.

## Sonarr

```env
SUBLARR_SONARR_URL=http://sonarr:8989
SUBLARR_SONARR_API_KEY=your_api_key_here
```

### Webhook Setup

1. In Sonarr: **Settings → Connect → Add → Webhook**
2. URL: `http://sublarr:5765/api/v1/webhook/sonarr`
3. Events: **On Import**, **On Upgrade**
4. Click **Test**, then **Save**

When triggered, Sublarr automatically scans the new item, searches providers, downloads and translates subtitles, and notifies media servers.

### Multi-Instance Setup

```env
SUBLARR_SONARR_INSTANCES_JSON=[
  {"name": "Anime", "url": "http://sonarr-anime:8989", "api_key": "abc123"},
  {"name": "Regular", "url": "http://sonarr:8989", "api_key": "xyz789"}
]
```

### Path Mapping

If Sonarr and Sublarr see different file paths:

```env
SUBLARR_PATH_MAPPING=/data/media=/mnt/media
```

Multiple mappings: `SUBLARR_PATH_MAPPING=/data/media=/mnt/media;/data/anime=/mnt/anime`

## Radarr

Identical to Sonarr but for movies.

```env
SUBLARR_RADARR_URL=http://radarr:7878
SUBLARR_RADARR_API_KEY=your_api_key_here
```

Webhook URL: `http://sublarr:5765/api/v1/webhook/radarr`

## Jellyfin

Sublarr triggers a library refresh after each subtitle download.

```env
SUBLARR_JELLYFIN_URL=http://jellyfin:8096
SUBLARR_JELLYFIN_API_KEY=your_api_key_here
```

Or via **Settings → Media Servers → Add → Jellyfin** in the UI.

## Emby / Plex / Kodi

```env
SUBLARR_MEDIA_SERVERS_JSON=[
  {"type": "emby", "name": "Main", "url": "http://emby:8096", "api_key": "your_key"},
  {"type": "plex", "name": "Plex", "url": "http://plex:32400", "token": "your_plex_token"},
  {"type": "kodi", "name": "Living Room", "url": "http://kodi-host:8080", "username": "kodi", "password": ""}
]
```

Kodi requires JSON-RPC enabled: **Settings → Services → Control → Allow programs on other systems to control Kodi**.