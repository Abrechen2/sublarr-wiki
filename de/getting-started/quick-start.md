---
title: Schnellstart
description: Sublarr mit dem *arr-Stack verbinden und erste Untertitel finden
published: true
date: 2026-03-14
---

# Schnellstart

## 1. Sublarr öffnen

Nach dem Start mit `docker compose up -d` die Adresse [http://localhost:5765](http://localhost:5765) im Browser öffnen.

## 2. Provider konfigurieren

Zu **Settings → Providers** navigieren und mindestens einen Untertitel-Provider aktivieren:

- **AnimeTosho** — ideal für Anime (kein API-Key erforderlich)
- **OpenSubtitles** — große Bibliothek, kostenloses Konto erforderlich
- **Jimaku** — Fokus auf japanische Anime

**Test** klicken, um die Erreichbarkeit des Providers zu prüfen.

## 3. Sprachprofil einrichten

Zu **Settings → Profiles → Language Profiles** navigieren und ein Profil erstellen:
- **Source language:** Englisch (en)
- **Target language:** Deutsch (de) oder die bevorzugte Sprache
- **Minimum score:** 60 (empfohlen)

## 4. Medienserver verbinden

Zu **Settings → Integrations** navigieren und Jellyfin oder Emby hinzufügen:
- Server-URL und API-Key eingeben
- **Test Connection** klicken

Sublarr scannt die Bibliothek und füllt die Wanted-Liste mit fehlenden Untertiteln.

## 5. Sonarr / Radarr verbinden (optional)

Zu **Settings → Integrations → Sonarr** (oder Radarr) navigieren:
- URL eingeben: `http://sonarr:8989`
- API-Key eingeben (zu finden unter Sonarr → Settings → General)
- Webhooks aktivieren: In Sonarr einen Webhook auf `http://sublarr:5765/api/v1/webhook/sonarr` einrichten

Neue Downloads lösen nun automatisch eine Untertitelsuche aus.

## 6. Untertitel suchen

Zur Seite **Wanted** wechseln — **Search All** klicken, um die Untertitelsuche für die gesamte Bibliothek zu starten.

> **Tipp:** Der erste Scan kann bei großen Bibliotheken eine Weile dauern. Den Fortschritt unter **Activity → Tasks** verfolgen.
