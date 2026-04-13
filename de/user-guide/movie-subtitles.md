---
title: Film-Untertitelverwaltung
description: Untertitel-Sidecar-Dateien für Filme in Sublarr verwalten
published: true
date: 2026-04-10
---

# Film-Untertitelverwaltung

Seit v0.47.0-beta zeigen Film-Detailseiten vorhandene Untertitel-Sidecar-Dateien zusammen mit dem vollständigen Untertitel-Aktionsmenü an, das auch für TV-Episoden verfügbar ist.

## Film-Untertitel anzeigen

Einen beliebigen Film in der Bibliothek öffnen und zur Detailseite navigieren. Falls Untertitel-Sidecar-Dateien im selben Ordner wie die Filmdatei vorhanden sind, erscheinen sie im Bereich **Subtitles**.

Jede Sidecar-Datei zeigt:
- Sprache und Format (SRT / ASS)
- Dateigröße
- Aktionsmenü

## Verfügbare Aktionen

| Aktion | Beschreibung |
|--------|--------------|
| **HI entfernen** | Hörgeschädigten-Annotationen entfernen (CC, Geräuschbeschreibungen) |
| **Standard-Korrekturen** | Standardmäßige Formatierungskorrekturen anwenden |
| **Timing verschieben** | Einen Millisekundenversatz auf alle Untertitel-Zeitstempel anwenden |
| **Download** | Die Sidecar-Datei herunterladen |

## Timing-Offset-Tool

Das Timing-Offset-Tool verschiebt alle Untertitel-Cues um eine feste Anzahl Millisekunden. Positive Werte verzögern die Untertitel, negative Werte verschieben sie nach vorne.

Das ist nützlich, wenn eine Untertiteldatei leicht asynchron zu einem bestimmten Video-Encode ist.

## Hinweise

- Aktionen werden direkt auf die Sidecar-Datei auf der Festplatte angewendet — kein erneuter Download nötig
- Der Film muss in der Bibliothek sein (verbunden über Radarr oder Standalone-Scan), damit die Detailseite angezeigt wird
