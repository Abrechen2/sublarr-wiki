---
title: Wanted
description: Automatische Erkennung und Suche fehlender Untertitel
published: true
date: 2026-03-14
---

# Wanted

Die Wanted-Liste erfasst alle Episoden und Filme in der Bibliothek, denen Untertitel gemäß dem zugewiesenen Sprachprofil fehlen. Sublarr füllt diese Liste automatisch aus den verbundenen Sonarr-/Radarr-Instanzen, Standalone-Ordnern oder einer Kombination aus beidem.

### Wanted-System

Das Wanted-System verfolgt Medieneinträge mit fehlenden Untertiteln.

**Funktionsweise:**
1. **Scan:** Prüft periodisch die Sonarr-/Radarr-Bibliotheken (oder Standalone-Ordner) auf Einträge ohne Untertitel in der Zielsprache
2. **Inkrementeller Scan:** Prüft nur seit dem letzten Scan geänderte Einträge (vollständiger Scan jeden 6. Zyklus)
3. **Suche:** Fragt alle aktivierten Provider nach passenden Untertiteln ab
4. **Download:** Lädt das Ergebnis mit dem höchsten Score herunter
5. **Übersetzung:** Schickt den Untertitel bei Bedarf durch die Übersetzungs-Pipeline

**Manuelle Aktionen:**
- **Suchen** bei einem Wanted-Eintrag klicken, um eine sofortige Suche auszulösen
- **Verarbeiten** klicken, um das beste verfügbare Ergebnis herunterzuladen und zu übersetzen
- **Batch-Suche** für mehrere Einträge gleichzeitig nutzen
