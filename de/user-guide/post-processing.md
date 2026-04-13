---
title: Post-Processing
description: Shell-Befehl nach jedem Untertitel-Download ausführen
published: true
date: 2026-04-03
---

# Post-Processing

Sublarr kann nach jedem erfolgreichen Untertitel-Download automatisch einen
Shell-Befehl ausführen. Damit lassen sich Plex benachrichtigen, Dateien
umbenennen oder beliebige Automatisierungen anstoßen — ohne ein Plugin
schreiben zu müssen.

> **Standardmäßig deaktiviert.** Post-Processing muss unter
> Settings → Automation explizit aktiviert werden, bevor der Befehl
> ausgeführt wird.

## Post-Processing aktivieren

1. Zu **Settings → Automation** navigieren
2. **Post-Processing** einschalten
3. Den Befehl im Feld **Post-Download Command** eingeben
4. **Save** klicken

## Verfügbare Variablen

Variablen werden vor der Ausführung in den Befehlsstring eingesetzt.

| Variable | Beispielwert | Beschreibung |
|---|---|---|
| `{subtitle_path}` | `/media/anime/Naruto.srt` | Absoluter Pfad zur gespeicherten Untertiteldatei |
| `{path}` | `/media/anime/Naruto.srt` | Alias für `{subtitle_path}` (Bazarr-Kompatibilität) |
| `{language}` | `de` | ISO-639-1-Sprachcode |
| `{provider}` | `jimaku` | Name des Providers, der den Untertitel geliefert hat |
| `{score}` | `93` | Ganzzahliger Match-Score (0–100) |
| `{media_type}` | `series` | `series`, `movie` oder leerer String |
| `{video_path}` | _(leer)_ | Reserviert — im aktuellen Release immer leer |

## Beispiele

**Plex nach Download benachrichtigen:**
```bash
curl -s "http://plex:32400/library/sections/1/refresh?X-Plex-Token=TOKEN" \
  -o /dev/null
```

**Log-Zeile schreiben:**
```bash
/usr/local/bin/log-subtitle.sh {subtitle_path} {language} {provider}
```

**Discord-Webhook bei Download:**
```bash
curl -s -X POST https://discord.com/api/webhooks/YOUR_ID/YOUR_TOKEN \
  -H "Content-Type: application/json" \
  -d '{"content":"Untertitel heruntergeladen: {subtitle_path} ({language}) via {provider}"}'
```

> Hinweis: Der Befehl wird mit `shlex.split` tokenisiert — Pfade mit
> Leerzeichen in Anführungszeichen setzen oder über ein Wrapper-Skript
> weiterleiten.

## Verhalten & Limits

- **Timeout:** 60 Sekunden. Befehle, die das überschreiten, werden beendet; Sublarr loggt eine Warnung und fährt fort.
- **Nicht-blockierende Fehler:** Ein fehlschlagender Befehl (Exit-Code ungleich 0, Absturz oder Timeout) wird als Warnung geloggt. Er blockiert oder wiederholt die Download-Pipeline niemals.
- **Keine Shell-Erweiterung:** Der Befehl läuft mit `shell=False`. Shell-Features wie `&&`, `|`, `$VAR` oder Glob-Muster sind nicht verfügbar. Für komplexe Logik ein Wrapper-Skript verwenden.
- **Ausführungskontext:** Der Befehl läuft als derselbe Benutzer wie Sublarr (Container: `sublarr`-Benutzer, Standard-UID 1000). Sicherstellen, dass der Befehl und alle Zielpfade für diesen Benutzer zugänglich sind.

## Fehlerbehebung

**Befehl wird nicht ausgeführt**
- Prüfen, ob Post-Processing unter Settings → Automation **eingeschaltet** ist.
- Sicherstellen, dass das Feld `Post-Download Command` nicht leer ist.

**„invalid shell syntax" in den Logs**
- Sublarr verwendet `shlex.split` zur Tokenisierung des Befehls. Nicht geschlossene
  Anführungszeichen oder nicht unterstützte Shell-Syntax verursacht diesen Fehler.
  Den Befehl mit `python3 -c "import shlex; print(shlex.split('BEFEHL'))"` testen.

**Timeout-Warnung in den Logs**
- Der Befehl überschreitet 60 Sekunden. Lang laufende Arbeiten in einen
  Hintergrundjob auslagern und den Post-Processing-Befehl nur zum Anstoßen
  verwenden.
