---
title: Umgebungsvariablen
description: Alle SUBLARR_*-Umgebungsvariablen und Konfigurationsoptionen
published: true
date: 2026-03-14
---

# Konfigurationsreferenz

## Schnellstart — minimale `.env`

Nur wenige Einstellungen müssen in der `.env`-Datei stehen. Alles andere
wird in der **Settings-UI** konfiguriert, sobald der Container läuft
(`http://localhost:<SUBLARR_PORT>`).

```env
# Docker / Compose
VERSION=0.1.0      # muss backend/VERSION entsprechen
PUID=1000
PGID=1000

# Server
SUBLARR_PORT=5765

# Pfade (müssen zu den Volume-Mounts in docker-compose.yml passen)
SUBLARR_MEDIA_PATH=/media
SUBLARR_DB_PATH=/config/sublarr.db

# LLM-Dienstendpunkt (Infrastruktur — nicht in der Settings-UI)
SUBLARR_OLLAMA_URL=http://ollama:11434

# Optional: API-Key-Authentifizierung
# SUBLARR_API_KEY=
```

`.env.example` nach `.env` kopieren und los geht's.

---

## Wie Einstellungen funktionieren

Alle `SUBLARR_`-Variablen können über drei Ebenen gesetzt werden (höchste Priorität zuerst):

1. **Umgebungsvariable / `.env`** — für Infrastruktur und Secrets
2. **Settings-UI** — Laufzeitkonfiguration in der DB-Tabelle `config_entries`
3. **Eingebauter Standard** — sicherer Wert für jedes Feld

Die folgenden Tabellen bilden die vollständige Referenz. Die Spalte **UI** zeigt an,
ob eine Einstellung auch in der Settings-UI verfügbar ist (und daher für den Alltag
**nicht** in `.env` stehen muss).

---

## Core

| Variable | Standard | UI | Beschreibung |
|---|---|:---:|---|
| `SUBLARR_MEDIA_PATH` | `/media` | ✅ | Stammpfad der Medienbibliothek |
| `SUBLARR_DB_PATH` | `/config/sublarr.db` | ✅ | Speicherort der SQLite-Datenbank |
| `SUBLARR_DATABASE_URL` | *(leer)* | — | Vollständige SQLAlchemy-URL (z. B. `postgresql://...`). Überschreibt `DB_PATH`, wenn gesetzt |
| `SUBLARR_PORT` | `5765` | ✅ | HTTP-Port |
| `SUBLARR_API_KEY` | *(leer)* | ✅ | Optionaler API-Key für Authentifizierung (`X-Api-Key`-Header). Leer = keine Authentifizierung |
| `SUBLARR_CORS_ORIGINS` | `http://localhost:5173,http://localhost:5765` | — | Erlaubte CORS-/WebSocket-Origins (kommasepariert). `*` nur in vertrauenswürdigen Umgebungen setzen |
| `SUBLARR_LOG_LEVEL` | `INFO` | ✅ | Log-Level: `DEBUG`, `INFO`, `WARNING`, `ERROR` |
| `SUBLARR_LOG_FILE` | `log/sublarr.log` | — | Log-Dateipfad. Docker-Standard: `/config/sublarr.log` |
| `SUBLARR_LOG_FORMAT` | `text` | — | Log-Format: `text` oder `json` (strukturiert für Log-Aggregation) |
| `PUID` / `PGID` | `1000` | — | Container-Benutzer-/Gruppen-IDs für Volume-Dateiberechtigungen |

---

## Übersetzung

| Variable | Standard | UI | Beschreibung |
|---|---|:---:|---|
| `SUBLARR_OLLAMA_URL` | `http://localhost:11434` | — | Ollama-Basis-URL — in `.env` setzen (Infrastruktur-Endpunkt) |
| `SUBLARR_OLLAMA_MODEL` | `qwen2.5:14b-instruct` | — | Standardmodell für Übersetzung. Empfohlen: `hf.co/Sublarr/anime-translator-v6-GGUF:Q4_K_M` (eigenes feinjustiertes Modell, EN→DE Anime-Untertitel — siehe [huggingface.co/Sublarr](https://huggingface.co/Sublarr)) |
| `SUBLARR_SOURCE_LANGUAGE` | `en` | ✅ | Quellsprache der Untertitel |
| `SUBLARR_TARGET_LANGUAGE` | `de` | ✅ | Standard-Zielsprache |
| `SUBLARR_SOURCE_LANGUAGE_NAME` | `English` | ✅ | Lesbarer Name der Quellsprache (in Prompts verwendet) |
| `SUBLARR_TARGET_LANGUAGE_NAME` | `German` | ✅ | Lesbarer Name der Zielsprache (in Prompts verwendet) |
| `SUBLARR_BATCH_SIZE` | `15` | — | Untertitel-Cues pro LLM-Aufruf |
| `SUBLARR_REQUEST_TIMEOUT` | `90` | — | LLM-Request-Timeout in Sekunden |
| `SUBLARR_TEMPERATURE` | `0.3` | — | LLM-Temperature (niedriger = konsistenter) |
| `SUBLARR_MAX_RETRIES` | `3` | — | Maximale Wiederholungsversuche bei LLM-Fehler |
| `SUBLARR_PROMPT_TEMPLATE` | *(leer)* | — | Eigenes Prompt-Template. Leer = automatisch generiert |
| `SUBLARR_TRANSLATION_MAX_WORKERS` | `4` | — | Parallele Worker-Threads im Thread-Pool der Job-Queue. Erhöhen für höheren Übersetzungsdurchsatz; verringern bei begrenztem Arbeitsspeicher. Gilt für `MemoryJobQueue` (In-Process); mit Redis+RQ stattdessen Worker via `--scale rq-worker=N` skalieren |

---

## Provider-System

| Variable | Standard | UI | Beschreibung |
|---|---|:---:|---|
| `SUBLARR_PROVIDER_PRIORITIES` | `animetosho,jimaku,opensubtitles,subdl` | — | Suchreihenfolge der Provider (kommasepariert) |
| `SUBLARR_PROVIDERS_ENABLED` | *(leer)* | — | Explizite Liste aktivierter Provider. Leer = alle registrierten |
| `SUBLARR_PROVIDER_SEARCH_TIMEOUT` | `30` | — | Globaler Fallback-Timeout pro Provider (Sekunden) |
| `SUBLARR_PROVIDER_CACHE_TTL_MINUTES` | `5` | — | Cache-TTL für Provider-Suchergebnisse |
| `SUBLARR_PROVIDER_AUTO_PRIORITIZE` | `true` | — | Provider automatisch nach Erfolgsrate priorisieren |
| `SUBLARR_PROVIDER_RATE_LIMIT_ENABLED` | `true` | — | Pro-Provider-Rate-Limiting aktivieren |
| `SUBLARR_PROVIDER_DYNAMIC_TIMEOUT_ENABLED` | `true` | — | Antwortzeit-Historie für dynamische Timeouts nutzen |
| `SUBLARR_PROVIDER_DYNAMIC_TIMEOUT_MULTIPLIER` | `3.0` | — | Timeout = durchschnittliche_Antwortzeit × Multiplikator |
| `SUBLARR_PROVIDER_DYNAMIC_TIMEOUT_MIN_SECS` | `5` | — | Minimaler dynamischer Timeout |
| `SUBLARR_PROVIDER_DYNAMIC_TIMEOUT_MAX_SECS` | `30` | — | Maximaler dynamischer Timeout |

### Provider-API-Keys

Provider-Zugangsdaten können unter **Settings → Providers** (Tab API Keys) eingegeben werden und
werden verschlüsselt in der Datenbank gespeichert. Umgebungsvariablen überschreiben UI-Werte, wenn gesetzt.

| Variable | UI | Provider |
|---|:---:|---|
| `SUBLARR_OPENSUBTITLES_API_KEY` | ✅ | [OpenSubtitles](https://www.opensubtitles.com/en/consumers) |
| `SUBLARR_OPENSUBTITLES_USERNAME` | ✅ | OpenSubtitles-Benutzername |
| `SUBLARR_OPENSUBTITLES_PASSWORD` | ✅ | OpenSubtitles-Passwort |
| `SUBLARR_JIMAKU_API_KEY` | ✅ | [Jimaku](https://jimaku.cc/) |
| `SUBLARR_SUBDL_API_KEY` | ✅ | [SubDL](https://subdl.com/) |

AnimeTosho, Gestdown, Podnapisi und Titrari funktionieren ohne API-Key.

---

## Sonarr & Radarr

Konfiguration unter **Settings → Sonarr / Radarr**. Umgebungsvariablen sind nur für
geskriptete/Headless-Deployments nötig.

| Variable | Standard | UI | Beschreibung |
|---|---|:---:|---|
| `SUBLARR_SONARR_URL` | *(leer)* | ✅ | Sonarr-Basis-URL |
| `SUBLARR_SONARR_API_KEY` | *(leer)* | ✅ | Sonarr-API-Key |
| `SUBLARR_SONARR_INSTANCES_JSON` | *(leer)* | — | JSON-Array mehrerer Sonarr-Instanzen |
| `SUBLARR_RADARR_URL` | *(leer)* | ✅ | Radarr-Basis-URL |
| `SUBLARR_RADARR_API_KEY` | *(leer)* | ✅ | Radarr-API-Key |
| `SUBLARR_RADARR_INSTANCES_JSON` | *(leer)* | — | JSON-Array mehrerer Radarr-Instanzen |
| `SUBLARR_PATH_MAPPING` | *(leer)* | — | Pfad-Remapping. Format: `remote=local;remote2=local2` |

Multi-Instance-JSON-Format:

```json
[{"name": "Main", "url": "http://sonarr:8989", "api_key": "abc123"}]
```

---

## Medienserver

Konfiguration unter **Settings → Media Servers**. Die UI ist gegenüber Umgebungsvariablen
vorzuziehen — Multi-Instance-Konfiguration ist dort einfacher zu verwalten.

| Variable | Standard | UI | Beschreibung |
|---|---|:---:|---|
| `SUBLARR_JELLYFIN_URL` | *(leer)* | — | Jellyfin-Basis-URL (Legacy-Einzelinstanz) |
| `SUBLARR_JELLYFIN_API_KEY` | *(leer)* | — | Jellyfin-API-Key (Legacy) |
| `SUBLARR_MEDIA_SERVERS_JSON` | *(leer)* | ✅ | JSON-Array aller Medienserver-Instanzen |

Medienserver-JSON-Format:

```json
[
  {"type": "jellyfin", "name": "Main", "url": "http://jellyfin:8096", "api_key": "abc123"},
  {"type": "plex", "name": "Plex", "url": "http://plex:32400", "token": "xyz789"},
  {"type": "kodi", "name": "Wohnzimmer", "url": "http://kodi:8080", "username": "kodi", "password": ""}
]
```

---

## Wanted-System & Automatisierung

Konfiguration unter **Settings → Automation**. Alle benutzerseitigen Schalter und
Intervalle sind dort verfügbar; die folgenden Tuning-Parameter bleiben rein umgebungsvariablenbasiert.

| Variable | Standard | UI | Beschreibung |
|---|---|:---:|---|
| `SUBLARR_WANTED_SCAN_INTERVAL_HOURS` | `0` | ✅ | Wie oft nach fehlenden Untertiteln gescannt wird. `0` = deaktiviert (ereignisgesteuert via Webhooks) |
| `SUBLARR_WANTED_SCAN_ON_STARTUP` | `false` | ✅ | Wanted-Scan beim Containerstart ausführen |
| `SUBLARR_WANTED_ANIME_ONLY` | `true` | ✅ | Nur Anime-Serien scannen |
| `SUBLARR_WANTED_ANIME_MOVIES_ONLY` | `false` | ✅ | Radarr-Filme nach Anime-Tag filtern (unabhängig von `WANTED_ANIME_ONLY`) |
| `SUBLARR_WANTED_MAX_SEARCH_ATTEMPTS` | `3` | ✅ | Maximale Provider-Suchversuche pro Wanted-Eintrag |
| `SUBLARR_WANTED_AUTO_EXTRACT` | `false` | ✅ | Eingebettete Untertitel während des Wanted-Scans automatisch extrahieren |
| `SUBLARR_WANTED_AUTO_TRANSLATE` | `false` | ✅ | Nach automatischer Extraktion im Wanted-Scan automatisch übersetzen |
| `SUBLARR_WANTED_SKIP_SRT_ON_NO_ASS` | `true` | — | SRT-Schritte überspringen, wenn in Schritt 1+2 kein ASS gefunden wurde |
| `SUBLARR_WANTED_SEARCH_INTERVAL_HOURS` | `24` | ✅ | Automatisches Suchintervall. `0` = deaktiviert |
| `SUBLARR_WANTED_SEARCH_ON_STARTUP` | `false` | ✅ | Wanted-Suche beim Containerstart ausführen |
| `SUBLARR_WANTED_SEARCH_MAX_ITEMS_PER_RUN` | `50` | ✅ | Maximale Einträge pro Scheduler-Durchlauf |
| `SUBLARR_WANTED_ADAPTIVE_BACKOFF_ENABLED` | `true` | — | Exponentielles Backoff für wiederholt fehlschlagende Einträge |
| `SUBLARR_WANTED_BACKOFF_BASE_HOURS` | `1.0` | — | Backoff-Basisintervall in Stunden |
| `SUBLARR_WANTED_BACKOFF_CAP_HOURS` | `168` | — | Maximale Backoff-Grenze (7 Tage) |
| `SUBLARR_USE_EMBEDDED_SUBS` | `true` | — | Eingebettete Untertitel-Streams in MKV-Dateien prüfen |
| `SUBLARR_SCAN_METADATA_ENGINE` | `auto` | — | Metadaten-Scan-Engine: `ffprobe`, `mediainfo` oder `auto` |
| `SUBLARR_SCAN_METADATA_MAX_WORKERS` | `4` | — | Parallele Worker für Batch-Metadaten-Scans |
| `SUBLARR_SCAN_YIELD_MS` | `0` | — | Pause zwischen Serien/Filmen (ms) während des Wanted-Scans, um CPU an API-Threads abzugeben. Standard `0` (keine Pause). `5`–`10` auf stark belasteten Single-Core-Systemen empfohlen |

---

## Webhook-Automatisierung

Konfiguration unter **Settings → Automation**.

| Variable | Standard | UI | Beschreibung |
|---|---|:---:|---|
| `SUBLARR_WEBHOOK_DELAY_MINUTES` | `5` | ✅ | Wartezeit nach Sonarr-/Radarr-Webhook vor der Verarbeitung |
| `SUBLARR_WEBHOOK_AUTO_SCAN` | `true` | ✅ | Datei nach Webhook automatisch scannen |
| `SUBLARR_WEBHOOK_AUTO_SEARCH` | `true` | ✅ | Provider nach Webhook automatisch durchsuchen |
| `SUBLARR_WEBHOOK_AUTO_TRANSLATE` | `true` | ✅ | Nach Webhook-Download automatisch übersetzen |

---

## Upgrade-System

Konfiguration unter **Settings → Automation**.

| Variable | Standard | UI | Beschreibung |
|---|---|:---:|---|
| `SUBLARR_UPGRADE_ENABLED` | `true` | ✅ | Niedrigqualitative Untertitel ersetzen, wenn eine bessere Version gefunden wird |
| `SUBLARR_UPGRADE_MIN_SCORE_DELTA` | `50` | ✅ | Mindestverbesserung im Score, die für ein Upgrade erforderlich ist |
| `SUBLARR_UPGRADE_WINDOW_DAYS` | `7` | ✅ | Kürzlich heruntergeladene Untertitel innerhalb dieses Fensters erfordern das 2-fache Delta |
| `SUBLARR_UPGRADE_PREFER_ASS` | `true` | ✅ | SRT immer auf ASS upgraden, wenn verfügbar |

---

## Video Sync

Nur Umgebungsvariablen — noch keine UI-Konfiguration.

| Variable | Standard | UI | Beschreibung |
|---|---|:---:|---|
| `SUBLARR_AUTO_SYNC_AFTER_DOWNLOAD` | `false` | — | Untertitel-Timing nach Download automatisch an das Video anpassen |
| `SUBLARR_AUTO_SYNC_ENGINE` | `ffsubsync` | — | Sync-Engine: `ffsubsync` oder `alass` |

---

## Benachrichtigungen (Apprise)

Nur Umgebungsvariablen — Konfiguration über Apprise-URL-Strings.

| Variable | Standard | UI | Beschreibung |
|---|---|:---:|---|
| `SUBLARR_NOTIFICATION_URLS_JSON` | *(leer)* | — | JSON-Array mit Apprise-Benachrichtigungs-URLs |
| `SUBLARR_NOTIFY_ON_DOWNLOAD` | `true` | — | Benachrichtigung bei Untertitel-Download |
| `SUBLARR_NOTIFY_ON_UPGRADE` | `true` | — | Benachrichtigung bei Untertitel-Upgrade |
| `SUBLARR_NOTIFY_ON_BATCH_COMPLETE` | `true` | — | Benachrichtigung bei Abschluss von Batch-Suche/-Übersetzung |
| `SUBLARR_NOTIFY_ON_ERROR` | `true` | — | Benachrichtigung bei Fehlern |
| `SUBLARR_NOTIFY_MANUAL_ACTIONS` | `false` | — | Benachrichtigung bei manuell ausgelösten Aktionen |

---

## Circuit Breaker

Nur Umgebungsvariablen — Tuning-Parameter für die Provider-Fehlerbehandlung.

| Variable | Standard | UI | Beschreibung |
|---|---|:---:|---|
| `SUBLARR_CIRCUIT_BREAKER_FAILURE_THRESHOLD` | `5` | — | Aufeinanderfolgende Fehler bis zum Öffnen des Circuit |
| `SUBLARR_CIRCUIT_BREAKER_COOLDOWN_SECONDS` | `60` | — | Sekunden im OPEN-Zustand bis zum Half-Open-Probe |
| `SUBLARR_PROVIDER_AUTO_DISABLE_COOLDOWN_MINUTES` | `30` | — | Minuten bis zur Reaktivierung eines automatisch deaktivierten Providers |

---

## Backup

Nur Umgebungsvariablen — Backup-Verzeichnis und Aufbewahrungsrichtlinie.

| Variable | Standard | UI | Beschreibung |
|---|---|:---:|---|
| `SUBLARR_BACKUP_DIR` | `/config/backups` | — | Backup-Speicherverzeichnis |
| `SUBLARR_BACKUP_RETENTION_DAILY` | `7` | — | Letzte N tägliche Backups behalten |
| `SUBLARR_BACKUP_RETENTION_WEEKLY` | `4` | — | Letzte N wöchentliche Backups behalten |
| `SUBLARR_BACKUP_RETENTION_MONTHLY` | `3` | — | Letzte N monatliche Backups behalten |

---

## Standalone-Modus (ohne Sonarr/Radarr)

Konfiguration unter **Settings → Library Sources**.

| Variable | Standard | UI | Beschreibung |
|---|---|:---:|---|
| `SUBLARR_STANDALONE_ENABLED` | `false` | ✅ | Ordnerüberwachungsmodus ohne Sonarr/Radarr aktivieren |
| `SUBLARR_STANDALONE_SCAN_INTERVAL_HOURS` | `6` | ✅ | Ordner-Scan-Intervall in Stunden. `0` = deaktiviert |
| `SUBLARR_STANDALONE_DEBOUNCE_SECONDS` | `10` | ✅ | Sekunden Wartezeit nach Erkennung einer neuen Datei vor der Verarbeitung |
| `SUBLARR_TMDB_API_KEY` | *(leer)* | ✅ | TMDB API v3 Bearer Token (für Metadaten-Lookup) |
| `SUBLARR_TVDB_API_KEY` | *(leer)* | ✅ | TVDB API v4 Key (optional) |
| `SUBLARR_TVDB_PIN` | *(leer)* | ✅ | TVDB PIN (optional, für Abonnenten-Features) |
| `SUBLARR_METADATA_CACHE_TTL_DAYS` | `30` | — | Tage, die Metadaten-Antworten gecacht werden |

---

## AniDB-Integration

Nur Umgebungsvariablen — steuert die AniDB-ID-Auflösung für absolute Anime-Episodennummerierung.

| Variable | Standard | UI | Beschreibung |
|---|---|:---:|---|
| `SUBLARR_ANIDB_ENABLED` | `true` | — | AniDB-ID-Auflösung für absolute Episodennummerierung aktivieren |
| `SUBLARR_ANIDB_CACHE_TTL_DAYS` | `30` | — | Cache-TTL für TVDB-zu-AniDB-Mappings |
| `SUBLARR_ANIDB_CUSTOM_FIELD_NAME` | `anidb_id` | — | Name des Custom Fields in Sonarr für die AniDB-ID |
| `SUBLARR_ANIDB_FALLBACK_TO_MAPPING` | `true` | — | Gecachtes Mapping als Fallback nutzen, wenn AniDB nicht erreichbar ist |

---

## Datenbank und Redis (Erweitert)

Nur Umgebungsvariablen — Infrastruktur-Level-Backend-Auswahl. Die meisten Deployments nutzen den SQLite-Standard.

| Variable | Standard | UI | Beschreibung |
|---|---|:---:|---|
| `SUBLARR_DATABASE_URL` | *(leer)* | — | SQLAlchemy-URL. Leer = SQLite unter `DB_PATH` |
| `SUBLARR_DB_POOL_SIZE` | `5` | — | SQLAlchemy pool_size (bei SQLite ignoriert) |
| `SUBLARR_DB_POOL_MAX_OVERFLOW` | `10` | — | SQLAlchemy max_overflow (bei SQLite ignoriert) |
| `SUBLARR_DB_POOL_RECYCLE` | `3600` | — | Verbindungen nach N Sekunden recyclen |
| `SUBLARR_REDIS_URL` | *(leer)* | — | Redis-URL (z. B. `redis://localhost:6379/0`). Leer = kein Redis; automatischer Fallback auf In-Process-`MemoryJobQueue` + In-Memory-Provider-Cache |
| `SUBLARR_REDIS_CACHE_ENABLED` | `true` | — | Redis für Provider-Suchergebnis-Cache nutzen (wenn redis_url gesetzt). Deaktivieren, um Redis nur für die Job-Queue zu verwenden |
| `SUBLARR_REDIS_QUEUE_ENABLED` | `true` | — | Redis+RQ für Job-Queue nutzen (wenn redis_url gesetzt). Erfordert einen separaten `rq worker`-Prozess — `docker-compose.redis.yml` verwenden oder `python backend/worker.py` starten. Skalierung mit `docker compose ... --scale rq-worker=N` |

---

## Stream-Entfernung / Remux

Konfiguration unter **Settings → Automation → Stream Removal**.

| Variable | Standard | UI | Beschreibung |
|---|---|:---:|---|
| `SUBLARR_REMUX_TRASH_DIR` | `.sublarr` | ✅ | Relativer (zum Medien-Root) oder absoluter Pfad für den Remux-Backup-Papierkorb. Backups landen in `<trash_dir>/trash/<YYYY-MM-DD>/<file>.<ts>.bak` |
| `SUBLARR_REMUX_BACKUP_RETENTION_DAYS` | `7` | ✅ | Tage, die Remux-Backups aufbewahrt werden. `0` = unbegrenzt |
| `SUBLARR_REMUX_USE_REFLINK` | `true` | ✅ | CoW-Reflink für kostenlose Backups auf Btrfs/XFS versuchen, bevor auf reguläre Kopie zurückgefallen wird |
| `SUBLARR_REMUX_ARR_PAUSE_ENABLED` | `true` | ✅ | Sonarr-/Radarr-Ordnerüberwachung während der Remux-Operation pausieren |

**Backend-Auswahl:** mkvmerge (MKV/MK3D) oder ffmpeg (MP4, AVI, alle anderen) — automatisch anhand der Dateiendung erkannt. Falls mkvmerge nicht verfügbar ist, Fallback auf ffmpeg mit Warnung (`mkvtoolnix` für besseren MKV-Support installieren). mkvtoolnix ist im offiziellen Docker-Image enthalten.

---

## Sidecar-Auto-Cleanup

Automatisches Löschen von Sidecar-Dateien in nicht benötigten Sprachen nach Batch-Extraktion, um das Medienverzeichnis aufgeräumt zu halten.
Konfiguration unter **Settings → Automation**.

| Variable | Standard | UI | Beschreibung |
|---|---|:---:|---|
| `SUBLARR_AUTO_CLEANUP_AFTER_EXTRACT` | `false` | ✅ | Fremdsprachige Sidecars nach Batch-Extraktion löschen |
| `SUBLARR_AUTO_CLEANUP_KEEP_LANGUAGES` | *(leer)* | ✅ | Kommaseparierte ISO-639-1-Codes der zu behaltenden Sprachen (leer = nichts wird gelöscht) |
| `SUBLARR_AUTO_CLEANUP_KEEP_FORMATS` | `any` | ✅ | `ass`, `srt` oder `any` — SRT löschen, wenn ASS für dieselbe Sprache vorhanden |

---

## Untertitel-Papierkorb

Soft-Delete für Untertitel. Gelöschte Dateien werden in ein `.trash`-Verzeichnis verschoben und nach N Tagen automatisch endgültig gelöscht.
Konfiguration unter **Settings → Automation**.

| Variable | Standard | UI | Beschreibung |
|---|---|:---:|---|
| `SUBLARR_SUBTITLE_TRASH_RETENTION_DAYS` | `7` | ✅ | Tage, die gelöschte Dateien vor der endgültigen Löschung aufbewahrt werden. `0` = unbegrenzt |

---

## Hörgeschädigten-Annotationen

Konfiguration unter **Settings → Translation**.

| Variable | Standard | UI | Beschreibung |
|---|---|:---:|---|
| `SUBLARR_HI_REMOVAL_ENABLED` | `false` | ✅ | Hörgeschädigten-Annotationen beim Download aus Untertiteln entfernen |

---

## Credit-Filterung

Konfiguration unter **Settings → Translation**.

| Variable | Standard | UI | Beschreibung |
|---|---|:---:|---|
| `SUBLARR_CREDIT_THRESHOLD_SEC` | `90` | ✅ | Sekunden am Ende einer Untertiteldatei, die von der Dauer-Heuristik als Credit-Bereich behandelt werden. |
| `SUBLARR_OP_WINDOW_SEC` | `300` | ✅ | Sekunden am Anfang und Ende einer Untertiteldatei, die als OP- und ED-Erkennungsfenster gelten. Bei kurzen Episoden reduzieren, falls Fehlerkennungen auftreten. |

---

## Plugin-System

Nur Umgebungsvariablen — Plugin-Verzeichnis ist Infrastruktur; Hot-Reload ist ein Entwicklerfeature.

| Variable | Standard | UI | Beschreibung |
|---|---|:---:|---|
| `SUBLARR_PLUGINS_DIR` | `/config/plugins` | — | Verzeichnis für Provider-Plugins |
| `SUBLARR_PLUGIN_HOT_RELOAD` | `false` | — | Watchdog-Dateiüberwachung für Live-Plugin-Neuladen aktivieren |

---

Siehe [.env.example](.env.example) für die minimale Deployment-Vorlage.
