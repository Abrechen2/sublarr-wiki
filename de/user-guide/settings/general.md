---
title: Settings — Allgemein
description: Allgemeine Anwendungseinstellungen — Host, Sicherheit, Authentifizierung, UI, Backups
published: true
date: 2026-04-13
---

# Settings — Allgemein

Diese Seite beschreibt die UI-konfigurierbaren Einstellungen unter **Settings → General**. Für die Konfiguration via Umgebungsvariablen siehe [Umgebungsvariablen](/getting-started/environment-variables).

## Host & Port

| Einstellung | Standard | Umgebungsvariable | Beschreibung |
|-------------|----------|-------------------|--------------|
| Host | `0.0.0.0` | — | Bind-Adresse — Standard beibehalten, außer auf Multi-NIC-Servern |
| Port | `5765` | `SUBLARR_PORT` | HTTP-Port — ändern, falls der Port bereits belegt ist |
| URL Base | _(leer)_ | — | Setzen, wenn Sublarr hinter einem Reverse Proxy unter einem Unterpfad läuft (z. B. `/sublarr`) |

## Authentifizierung

| Einstellung | Standard | Umgebungsvariable | Beschreibung |
|-------------|----------|-------------------|--------------|
| Authentifizierung aktiviert | `false` | — | Einzelkonto-Login aktivieren |
| Benutzername | `admin` | — | Login-Benutzername |
| Passwort | _(beim Erststart gesetzt)_ | — | Login-Passwort |

Siehe [Login-Einrichtung](/getting-started/quick-start#authentication) für den vollständigen Einrichtungsablauf.

### Session- & Login-Sicherheit

| Einstellung | Standard | Umgebungsvariable | Beschreibung |
|-------------|----------|-------------------|--------------|
| Session-Timeout (Minuten) | `0` | `SUBLARR_SESSION_TIMEOUT_MINUTES` | Inaktivitäts-Timeout bis zum automatischen Logout. `0` = kein Timeout |
| Max. Login-Versuche | `20` | `SUBLARR_MAX_LOGIN_ATTEMPTS` | Fehlgeschlagene Login-Versuche bis zur Kontosperre |
| Sperrdauer (Minuten) | `60` | `SUBLARR_LOCKOUT_DURATION_MINUTES` | Dauer der Kontosperre nach Überschreitung der maximalen Versuche |
| Erlaubte IP-Bereiche | _(leer)_ | `SUBLARR_ALLOWED_IP_RANGES` | Kommaseparierte CIDR-Bereiche (z. B. `192.168.1.0/24,10.0.0.0/8`). Leer = alle erlaubt |

> [!TIP]
> Für Homelab-Setups hinter einem VPN können die erlaubten IP-Bereiche leer bleiben. Nur bei Freigabe von Sublarr ins Internet verwenden, um den Zugriff auf vertrauenswürdige Netzwerke einzuschränken.

## Logging

| Einstellung | Standard | Umgebungsvariable | Beschreibung |
|-------------|----------|-------------------|--------------|
| Log-Level | `INFO` | `SUBLARR_LOG_LEVEL` | Detaillierungsgrad: `DEBUG`, `INFO`, `WARNING`, `ERROR`, `CRITICAL` |
| Log-Datei | `log/sublarr.log` | `SUBLARR_LOG_FILE` | Log-Dateipfad. Docker-Standard: `/config/sublarr.log` |
| Log-Format | `text` | `SUBLARR_LOG_FORMAT` | Ausgabeformat: `text` (menschenlesbar) oder `json` (strukturiert für Log-Aggregation) |

> [!NOTE]
> `SUBLARR_LOG_FORMAT=json` setzen, wenn Logs in Loki, Elasticsearch oder ähnliche Systeme eingespeist werden. Das Format `text` ist in `docker logs` einfacher zu lesen.

## Datenbank

| Einstellung | Standard | Umgebungsvariable | Beschreibung |
|-------------|----------|-------------------|--------------|
| DB-Pfad | `/config/sublarr.db` | `SUBLARR_DB_PATH` | Speicherort der SQLite-Datenbankdatei |
| Database URL | _(leer)_ | `SUBLARR_DATABASE_URL` | Vollständige SQLAlchemy-URL (z. B. `postgresql://user:pass@host/db`). Überschreibt DB-Pfad, wenn gesetzt |
| DB Pool Size | `5` | `SUBLARR_DB_POOL_SIZE` | SQLAlchemy-Connection-Pool-Größe (bei SQLite ignoriert) |
| DB Pool Max Overflow | `10` | `SUBLARR_DB_POOL_MAX_OVERFLOW` | Zusätzliche Verbindungen über die Pool-Größe hinaus (bei SQLite ignoriert) |
| DB Pool Recycle | `3600` | `SUBLARR_DB_POOL_RECYCLE` | Verbindungen nach N Sekunden recyceln, um veraltete Verbindungen zu vermeiden |

> [!WARNING]
> Der Wechsel von SQLite auf PostgreSQL erfordert eine Datenmigration. Siehe [PostgreSQL-Setup](/development/postgresql) für Anweisungen.

## CORS

| Einstellung | Standard | Umgebungsvariable | Beschreibung |
|-------------|----------|-------------------|--------------|
| CORS Origins | `http://localhost:5173,http://localhost:5765` | `SUBLARR_CORS_ORIGINS` | Kommaseparierte erlaubte Origins für CORS- und WebSocket-Verbindungen |

> [!WARNING]
> `SUBLARR_CORS_ORIGINS=*` erlaubt jeden Origin. Nur in vollständig vertrauenswürdigen Umgebungen verwenden.

## Redis

| Einstellung | Standard | Umgebungsvariable | Beschreibung |
|-------------|----------|-------------------|--------------|
| Redis URL | _(leer)_ | `SUBLARR_REDIS_URL` | Redis-Verbindungs-URL (z. B. `redis://localhost:6379/0`). Leer = In-Memory-Fallback |
| Redis Cache Enabled | `true` | `SUBLARR_REDIS_CACHE_ENABLED` | Redis für Provider-Suchergebnis-Cache nutzen (erfordert `redis_url`) |
| Redis Queue Enabled | `true` | `SUBLARR_REDIS_QUEUE_ENABLED` | Redis+RQ für die Job-Queue nutzen (erfordert `redis_url` und einen separaten Worker-Prozess) |

> [!TIP]
> Redis ist optional. Ohne Redis nutzt Sublarr eine In-Memory-Job-Queue und einen In-Memory-Provider-Cache. Redis wird für Multi-Container-Deployments oder bei Bedarf an persistentem Job-Status über Neustarts hinweg empfohlen.

## Plugins

| Einstellung | Standard | Umgebungsvariable | Beschreibung |
|-------------|----------|-------------------|--------------|
| Plugin-Verzeichnis | `/config/plugins` | `SUBLARR_PLUGINS_DIR` | Verzeichnis, aus dem eigene Provider-Plugins geladen werden |
| Plugin Hot Reload | `false` | `SUBLARR_PLUGIN_HOT_RELOAD` | Plugin-Verzeichnis auf Änderungen überwachen und automatisch neu laden |

Siehe [Plugin-Entwicklung](/development/plugin-development) für die Erstellung eigener Provider.

## Oberflächen-Einstellungen

| Einstellung | Standard | Umgebungsvariable | Beschreibung |
|-------------|----------|-------------------|--------------|
| Oberflächensprache | `en` | `SUBLARR_INTERFACE_LANGUAGE` | UI-Sprache: `en` (Englisch) oder `de` (Deutsch) |
| Einträge pro Seite | `25` | `SUBLARR_ITEMS_PER_PAGE` | Anzahl der Einträge pro Seite in Listenansichten |
| Standard-Bibliotheksansicht | `grid` | `SUBLARR_DEFAULT_LIBRARY_VIEW` | Anzeigemodus der Bibliothek: `grid` oder `list` |
| Standard-Bibliotheks-Sortierung | `alpha` | `SUBLARR_DEFAULT_LIBRARY_SORT` | Standard-Sortierreihenfolge: `alpha`, `date` oder `score` |
| Datum-/Zeitformat | `relative` | `SUBLARR_DATETIME_FORMAT` | Zeitstempel-Anzeige: `relative` (z. B. „vor 2 Stunden") oder `absolute` (z. B. „2026-04-13 14:30") |

## Ruhezeiten

| Einstellung | Standard | Umgebungsvariable | Beschreibung |
|-------------|----------|-------------------|--------------|
| Ruhezeiten aktiviert | `false` | `SUBLARR_QUIET_HOURS_ENABLED` | Automatische Suchen und Downloads während definierter Stunden pausieren |
| Startzeit | `23:00` | `SUBLARR_QUIET_HOURS_START` | Beginn der Ruhephase (24-Stunden-Format) |
| Endzeit | `07:00` | `SUBLARR_QUIET_HOURS_END` | Ende der Ruhephase (24-Stunden-Format) |
| Zeitzone | `UTC` | `SUBLARR_QUIET_HOURS_TIMEZONE` | Zeitzone für Ruhezeiten (z. B. `Europe/Berlin`, `America/New_York`) |

> [!NOTE]
> Ruhezeiten unterdrücken alle automatischen Provider-Suchen und Übersetzungsjobs. Manuelle Aktionen über die UI sind davon nicht betroffen.

## Festplatten-Überwachung

| Einstellung | Standard | Umgebungsvariable | Beschreibung |
|-------------|----------|-------------------|--------------|
| Warnungsschwelle (%) | `90` | `SUBLARR_DISK_WARNING_THRESHOLD_PERCENT` | Festplattenauslastung in Prozent, ab der eine Warnung ausgelöst wird |
| Warnungsbenachrichtigung | `true` | `SUBLARR_DISK_WARNING_NOTIFY` | Benachrichtigung senden, wenn die Auslastung den Schwellenwert überschreitet |

## Updates

Sublarr prüft GitHub-Releases auf neuere Versionen. Bei verfügbarem Update erscheint eine Benachrichtigung in der Sidebar. Automatische Updates werden nicht unterstützt — das neue Docker-Image manuell pullen.

## Backups

Sublarr sichert seine SQLite-Datenbank automatisch nach `/config/backups/` gemäß einem konfigurierbaren Zeitplan.

| Einstellung | Standard | Umgebungsvariable | Beschreibung |
|-------------|----------|-------------------|--------------|
| Auto-Backup aktiviert | `false` | `SUBLARR_BACKUP_AUTO_ENABLED` | Geplante automatische Backups aktivieren |
| Backup-Intervall (Stunden) | `24` | `SUBLARR_BACKUP_AUTO_INTERVAL_HOURS` | Zeit zwischen automatischen Backups |
| Backup beim Start | `false` | `SUBLARR_BACKUP_AUTO_ON_STARTUP` | Backup beim Containerstart erstellen |
| Bei Fehler benachrichtigen | `true` | `SUBLARR_BACKUP_NOTIFY_ON_FAILURE` | Benachrichtigung senden, wenn ein geplantes Backup fehlschlägt |

Wiederherstellung durch Ersetzen von `/config/sublarr.db` und Neustart des Containers. Alternativ die eingebaute Restore-Funktion unter **Settings → Backup → Restore** nutzen.

## Setup-Wizard

| Einstellung | Default | Umgebungsvariable | Beschreibung |
|-------------|---------|-------------------|--------------|
| Wizard abgeschlossen | `false` | `SUBLARR_SETUP_WIZARD_COMPLETED` | Wird auf `true` gesetzt, sobald der Erst-Setup-Wizard abgeschlossen ist. Wird in der Datenbank gespeichert; die UI unterdrückt den Wizard danach bei allen weiteren Besuchen. Manuelles Zurücksetzen auf `false` blendet ihn beim nächsten Laden wieder ein. |

> [!NOTE]
> Du musst diese Einstellung normalerweise nicht ändern — der Wizard setzt sie nach Abschluss selbst. Sie existiert vor allem, damit Headless-Deployments, die ihre Konfiguration über `SUBLARR_*`-Umgebungsvariablen einseeden, die Installation als fertig markieren können.

## Analyse

Sublarr sammelt keine Analysen oder Telemetriedaten. Keine Daten verlassen den Server.
