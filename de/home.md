---
title: Sublarr Wiki
description: Dokumentation für Sublarr — selbstgehosteter Untertitel-Manager für Anime & Medien
published: true
date: 2026-04-01
---

# Sublarr

Selbstgehosteter Untertitel-Manager für Anime- und Medienbibliotheken. Findet die besten Untertitel, übersetzt sie lokal mit einem eigenen LLM-Modell und hält alles synchron mit dem *arr-Stack.

> **Aktuell:** v0.47.3-beta — Film-Untertitelverwaltung, 74 Sprachoptionen, vollständige DE/EN-UI-Lokalisierung, neu gestaltete Cleanup-Seite mit 5 festen Operationen und Dry-Run-Dateivorschau, persistente Settings-Navigation, Post-Processing-UI und Untertitel-Präsenz-Pillen auf der Wanted-Seite. (siehe [Upgrade-Leitfaden](/getting-started/upgrade-guide))  <!-- Bei jedem Release aktualisieren — Source of Truth: `backend/VERSION` -->

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
| [Web Player](/user-guide/web-player) | Video-Player im Browser mit Untertitel-Overlay |
| [Wellenform-Editor](/user-guide/waveform-editor) | Timeline-basierte Untertitelbearbeitung |
| [AI-Glossar](/user-guide/ai-glossary) | Wiederkehrende Begriffe automatisch für konsistente Übersetzung extrahieren |
| [Video Sync](/user-guide/video-sync) | Untertitel per ffsubsync / alass an Audio synchronisieren |
| [Benachrichtigungen](/user-guide/notifications) | Push-Benachrichtigungen via Apprise |
| [Post-Processing](/user-guide/post-processing) | Shell-Befehl nach Untertitel-Download |
| [Circuit Breaker](/user-guide/advanced/circuit-breaker) | Provider-Resilienz und Fehlerisolierung |
| [Untertitel-Papierkorb](/user-guide/subtitle-trash) | Wiederherstellungsfenster für gelöschte Untertitel |
| [Credit-Filterung](/user-guide/credit-filtering) | Opening-/Ending-Credit-Zeilen entfernen |
| [HI-Entfernung](/user-guide/hi-removal) | Hörgeschädigten-Annotationen entfernen |
| [Stream-Entfernung](/user-guide/stream-removal) | Remux: eingebettete Untertitel-Streams entfernen |
| [Einstellungen](/user-guide/settings/general) | Vollständige Einstellungsreferenz |
| [Sprachprofile](/user-guide/language-profiles) | Sprach-Zuweisung pro Serie |
| [Übersetzung & LLM](/user-guide/translation-llm) | Ollama, eigenes Anime-Modell, Übersetzungs-Pipeline |
| [Integrationen](/user-guide/integrations) | Sonarr, Radarr, Jellyfin, Emby |

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
