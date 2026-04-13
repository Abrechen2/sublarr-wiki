---
title: Settings — Integrations
description: Sonarr, Radarr, Jellyfin, and Emby integration settings
published: true
date: 2026-04-13
---

# Settings — Integrations

### Webhooks (Sonarr/Radarr)

Webhooks allow Sonarr and Radarr to notify Sublarr when new media is downloaded.

**Sonarr Webhook URL:** `http://<sublarr-host>:5765/api/v1/webhook/sonarr`
**Radarr Webhook URL:** `http://<sublarr-host>:5765/api/v1/webhook/radarr`

When triggered, Sublarr automatically:
1. Scans the new item for existing subtitles
2. Searches providers for missing subtitles
3. Downloads the best match
4. Translates if configured in the language profile
5. Notifies media servers to refresh the library

---

## Sonarr

Sublarr processes new TV episodes automatically when Sonarr sends a webhook.

### Webhook Setup

1. In Sonarr: **Settings → Connect → Add** → **Webhook**
2. Configure:
   - **Name:** Sublarr
   - **URL:** `http://sublarr:5765/api/v1/webhook/sonarr`
   - **Events:** On Import, On Upgrade
3. Click **Test** — Sonarr will send a test payload
4. Save

### Library Scan (Optional)

```
SUBLARR_SONARR_URL=http://sonarr:8989
SUBLARR_SONARR_API_KEY=your_api_key_here
```

### Multi-Instance Setup

```
SUBLARR_SONARR_INSTANCES_JSON=[
  {"name": "Anime", "url": "http://sonarr-anime:8989", "api_key": "abc123"},
  {"name": "Regular", "url": "http://sonarr:8989", "api_key": "xyz789"}
]
```

### Path Mapping

```
SUBLARR_PATH_MAPPING=/data/media=/mnt/media;/data/anime=/mnt/anime
```

---

## Radarr

1. In Radarr: **Settings → Connect → Add** → **Webhook**
2. URL: `http://sublarr:5765/api/v1/webhook/radarr`
3. Events: On Import, On Upgrade

```env
SUBLARR_RADARR_URL=http://radarr:7878
SUBLARR_RADARR_API_KEY=your_api_key_here
```

### Multi-Instance Setup

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Radarr Instances JSON | _(empty)_ | `SUBLARR_RADARR_INSTANCES_JSON` | JSON array of multiple Radarr instances |

```env
SUBLARR_RADARR_INSTANCES_JSON=[
  {"name": "Movies", "url": "http://radarr:7878", "api_key": "abc123"},
  {"name": "Anime Movies", "url": "http://radarr-anime:7878", "api_key": "xyz789"}
]
```

> [!NOTE]
> The JSON format is the same as for Sonarr multi-instance setup. Each instance has `name`, `url`, `api_key`, and an optional `path_mapping` field.

---

## Jellyfin

1. In Sublarr: **Settings → Media Servers → Add**
2. Select type: **Jellyfin**
3. Enter URL and API Key (from Jellyfin → **Dashboard → Advanced → API Keys**)

```
SUBLARR_JELLYFIN_URL=http://jellyfin:8096
SUBLARR_JELLYFIN_API_KEY=your_api_key_here
```

---

## Emby

```
SUBLARR_MEDIA_SERVERS_JSON=[
  {"type": "emby", "name": "Main", "url": "http://emby:8096", "api_key": "your_key"}
]
```

---

## Plex

```
SUBLARR_MEDIA_SERVERS_JSON=[
  {"type": "plex", "name": "Plex", "url": "http://plex:32400", "token": "your_plex_token"}
]
```

---

## Kodi

```
SUBLARR_MEDIA_SERVERS_JSON=[
  {"type": "kodi", "name": "Living Room", "url": "http://kodi-host:8080", "username": "kodi", "password": ""}
]
```

Enable JSON-RPC in Kodi: **Settings → Services → Control → Allow programs on other systems to control Kodi**

---

## AniDB Integration

AniDB ID resolution enables correct absolute episode numbering for anime series. Sublarr maps TVDB season/episode numbers to AniDB absolute episode numbers, which most fansub providers use.

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Enabled | `true` | `SUBLARR_ANIDB_ENABLED` | Enable AniDB ID resolution for absolute anime episode ordering |
| Cache TTL | `30` | `SUBLARR_ANIDB_CACHE_TTL_DAYS` | Days to cache TVDB → AniDB mappings before refreshing |
| Custom Field Name | `anidb_id` | `SUBLARR_ANIDB_CUSTOM_FIELD_NAME` | Custom field name in Sonarr for AniDB ID storage |
| Fallback to Mapping | `true` | `SUBLARR_ANIDB_FALLBACK_TO_MAPPING` | Use cached offline mapping when AniDB API is unreachable |

> [!TIP]
> AniDB integration uses a 4-tier resolution strategy: Sonarr custom field → AniDB API → TheTVDB mapping file → offline XML dump. The offline fallback ensures subtitle matching works even when external APIs are down.
