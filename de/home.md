---
title: Sublarr Wiki
description: Dokumentation für Sublarr — selbstgehosteter Untertitel-Manager für Anime & Medien
published: true
date: 2026-04-13
---

# Sublarr

Selbstgehosteter Untertitel-Manager für Anime- und Medienbibliotheken. Findet die besten Untertitel, übersetzt sie lokal mit einem eigenen LLM-Modell und hält alles synchron mit dem *arr-Stack.

> **Aktuell:** v0.82.0-beta — **Live OpenAPI/Swagger-Discovery + sauberer Episode-vs-Pill-Action-Split + vereinheitlichte Embedded-Extractor-Pipeline.** Jede Sublarr-Instanz liefert jetzt eine interaktive Swagger-UI unter `/api/docs` (anonym lesbar) + die Roh-Spec OpenAPI 3.0.3 unter `/api/v1/openapi.json` aus — 243 Paths / 288 Operations / 27 Tags / 5 wiederverwendbare Pydantic-Components (`ErrorResponse`, `WantedItem`, `LanguageProfile`, `CleanupRule`, `SubtitleSidecar`) — als Fundament für kommende Request-Validation, Frontend-Codegen und Contract-Testing. Series-View nach striktem Episode-vs-Pill-Schnitt umstrukturiert: per-Language-Sidecar-Pills tragen jede sidecar-spezifische Aktion (Vorschau, Editor, Download, NFO, HI entfernen, Common Fixes, Timing-Modal, Auto-Sync, Video-Sync, Health-Check, Restore); das Episoden-`⋯` enthält nur noch folgenübergreifende Aktionen (Vergleichen, Embedded Tracks, Interactive Search, History) — von ~22 auf ~8 Steuerelemente reduziert ohne `firstSubPath`-Heuristik. Single-Item-`/wanted/<id>/extract`, Batch-Probe und der Auto-Extract-Drain teilen sich jetzt eine Pipeline (`services.embedded_extractor.extract_and_cleanup`) — Sidecars korrekt nach Quell-Stream-Sprache benannt, Off-Target-Sidecars in den Trash verschoben, Response enthält `extracted_count` / `sidecars_trashed`. Neu: **Untertitel-Backup-Verwaltungsseite** (`/settings/cleanup/subtitle-backups`) listet jede `.bak.<ext>`-Datei mit Sprach-Pill, Modifier-Badges (HI/FORCED/SDH/CC), Eltern-Video, Alter, Orphan-Status — unterstützt Bulk-Purge Waisen, Bulk-Purge nach Alter (über `subtitle_bak_retention_days`), Per-Zeile Restore (atomarer 3-Schritt-Swap-Rename, voll reversibel) und Löschen. Carry-Over seit 0.71: 12-Backend-Übersetzungsplattform (Lingarr-Parität) + 29-Provider-Untertitelauslieferung (Bazarr-Parität) + benannte Scoring-Penalty-Pipeline + Multi-Engine-Sync-Orchestrator + granulare Blacklist + persistente Subtitle-Automation-Queue + LanguageProfile-Vererbung + Settings-Template-Gerüst + 100% EN/DE-i18n-Parität. (siehe [Upgrade-Leitfaden](/getting-started/upgrade-guide))  <!-- Bei jedem Release aktualisieren — Source of Truth: `backend/VERSION` -->

---

## Erste Schritte

| | |
|---|---|
| [Installation](/getting-started/installation) | Docker, Docker Compose, Umgebungsvariablen |
| [Schnellstart](/getting-started/quick-start) | *arr-Apps verbinden und erste Untertitel finden |
| [Umgebungsvariablen](/getting-started/environment-variables) | Alle `SUBLARR_*`-Konfigurationsoptionen |
| [Upgrade-Leitfaden](/getting-started/upgrade-guide) | Versionswechsel, Migrationshinweise |
| [FAQ](/getting-started/faq) | Häufig gestellte Fragen |

## Benutzerhandbuch

| | |
|---|---|
| [Bibliothek](/user-guide/library) | Medienbibliothek durchsuchen und verwalten |
| [Wanted](/user-guide/wanted) | Automatische Erkennung und Suche fehlender Untertitel |
| [Aktivität](/user-guide/activity) | Übersetzungsjobs, Download-Verlauf |
| [Sprachprofile](/user-guide/language-profiles) | Sprach-Zuweisung pro Serie |
| [Übersetzung & LLM](/user-guide/translation-llm) | Ollama, eigenes Anime-Modell, Übersetzungs-Pipeline |
| [Integrationen](/user-guide/integrations) | Sonarr, Radarr, Jellyfin, Emby, Plex, Kodi |
| [Bereinigung](/user-guide/cleanup) | Deduplizierung, Waisen-Erkennung, Format-Upgrades |
| [Post-Processing](/user-guide/post-processing) | Shell-Befehle, Video-Sync, HI-Entfernung, Credit-Filterung |
| [Film-Untertitel](/user-guide/movie-subtitles) | Radarr Film-Untertitelverwaltung |
| [Lokalisierung](/user-guide/localization) | UI-Sprache (DE/EN), 74 Untertitel-Sprachen |
| [Circuit Breaker](/user-guide/advanced/circuit-breaker) | Provider-Resilienz und Fehlerisolierung |
| [Einstellungen](/user-guide/settings/general) | Vollständige Einstellungsreferenz |

## Fehlerbehebung

| | |
|---|---|
| [Allgemeine Fehlerbehebung](/troubleshooting/general) | Häufige Probleme und Lösungen |
| [Reverse-Proxy-Leitfaden](/troubleshooting/reverse-proxy) | nginx, Caddy, NPM einrichten |
| [Performance-Optimierung](/troubleshooting/performance-tuning) | Große Bibliotheken, Übersetzungsdurchsatz |

## Entwicklung

| | |
|---|---|
| [Architektur](/development/architecture) | Systemdesign, Komponentenübersicht |
| [Plugin-Entwicklung](/development/plugin-development) | Eigene Provider-/Hook-Plugins schreiben |
| [API-Referenz](/development/api-reference) | REST-API-Endpunkte |
| [Datenbankschema](/development/database-schema) | SQLite-Tabellen und Beziehungen |
| [PostgreSQL-Setup](/development/postgresql) | Auf PostgreSQL umstellen |
| [Mitwirken](/development/contributing) | Entwicklungs-Workflow, PR-Richtlinien |

---

## Community

- 💬 [Discord](https://discord.gg/WjatsKzHXz) — Live-Chat, Installations-Hilfe, Beta-Testing
- 🔴 [Reddit /r/Sublarr](https://www.reddit.com/r/Sublarr/) — Ankündigungen, Showcases, Diskussionen
- 🐙 [GitHub Issues](https://github.com/Abrechen2/sublarr/issues) — Bug-Reports & Feature-Wünsche

## Links

- [sublarr.de](https://sublarr.de) — Landing Page
- [GitHub](https://github.com/Abrechen2/sublarr) — Quellcode & Releases
- [HuggingFace](https://huggingface.co/Sublarr) — Eigenes Anime-Übersetzungsmodell
- [Spenden](https://www.paypal.com/donate?hosted_button_id=GLXYTD3FV9Y78) — Entwicklung unterstützen
