---
title: Sublarr Wiki
description: Dokumentation für Sublarr — selbstgehosteter Untertitel-Manager für Anime & Medien
published: true
date: 2026-04-13
---

# Sublarr

Selbstgehosteter Untertitel-Manager für Anime- und Medienbibliotheken. Findet die besten Untertitel, übersetzt sie lokal mit einem eigenen LLM-Modell und hält alles synchron mit dem *arr-Stack.

> **Aktuell:** v0.51.3-beta — OpenAPI-Security auf allen 286 Routen, 3600+ Tests, Circuit-Breaker Auth-Propagation, Firefox WebVTT-Untertitel-Fix, SECURITY.md und MIGRATION.md fuer V1-Bereitschaft. (siehe [Upgrade-Leitfaden](/getting-started/upgrade-guide))  <!-- Bei jedem Release aktualisieren — Source of Truth: `backend/VERSION` -->

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

## Links

- [sublarr.app](https://sublarr.app) — Landing Page
- [GitHub](https://github.com/Abrechen2/sublarr) — Quellcode & Releases
- [HuggingFace](https://huggingface.co/Sublarr) — Eigenes Anime-Übersetzungsmodell
- [Spenden](https://www.paypal.com/donate?hosted_button_id=GLXYTD3FV9Y78) — Entwicklung unterstützen
