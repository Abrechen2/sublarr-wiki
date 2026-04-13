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
