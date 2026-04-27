---
title: Sublarr Wiki
description: Dokumentation für Sublarr — selbstgehosteter Untertitel-Manager für Anime & Medien
published: true
date: 2026-04-13
---

# Sublarr

Selbstgehosteter Untertitel-Manager für Anime- und Medienbibliotheken. Findet die besten Untertitel, übersetzt sie lokal mit einem eigenen LLM-Modell und hält alles synchron mit dem *arr-Stack.

> **Aktuell:** v0.76.7-beta — **Plan A + Plan B abgeschlossen + Subtitle-Automation-Pipeline + Vererbungssystem + Settings-Template-Gerüst.** Übersetzungsplattform erreicht Lingarr-Parität (12 Backends: Ollama, OpenAI-Compat, Claude, Gemini, DeepSeek, Mistral, ChatGPT, DeepL, Google, LibreTranslate, Azure Translator, MyMemory) mit Live-Queue-Dashboard, Kostenerfassung, Concurrency und Kontextfenstern. Untertitelauslieferung erreicht Bazarr-Parität: **29 Provider** (16 nativ + 7 Subliminal-Adapter), **benannte Scoring-Penalty-Pipeline** (15 konfigurierbare Regeln), **Untertitel-Reparatur** auf jedem Save-Pfad (BOM / Zeilenenden / Dezimalzahlen / Overlaps / Encoding), **Embedded-Track-Auswahl** nach Sprache + Forced + HI, **Post-Processing-Pipeline** mit 8 Ops + opt-in Shell-Escape, **Multi-Engine-Sync-Orchestrator** mit Fallback-Kette + Audit-Trail, **granulare Blacklist** per File-Hash. Neu seit 0.71: **persistente Subtitle-Automation-Queue** (Drain-Worker + SDH-Toleranz + Fremdsprachenspuren-Cleanup), **Per-Serie-Cleanup-Override** + 3-Zustand-StatusStripe, **LanguageProfile-Vererbung** (Backend-API + Per-Serie/Film-Selektoren + Library-Bulk-Assign-Toolbar), **15 Settings-Seiten migriert** auf das Codex-Template-Gerüst (FormLayout / CollectionLayout / RulesLayout) + sticky Section-TOCs, **i18n EN/DE-Parität** zu 100% in 10 Namespace-Dateien (2611 Keys) und der 0.76.7-**Slow-Mode-Bypass** + retroaktive Backlog-Resurrection-Migration, die 2032 eingefrorene Wanted-Items über 30 Tage zurück in die Such-Rotation bringt. (siehe [Upgrade-Leitfaden](/getting-started/upgrade-guide))  <!-- Bei jedem Release aktualisieren — Source of Truth: `backend/VERSION` -->

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
