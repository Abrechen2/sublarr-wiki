---
title: Allgemeine Fehlerbehebung
description: Häufige Sublarr-Probleme und deren Lösung
published: true
date: 2026-03-14
---

# Fehlerbehebung

## Untertitel werden nicht heruntergeladen

Häufige Ursachen:

1. **Kein passender Provider gefunden** — prüfen, welche Provider aktiviert sind und API-Keys besitzen
2. **Provider-Circuit-Breaker OPEN** — ein Provider ist zu oft fehlgeschlagen; Cooldown abwarten oder in den Einstellungen zurücksetzen
3. **Eintrag nicht in der Wanted-Queue** — auf der Wanted-Seite prüfen, ob die Datei gescannt wurde
4. **Path Mapping falsch** — wenn Sonarr und Sublarr unterschiedliche Mount-Pfade nutzen, `SUBLARR_PATH_MAPPING` setzen
5. **Kein Sprachprofil zugewiesen** — die Serie benötigt ein in der Bibliothek zugewiesenes Sprachprofil

Auf den Seiten **Queue** und **Activity** nach Fehlerdetails schauen.

## Übersetzung startet nicht

1. Prüfen, ob Ollama erreichbar ist: `curl http://ollama-host:11434/api/tags`
2. Prüfen, ob das Modell gepullt ist: `ollama list`
3. Die Queue-Seite auf fehlgeschlagene Übersetzungsjobs prüfen
4. Container-Logs einsehen: `docker logs sublarr`

## Container-Logs

```bash
docker logs sublarr --tail 100 --follow
```

Debug-Logging aktivieren:

```
SUBLARR_LOG_LEVEL=DEBUG
```

## Health Check

```bash
curl http://localhost:5765/api/v1/health/detailed
```

Gibt den Status aller 11 Subsysteme zurück: Provider, Übersetzungs-Backends, Medienserver, Scheduler, Datenbank.

## Pre-commit Hooks (Entwicklung)

```bash
# Pre-commit installieren
pip install pre-commit
pre-commit install
```

## CI-Pipeline-Probleme

- GitHub-Actions-Logs auf spezifische Fehler prüfen
- Sicherstellen, dass `.env.example` alle erforderlichen Variablen enthält
- Tests lokal ausführen: `pytest` oder `npm test`

## Weitere Hilfe

1. Ein [Issue auf GitHub](https://github.com/Abrechen2/sublarr/issues) eröffnen mit:
   - Fehlermeldung
   - Schritte zur Reproduktion
   - Systeminfos (OS, Docker-Version)
2. Logs prüfen: `backend/logs/` oder Browser-Konsole
