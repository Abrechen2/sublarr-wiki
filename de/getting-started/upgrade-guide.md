---
title: Upgrade-Leitfaden
description: Sublarr zwischen Versionen aktualisieren — Migrationshinweise und Breaking Changes
published: true
date: 2026-03-14
---

# Migrationsleitfaden

## Aktuelle Version: 0.37.0-beta

---

## Upgrade auf 0.37.x

### v0.37.0-beta — DateTime-Migration (Breaking)

**Achtung: Breaking Change — alle Zeitstempel-Spalten wurden von TEXT auf `DateTime(timezone=True)` migriert.**

#### Was sich geändert hat

Frühere Versionen speicherten Zeitstempel als ISO-8601-Strings mit `T`-Trennzeichen und `+00:00`-Offset:
```
2024-01-15T10:30:00+00:00
```

Ab 0.37.0-beta werden Zeitstempel im nativen SQLAlchemy-SQLite-Format gespeichert (Leerzeichen als Trennzeichen, ohne Offset):
```
2024-01-15 10:30:00
```

#### Migration läuft automatisch

Bei **Docker-Deployments** wird die Alembic-Migration `b0c1d2e3f4a5` automatisch beim Containerstart ausgeführt — keine manuellen Schritte erforderlich.

#### Migration verifizieren (optional)

Ein eigenständiges Prüfskript ist enthalten:

```bash
# Vor dem Upgrade — aktuelle Zeilenzahlen sichern
python scripts/check_datetime_migration.py --db /config/sublarr.db --mode before

# Nach dem Upgrade — Format prüfen und Zahlen vergleichen
python scripts/check_datetime_migration.py --db /config/sublarr.db --mode after
```

Exit-Code 0 = alle Prüfungen bestanden. Exit-Code 1 = Probleme gefunden (siehe Ausgabe).

#### Session-Timeout-Standard geändert

Der Standard-Session-Timeout beträgt jetzt **8 Stunden** (vorher Flask-Standard von 31 Tagen). Konfigurierbar über `SUBLARR_SESSION_TIMEOUT_MINUTES`.

---

## Aktuelle Version (Archiv): 0.19.2-beta

Dieser Leitfaden deckt Upgrades von jeder früheren Sublarr-Version ab. Die vollständige Änderungshistorie findet sich im [CHANGELOG.md](../CHANGELOG.md).

Für den Wechsel von SQLite auf PostgreSQL siehe [POSTGRESQL.md](POSTGRESQL.md).

---

## Upgrade auf 0.13.x

### v0.13.2-beta — Sicherheitshärtung

Keine Breaking Changes. Neue Sicherheitsfeatures sind standardmäßig aktiv:
- WebSocket-Authentifizierung wird nun erzwungen (`SUBLARR_API_KEY`, falls gesetzt)
- Secret-Maskierung in Logs (API-Keys, Passwörter sind in der Debug-Ausgabe nicht mehr sichtbar)
- DoS-Limits für Log-/Batch-Endpunkte
- Verbesserungen beim Path-Traversal-Schutz

### v0.13.1-beta — Sidecar-Verwaltung

Neue Einstellungen (alle optional, standardmäßig deaktiviert):
- `SUBLARR_AUTO_CLEANUP_AFTER_EXTRACT` — fremdsprachige Sidecars nach Batch-Extraktion automatisch löschen
- `SUBLARR_AUTO_CLEANUP_KEEP_LANGUAGES` — Sprachen, die beim Cleanup beibehalten werden
- `SUBLARR_AUTO_CLEANUP_KEEP_FORMATS` — Formatpräferenz für Cleanup
- `SUBLARR_SUBTITLE_TRASH_RETENTION_DAYS` — Aufbewahrungsdauer für Soft-Delete

Datenbank: Neue Tabellen `upgrade_history` und `filter_presets` werden beim Start automatisch erstellt.

### v0.13.0-beta — Tasks-Seite & Queue-Sichtbarkeit

Neue Endpunkte:
- `GET /api/v1/tasks` — Liste der Hintergrundaufgaben
- `POST /api/v1/tasks/<name>/cancel` — laufende Aufgabe abbrechen

Frontend: Neue Seiten Tasks (`/tasks`), Queue (`/queue`), Statistics (`/statistics`).

---

## Upgrade von v0.9.x → v0.13.x (Historisch)

## Hinweis zur Versionsnummerierung

Sublarr hat frühzeitig eine Standard-Pre-Release-Versionierung eingeführt. Die anfängliche v1.0.0-beta war verfrüht — v1.0.0 ist für das finale stabile Release reserviert. Die Reihenfolge war:

```
v1.0.0-beta (alt) -> v0.9.0-beta -> ... -> v0.13.2-beta (aktuell) -> v1.0.0 (stabil)
```

## Was sich in v0.9.0 geändert hat

v0.9.0-beta brachte: Plugin-Erweiterbarkeit, Multi-Backend-Übersetzung (DeepL, LibreTranslate, OpenAI-kompatibel, Google), Whisper Speech-to-Text, Standalone-Modus ohne Sonarr/Radarr, Plex-/Kodi-Support, Forced-Subtitle-Verwaltung, Event Hooks, UI-Internationalisierung (EN/DE), Dark-/Light-Theming, Backup/Restore, Statistiken, OpenAPI-Dokumentation und Performance-Verbesserungen.

## Upgrade-Schritte

### Docker (Empfohlen)

1. **Neues Image pullen:**
   ```bash
   docker pull ghcr.io/abrechen2/sublarr:0.13.2-beta
   ```

2. **Bestehenden Container stoppen:**
   ```bash
   docker stop sublarr
   ```

3. **Daten sichern** (empfohlen):
   ```bash
   cp -r /path/to/appdata/sublarr /path/to/appdata/sublarr.backup
   ```

4. **docker-compose.yml aktualisieren** (falls Version-Tags verwendet werden):
   ```yaml
   services:
     sublarr:
       image: ghcr.io/abrechen2/sublarr:0.13.2-beta
       # ... Rest der Konfiguration unverändert
   ```

5. **Mit neuem Image starten:**
   ```bash
   docker compose up -d
   ```

6. **Datenbankmigrationen laufen automatisch beim Start.** Keine manuellen Schritte erforderlich.

### Unraid

1. Zum Docker-Tab in Unraid wechseln
2. Auf das Sublarr-Container-Icon klicken und „Edit" wählen
3. Das Repository-Feld auf `ghcr.io/abrechen2/sublarr:0.13.2-beta` aktualisieren
4. Apply klicken
5. Der Container wird automatisch neu gepullt und gestartet

### Konfigurationsänderungen

#### Neue Umgebungsvariablen

Keine neuen erforderlichen Umgebungsvariablen. Alle neuen Features werden über die Web-UI-Einstellungsseite konfiguriert.

Optionale Variablen für erweiterte Nutzung:

| Variable | Standard | Zweck |
|----------|----------|-------|
| `SUBLARR_PLUGIN_HOT_RELOAD` | `false` | Watchdog-basiertes Plugin-Hot-Reload aktivieren |
| `SUBLARR_WHISPER_ENABLED` | `false` | Whisper Speech-to-Text-Fallback aktivieren |
| `SUBLARR_WHISPER_BACKEND` | `subgen` | Aktives Whisper-Backend (subgen oder faster_whisper) |
| `SUBLARR_MAX_CONCURRENT_WHISPER` | `1` | Maximale gleichzeitige Whisper-Jobs |

#### Entfernte Variablen

Keine. Alle bestehenden v1.0.0-beta-Umgebungsvariablen funktionieren weiterhin.

#### Geänderte Standardwerte

Keine. Alle Standardwerte bleiben unverändert.

### Breaking Changes

#### API-Änderungen

Keine Breaking API-Changes. Alle v1.0.0-beta-Endpunkte funktionieren weiterhin an denselben Pfaden mit denselben Request-/Response-Formaten.

**Neue Endpunkte hinzugefügt** (nicht-breaking):
- `GET /api/v1/openapi.json` — OpenAPI-Spezifikation
- `GET /api/docs/` — Swagger UI
- `GET /api/v1/health/detailed` — Erweiterte Health mit 11 Subsystem-Kategorien
- `GET /api/v1/tasks` — Hintergrund-Scheduler-Status
- Provider-, Übersetzungs-, Medienserver-, Whisper-, Standalone-, Hooks- und Scoring-Verwaltungsendpunkte

#### Architekturänderungen

Das Backend wurde von einer monolithischen `server.py` in 30 Flask-Blueprint-Module mit dem Application-Factory-Pattern refaktoriert. **Das beeinflusst die öffentliche API nicht** — alle Endpunkte bleiben an denselben Pfaden.

Falls interne Python-Module referenziert wurden (z. B. in eigenen Skripten), Imports aktualisieren:
- `from server import app` wird zu `from app import create_app`
- `from database import ...` wird zu `from db.xxx import ...` (aufgeteilt in Domain-Module)

### Plugin-System

Eigene Provider-Plugins können jetzt im Verzeichnis `/config/plugins/` installiert werden (als Docker-Volume eingebunden). Jedes Plugin ist eine Python-Datei, die das `SubtitleProvider`-ABC implementiert.

Siehe [docs/PROVIDERS.md](PROVIDERS.md) für den Plugin-Entwicklungsleitfaden.

### Neue Features mit Konfigurationsbedarf

Alle neuen Features funktionieren mit sinnvollen Standardwerten. Optionale Konfiguration über die Settings-UI:

| Feature | Konfiguration unter | Hinweise |
|---------|---------------------|----------|
| Übersetzungs-Backends | Settings > Translation Backends | Ollama ist Standard; DeepL, LibreTranslate usw. hinzufügen |
| Medienserver | Settings > Media Servers | Plex, Kodi neben bestehendem Jellyfin/Emby |
| Whisper | Settings > Whisper | faster-whisper oder Subgen aktivieren und konfigurieren |
| Standalone-Modus | Settings > Library Sources | Watched Folders für Medien ohne *arr-Apps hinzufügen |
| Forced Subtitles | Language Profiles > Forced Preference | Forced-Sub-Handling pro Profil |
| Event Hooks | Settings > Events & Hooks | Shell-Skripte und ausgehende Webhooks |
| Eigenes Scoring | Settings > Scoring | Gewichtungen und Pro-Provider-Modifikatoren anpassen |
| Sprache | UI-Header > Sprachumschalter | Zwischen Englisch und Deutsch umschalten |
| Theme | UI-Header > Theme-Toggle | Dark, Light oder Systemeinstellung |
| Backup-Zeitplan | Settings > Backup | Automatisches Backup-Intervall konfigurieren |

## Fehlerbehebung

### Probleme bei der Datenbankmigration

Datenbankmigrationen laufen automatisch beim Start. Bei Problemen:

1. **Container-Logs prüfen:**
   ```bash
   docker logs sublarr
   ```

2. **Aus Backup wiederherstellen:**
   ```bash
   docker stop sublarr
   cp /path/to/appdata/sublarr.backup/sublarr.db /path/to/appdata/sublarr/sublarr.db
   docker start sublarr
   ```

3. **Eingebaute Backup-/Restore-Funktion nutzen:** Falls vor dem Upgrade ein Backup über die UI erstellt wurde, kann es unter Settings > Backup > Restore wiederhergestellt werden.

### Plugin-Ladefehler

Häufige Plugin-Probleme und Lösungen:

| Problem | Lösung |
|---------|--------|
| Plugin erscheint nicht | Prüfen, ob die Datei in `/config/plugins/` liegt und die Endung `.py` hat |
| Import-Fehler | Sicherstellen, dass alle Abhängigkeiten im Container installiert sind |
| Namenskonflikt | Eingebaute Provider haben immer Vorrang; Plugin-Klasse umbenennen |
| Konfiguration wird nicht gespeichert | Plugin-`config_fields` auf das erwartete Format prüfen |
| Hot-Reload funktioniert nicht | `SUBLARR_PLUGIN_HOT_RELOAD=true` setzen und sicherstellen, dass `watchdog` installiert ist |

### Jellyfin-/Emby-Migration

Falls Jellyfin/Emby über die alten Umgebungsvariablen `SUBLARR_JELLYFIN_URL` / `SUBLARR_JELLYFIN_API_KEY` konfiguriert war, erfolgt die **automatische Migration** in das neue Medienserver-System beim ersten Start. Kein manuelles Eingreifen nötig.

Zur Prüfung: Unter Settings > Media Servers kontrollieren, ob die Jellyfin-/Emby-Instanz in der Liste erscheint.

### Performance-Probleme nach dem Upgrade

Der inkrementelle Wanted-Scan ist standardmäßig aktiviert. Falls Einträge fehlen:

1. Einen manuellen vollständigen Scan von der Wanted-Seite auslösen
2. Das System führt automatisch jeden 6. Zyklus einen vollständigen Scan durch
3. `/api/v1/health/detailed` für den Subsystem-Status prüfen

### API-Key-Authentifizierung

Falls `SUBLARR_API_KEY` gesetzt ist, funktionieren alle bestehenden authentifizierten Endpunkte weiterhin mit dem `X-Api-Key`-Header. Neue Endpunkte folgen demselben Authentifizierungsmuster.

## Fragen oder Probleme?

- Ein [GitHub Issue](https://github.com/Abrechen2/sublarr/issues) für Bugs oder Feature Requests eröffnen
- Das [Benutzerhandbuch](USER-GUIDE.md) für Einrichtungsanleitungen lesen
- Die [API-Dokumentation](http://localhost:5765/api/docs) über Swagger UI durchsehen
