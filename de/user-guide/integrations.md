---
title: Integrationen
description: Sublarr mit Sonarr, Radarr, Jellyfin und Emby verbinden
published: true
date: 2026-03-14
---

# Integrationen

Einrichtungsanleitungen für die Verbindung von Sublarr mit Sonarr, Radarr, Jellyfin, Emby, Plex, Kodi und Ollama.

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

Um die Sonarr-Bibliothek nach fehlenden Untertiteln zu scannen:

```
SUBLARR_SONARR_URL=http://sonarr:8989
SUBLARR_SONARR_API_KEY=eigener_api_key
```

Den API-Key unter Sonarr → **Settings → General → API Key** finden.

### Multi-Instance-Setup

Für mehrere Sonarr-Instanzen:

```
SUBLARR_SONARR_INSTANCES_JSON=[
  {"name": "Anime", "url": "http://sonarr-anime:8989", "api_key": "abc123"},
  {"name": "Regular", "url": "http://sonarr:8989", "api_key": "xyz789"}
]
```

### Path Mapping

Falls Sonarr und Sublarr unterschiedliche Dateipfade sehen:

```
SUBLARR_PATH_MAPPING=/data/media=/mnt/media
```

Mehrere Mappings durch Semikolon getrennt:

```
SUBLARR_PATH_MAPPING=/data/media=/mnt/media;/data/anime=/mnt/anime
```

---

## Radarr

Identisch zu Sonarr, aber für Filme.

1. In Radarr: **Settings → Connect → Add** → **Webhook**
2. URL: `http://sublarr:5765/api/v1/webhook/radarr`
3. Events: On Import, On Upgrade

```
SUBLARR_RADARR_URL=http://radarr:7878
SUBLARR_RADARR_API_KEY=eigener_api_key
```

---

## Jellyfin

Sublarr löst nach jedem Untertitel-Download eine Bibliotheksaktualisierung aus, damit Jellyfin neue Dateien sofort erkennt.

### Einrichtung

1. In Sublarr: **Settings → Media Servers → Add**
2. Typ auswählen: **Jellyfin**
3. Eingeben:
   - URL: `http://jellyfin:8096`
   - API Key: aus Jellyfin → **Dashboard → Advanced → API Keys**

Oder per Umgebungsvariable:

```
SUBLARR_JELLYFIN_URL=http://jellyfin:8096
SUBLARR_JELLYFIN_API_KEY=eigener_api_key
```

---

## Emby

Gleicher Ablauf wie bei Jellyfin — das Multi-Server-JSON-Format verwenden:

```
SUBLARR_MEDIA_SERVERS_JSON=[
  {"type": "emby", "name": "Main", "url": "http://emby:8096", "api_key": "eigener_key"}
]
```

---

## Plex

Sublarr nutzt `plexapi`, um nach Untertiteländerungen einen Bibliotheksscan auszulösen.

```
SUBLARR_MEDIA_SERVERS_JSON=[
  {"type": "plex", "name": "Plex", "url": "http://plex:32400", "token": "eigenes_plex_token"}
]
```

Plex-Token finden: Plex Support > Finding an Authentication Token (X-Plex-Token)

---

## Kodi

Sublarr sendet nach Untertiteländerungen einen `VideoLibrary.Scan`-JSON-RPC-Aufruf.

```
SUBLARR_MEDIA_SERVERS_JSON=[
  {"type": "kodi", "name": "Wohnzimmer", "url": "http://kodi-host:8080", "username": "kodi", "password": ""}
]
```

JSON-RPC in Kodi aktivieren: **Settings → Services → Control → Allow programs on other systems to control Kodi**

---

## Ollama (Lokales LLM)

Sublarr nutzt Ollama für die Untertitelübersetzung. Keine Daten verlassen das Netzwerk.

### Ollama installieren

```bash
# Linux / macOS
curl -fsSL https://ollama.ai/install.sh | sh
ollama pull qwen2.5:14b-instruct
```

### Sublarr konfigurieren

```
SUBLARR_OLLAMA_URL=http://host.docker.internal:11434
SUBLARR_OLLAMA_MODEL=qwen2.5:14b-instruct
```

### Empfohlene Modelle

| Modell | VRAM | Qualität | Geschwindigkeit |
|---|---|---|---|
| `qwen2.5:14b-instruct` | ~9 GB | Hervorragend | Mittel |
| `qwen2.5:7b-instruct` | ~5 GB | Sehr gut | Schnell |
| `gemma3:12b` | ~8 GB | Gut | Mittel |
| `mistral:7b-instruct` | ~5 GB | Gut | Schnell |

### Weitere Übersetzungs-Backends

Sublarr unterstützt außerdem:

- **DeepL** — hochwertige maschinelle Übersetzung mit Glossar-Unterstützung
- **LibreTranslate** — selbstgehostete, kostenlose Übersetzung
- **OpenAI-kompatibel** — jeder OpenAI-API-Endpunkt
- **Google Cloud Translation** — Google Translate API

Konfiguration unter **Settings → Translation → Translation Backends**.

---

## Apprise-Benachrichtigungen

Sublarr kann Benachrichtigungen über Apprise versenden, das über 80 Dienste unterstützt.

```
SUBLARR_NOTIFICATION_URLS_JSON=["pover://UserKey@AppToken", "discord://WebhookID/Token"]
```

Dienst-Beispiele:

```
pover://UserKey@AppToken           # Pushover
discord://WebhookID/WebhookToken   # Discord
tgram://BotToken/ChatID            # Telegram
slack://TokenA/TokenB/TokenC       # Slack
ntfy://ntfy.sh/topic               # ntfy
gotify://hostname/token            # Gotify
```

---

## Event Hooks

Sublarr unterstützt zwei Arten eigener Automatisierungstrigger: Shell Hooks und ausgehende Webhooks.

### Shell Hooks

Shell-Skripte in `/config/hooks/` ablegen. Skripte werden ausgeführt, wenn konfigurierte Events eintreten.

Konfiguration unter **Settings → Events & Hooks → Shell Hooks**:
- Event auswählen (z. B. `subtitle_downloaded`)
- Auf das Skript verweisen: `/config/hooks/notify.sh`
- Event-Daten werden als Umgebungsvariablen übergeben (z. B. `SUBLARR_FILE_PATH`, `SUBLARR_PROVIDER`, `SUBLARR_LANGUAGE`)

### Ausgehende Webhooks

HTTP-POST-Requests an externe Systeme bei Events senden.

Konfiguration unter **Settings → Events & Hooks → Outgoing Webhooks**:
- URL: eigener Endpunkt (z. B. `http://n8n:5678/webhook/sublarr`)
- Events: ein oder mehrere auswählen
- Payload: JSON-Body mit Event-Daten

**Verfügbare Events:**
- `subtitle_downloaded` — Untertitel erfolgreich heruntergeladen
- `translation_complete` — Übersetzungsjob abgeschlossen
- `provider_failed` — Provider hat einen Fehler zurückgegeben
- `wanted_item_added` — neuer Eintrag zur Wanted-Liste hinzugefügt
- `config_updated` — Einstellungen geändert
- `batch_complete` — Batch-Suche oder -Übersetzung abgeschlossen
- `upgrade_performed` — Untertitel auf bessere Version aktualisiert

### API

```
GET    /api/v1/hooks           Konfigurierte Hooks auflisten
POST   /api/v1/hooks           Hook erstellen
PUT    /api/v1/hooks/:id       Hook aktualisieren
DELETE /api/v1/hooks/:id       Hook löschen
```

---

## Standalone-Modus (Ohne Sonarr/Radarr)

Falls Sonarr oder Radarr nicht verwendet werden, kann Sublarr einen Ordner direkt überwachen.

```
SUBLARR_STANDALONE_ENABLED=true
SUBLARR_STANDALONE_SCAN_INTERVAL_HOURS=6
SUBLARR_TMDB_API_KEY=eigener_tmdb_key
```

Sublarr überwacht `SUBLARR_MEDIA_PATH`, erkennt neue Videodateien, schlägt Metadaten über TMDB/AniList/TVDB nach und reiht sie zur Untertitelsuche ein.
