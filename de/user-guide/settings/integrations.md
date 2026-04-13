---
title: Settings — Integrationen
description: Sonarr-, Radarr-, Jellyfin- und Emby-Integrationseinstellungen
published: true
date: 2026-03-14
---

# Settings — Integrationen

### Webhooks (Sonarr/Radarr)

Webhooks ermöglichen es Sonarr und Radarr, Sublarr zu benachrichtigen, wenn neue Medien heruntergeladen werden.

**Sonarr Webhook URL:** `http://<sublarr-host>:5765/api/v1/webhook/sonarr`
**Radarr Webhook URL:** `http://<sublarr-host>:5765/api/v1/webhook/radarr`

Beim Auslösen führt Sublarr automatisch folgende Schritte aus:
1. Das neue Element auf vorhandene Untertitel scannen
2. Provider nach fehlenden Untertiteln durchsuchen
3. Den besten Treffer herunterladen
4. Übersetzen, falls im Sprachprofil konfiguriert
5. Medienserver zur Bibliotheksaktualisierung benachrichtigen

---

## Sonarr

Sublarr verarbeitet neue TV-Episoden automatisch, sobald Sonarr einen Webhook sendet.

### Webhook-Einrichtung

1. In Sonarr: **Settings → Connect → Add** → **Webhook**
2. Konfigurieren:
   - **Name:** Sublarr
   - **URL:** `http://sublarr:5765/api/v1/webhook/sonarr`
   - **Events:** On Import, On Upgrade
3. **Test** klicken — Sonarr sendet eine Test-Payload
4. Speichern

### Bibliotheksscan (Optional)

```
SUBLARR_SONARR_URL=http://sonarr:8989
SUBLARR_SONARR_API_KEY=eigener_api_key
```

### Multi-Instance-Setup

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

```
SUBLARR_RADARR_URL=http://radarr:7878
SUBLARR_RADARR_API_KEY=eigener_api_key
```

---

## Jellyfin

1. In Sublarr: **Settings → Media Servers → Add**
2. Typ auswählen: **Jellyfin**
3. URL und API Key eingeben (aus Jellyfin → **Dashboard → Advanced → API Keys**)

```
SUBLARR_JELLYFIN_URL=http://jellyfin:8096
SUBLARR_JELLYFIN_API_KEY=eigener_api_key
```

---

## Emby

```
SUBLARR_MEDIA_SERVERS_JSON=[
  {"type": "emby", "name": "Main", "url": "http://emby:8096", "api_key": "eigener_key"}
]
```

---

## Plex

```
SUBLARR_MEDIA_SERVERS_JSON=[
  {"type": "plex", "name": "Plex", "url": "http://plex:32400", "token": "eigenes_plex_token"}
]
```

---

## Kodi

```
SUBLARR_MEDIA_SERVERS_JSON=[
  {"type": "kodi", "name": "Wohnzimmer", "url": "http://kodi-host:8080", "username": "kodi", "password": ""}
]
```

JSON-RPC in Kodi aktivieren: **Settings → Services → Control → Allow programs on other systems to control Kodi**
