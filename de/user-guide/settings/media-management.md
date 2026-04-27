---
title: Settings — Medienverwaltung
description: Dateibenennung, Import und Medienpfad-Konfiguration
published: true
date: 2026-03-14
---

# Settings — Medienverwaltung

## Untertitel-Dateibenennung

Sublarr schreibt Untertiteldateien neben die Mediendateien mit folgendem Namensmuster:

```
{MediaFileName}.{language}.{format}
```

Beispiele:
- `Show.S01E01.mkv` → `Show.S01E01.de.ass`
- `Movie.2023.mkv` → `Movie.2023.en.srt`

Der Sprachcode verwendet ISO 639-1 (2-stellig: `en`, `de`, `ja`) oder ISO 639-2 (3-stellig: `eng`, `deu`).

## Stammordner

Stammordner definieren, wo Sublarr nach Mediendateien sucht. Diese müssen mit den Jellyfin-/Emby-Bibliothekspfaden und den Docker-Volume-Mounts übereinstimmen.

Stammordner unter **Settings → Media Management → Root Folders** hinzufügen.

## Importverhalten

- Sublarr **löscht oder verändert niemals Mediendateien** — es werden ausschließlich `.ass`- und `.srt`-Sidecar-Dateien erstellt
- Vorhandene Untertitel mit einem höheren Score als der gefundene Untertitel werden nicht überschrieben (Upgrade-Schwellenwert konfigurierbar)
- Dateien werden mit denselben Berechtigungen wie das Verzeichnis der Mediendatei geschrieben

## Embedded-Untertitel-Auswahl *(v0.71.0-beta)*

Steuert, wie SDH-/CC-Spuren in MKV-Dateien gewichtet werden, wenn kein externer Provider eine bessere Variante liefert.

| Einstellung | Default | Umgebungsvariable | Beschreibung |
|-------------|---------|-------------------|--------------|
| Embedded SDH erlauben | `true` | `SUBLARR_EMBEDDED_ALLOW_SDH` | Ob SDH-/Closed-Caption-Spuren überhaupt ausgewählt werden dürfen. `false` schließt SDH-Spuren komplett aus, selbst wenn sonst keine Alternative existiert. |
| Embedded-SDH-Penalty | `5` | `SUBLARR_EMBEDDED_SDH_PENALTY` | Score-Abzug auf SDH-Spuren beim Ranking. Höhere Werte schieben sie weiter nach hinten. Greift nur bei `embedded_allow_sdh=true`. |

## Fremdsprachenspuren bereinigen *(v0.71.0-beta)*

Nachdem Sublarr eine Sidecar-Datei in der Zielsprache geschrieben hat, kann es zusätzlich unerwünschte *eingebettete* Untertitelspuren aus der MKV entfernen (z. B. eine versehentliche spanische Spur in einem englischen Release). Standardmäßig deaktiviert — pro Serie oder global aktivierbar.

| Einstellung | Default | Umgebungsvariable | Beschreibung |
|-------------|---------|-------------------|--------------|
| Cleanup-Default | `false` | `SUBLARR_CLEANUP_FOREIGN_TRACKS_DEFAULT` | Globaler Default. Bei `false` läuft das Cleanup nur für Serien mit explizitem Override. |
| `und`-Spuren behalten | `false` | `SUBLARR_CLEANUP_FOREIGN_TRACKS_KEEP_UND` | Bei `true` überleben Spuren mit unbestimmter Sprache (`und`) das Cleanup. Hilfreich bei schlecht getaggten Anime-Fansubs, deren Zielsprache nicht markiert ist. |

> [!TIP]
> Der Per-Serien-Override liegt unter **Library → Serie → ⋯ → Bearbeiten → Fremdsprachenspuren bereinigen** mit drei Zuständen: inherit (Default greift), erzwungen aktiv, erzwungen aus. Der effektive Zustand wird als farbiger Streifen in der Serien-Zeile gezeigt.
