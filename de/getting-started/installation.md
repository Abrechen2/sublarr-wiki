---
title: Installation
description: Sublarr mit Docker oder Docker Compose installieren
published: true
date: 2026-03-14
---

# Installation

## Schnellstart

### Voraussetzungen

- **Docker** oder Docker Compose (empfohlen)
- **Medienbibliothek**, die über das Dateisystem erreichbar ist
- **(Optional)** Sonarr und/oder Radarr zur Bibliotheksverwaltung
- **(Optional)** Ollama oder ein anderes Übersetzungs-Backend für LLM-basierte Untertitelübersetzung

### Docker Compose (Empfohlen)

Eine `docker-compose.yml` erstellen:

```yaml
services:
  sublarr:
    image: ghcr.io/abrechen2/sublarr:0.19.2-beta
    container_name: sublarr
    ports:
      - "5765:5765"
    volumes:
      - ./config:/config        # Anwendungskonfiguration und Datenbank
      - /path/to/media:/media:rw  # Medienbibliothek
    environment:
      - PUID=1000               # Benutzer-ID für Dateiberechtigungen
      - PGID=1000               # Gruppen-ID für Dateiberechtigungen
    restart: unless-stopped
```

Starten:

```bash
docker compose up -d
```

Anschließend `http://localhost:5765` im Browser öffnen. Der Einrichtungsassistent führt durch die Erstkonfiguration.

### Unraid

1. Im Unraid-Webinterface zum Tab **Apps** wechseln
2. In den Community Applications nach "Sublarr" suchen
3. **Install** klicken
4. Das Template konfigurieren:
   - **Config Path:** `/mnt/user/appdata/sublarr`
   - **Media Path:** Der eigene Medien-Share (z. B. `/mnt/user/media`)
   - **Sonarr/Radarr URLs und API Keys** (optional)
   - **Ollama URL** (falls Übersetzung genutzt wird)
5. **Apply** klicken

### Umgebungsvariablen

Alle Einstellungen verwenden das Präfix `SUBLARR_`. Wichtige Variablen:

| Variable | Standard | Beschreibung |
|----------|----------|--------------|
| `SUBLARR_PORT` | `5765` | Web-UI-Port |
| `SUBLARR_API_KEY` | *(leer)* | Optionaler API-Key zur Authentifizierung |
| `SUBLARR_MEDIA_PATH` | `/media` | Stammverzeichnis der Medienbibliothek |
| `SUBLARR_SONARR_URL` | *(leer)* | Sonarr-Instanz-URL |
| `SUBLARR_SONARR_API_KEY` | *(leer)* | Sonarr-API-Key |
| `SUBLARR_RADARR_URL` | *(leer)* | Radarr-Instanz-URL |
| `SUBLARR_RADARR_API_KEY` | *(leer)* | Radarr-API-Key |
| `SUBLARR_OLLAMA_URL` | `http://localhost:11434` | Ollama-Server-URL |
| `SUBLARR_OLLAMA_MODEL` | `qwen2.5:14b-instruct` | Ollama-Modell für Übersetzung |
| `SUBLARR_SOURCE_LANGUAGE` | `en` | Quellsprache der Untertitel |
| `SUBLARR_TARGET_LANGUAGE` | `de` | Zielsprache der Übersetzung |

Die meiste Konfiguration erfolgt über die Web-UI nach der Ersteinrichtung. Umgebungsvariablen liefern Standardwerte, die in den Einstellungen überschrieben werden können.

## Setup-Szenarien

### Szenario 1: Sonarr + Radarr (Empfohlen)

Das häufigste Setup — Sublarr überwacht die Sonarr-/Radarr-Bibliotheken und verwaltet Untertitel automatisch.

**Schritt 1: Sonarr konfigurieren**

1. In den Sublarr-Einstellungen zum Tab **Sonarr** wechseln
2. Die Sonarr-URL eingeben (z. B. `http://192.168.1.100:8989`)
3. Den Sonarr-API-Key eingeben (zu finden unter Sonarr > Settings > General)
4. **Test** klicken, um die Verbindung zu prüfen
5. **Path Mapping:** Falls Sonarr Medien unter einem anderen Pfad sieht als Sublarr (häufig bei Docker), Path Mappings konfigurieren:
   - Remote Path: `/tv` (Pfad in Sonarr)
   - Local Path: `/media/tv` (Pfad in Sublarr)

**Schritt 2: Radarr konfigurieren** (optional, für Filme)

1. Gleicher Ablauf wie bei Sonarr im Tab **Radarr**
2. URL, API-Key und Path Mappings

**Schritt 3: Webhooks einrichten**

Webhooks ermöglichen die Echtzeitverarbeitung von Untertiteln, sobald neue Medien heruntergeladen werden.

In **Sonarr:**
1. Settings > Connect öffnen
2. Einen neuen Webhook hinzufügen
3. URL: `http://sublarr:5765/api/v1/webhook/sonarr`
4. Trigger: Import, Upgrade

In **Radarr:**
1. Settings > Connect öffnen
2. Einen neuen Webhook hinzufügen
3. URL: `http://sublarr:5765/api/v1/webhook/radarr`
4. Trigger: Import, Upgrade

**Schritt 4: Sprachprofile erstellen**

1. Zu Settings > Advanced > Language Profiles wechseln
2. Ein Profil erstellen (z. B. „Anime Deutsch"):
   - Source Language: Englisch (oder Japanisch für Anime)
   - Target Languages: Deutsch
   - Translation Backend: Ollama (oder das bevorzugte Backend)
   - Forced Preference: Separate (für Signs/Songs)
3. Profile in der Bibliotheksansicht den Serien zuweisen

**Schritt 5: Provider aktivieren**

1. Zu Settings > Providers wechseln
2. Gewünschte Provider aktivieren und API-Keys eingeben, wo erforderlich:
   - AnimeTosho: Kein API-Key nötig (ideal für Anime)
   - OpenSubtitles: API-Key erforderlich (beste allgemeine Abdeckung)
   - SubDL: API-Key erforderlich (Subscene-Nachfolger)
   - Jimaku: API-Key erforderlich (Anime-fokussiert)
3. Provider-Prioritäten anpassen (höhere Priorität = wird zuerst durchsucht)

### Szenario 2: Standalone-Modus (Ohne *arr-Apps)

Sublarr ohne Sonarr oder Radarr nutzen, indem direkt auf Medienordner verwiesen wird.

**Schritt 1: Bibliotheksquellen konfigurieren**

1. Zu Settings > Advanced > Library Sources wechseln
2. **Add Watched Folder** klicken
3. Den Ordnerpfad eingeben (muss im Container erreichbar sein, z. B. `/media/anime`)
4. Inhaltstyp wählen: TV Shows, Movies oder Mixed
5. Auto-scan für automatische Dateierkennung aktivieren

**Schritt 2: Metadaten-Provider konfigurieren**

Im Standalone-Modus werden Metadaten-Provider zur Identifikation der Medien benötigt:

1. **AniList** (immer verfügbar, kein API-Key): Am besten für Anime-Erkennung
2. **TMDB** (API-Key erforderlich): Am besten für allgemeine Filme und TV-Serien
   - Kostenlosen API-Key unter https://www.themoviedb.org/settings/api beziehen
   - Unter Settings > Library Sources > TMDB API Key eintragen
3. **TVDB** (API-Key erforderlich): Alternative TV-Serien-Metadaten
   - API-Key unter https://thetvdb.com/dashboard/account/apikey beziehen

**Schritt 3: Erster Scan**

1. Nach der Konfiguration der Watched Folders auf der Library-Sources-Seite **Scan Now** klicken
2. Sublarr wird:
   - Alle Mediendateien in den Ordnern erkennen
   - Dateinamen mit `guessit` parsen (Anime-optimiert)
   - Metadaten von den konfigurierten Providern abrufen
   - Dateien in Serien/Filme gruppieren
   - Einträge ohne Untertitel zur Wanted-Liste hinzufügen

**Schritt 4: Laufende Überwachung**

Der `MediaFileWatcher` überwacht die Ordner kontinuierlich auf neue Dateien. Bei Erkennung:
1. Datei-Stabilitätsprüfung (wartet auf Download-Abschluss)
2. Dateiname parsen und Metadaten nachschlagen
3. Automatische Aufnahme in die Wanted-Liste
4. Untertitelsuche und Download (falls Auto-Search aktiviert)

### Szenario 3: Mischbetrieb

Sonarr-/Radarr-Integration und Standalone-Modus gleichzeitig betreiben.

1. Sonarr/Radarr wie in Szenario 1 konfigurieren
2. Watched Folders für Medien hinzufügen, die nicht über *arr-Apps verwaltet werden
3. Beide Quellen speisen in dieselbe Wanted-Pipeline
4. Einträge werden mit ihrer Quelle gekennzeichnet (sonarr/radarr/standalone)
