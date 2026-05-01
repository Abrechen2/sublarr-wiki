---
title: Sublarr Wiki
description: Documentation for Sublarr — self-hosted subtitle manager for anime & media
published: true
date: 2026-04-15
---

# Sublarr

Self-hosted subtitle manager for anime & media libraries. Finds the best subtitles, translates them locally with a custom LLM model, and keeps everything in sync with your *arr stack.

> **Latest:** v0.82.0-beta — **Live OpenAPI/Swagger discovery + clean episode/pill action split + unified embedded-extractor pipeline.** Every instance now ships an interactive Swagger UI at `/api/docs` (anonymous-readable) plus the raw OpenAPI 3.0.3 spec at `/api/v1/openapi.json` — 243 paths / 288 operations / 27 tags / 5 reusable Pydantic components (`ErrorResponse`, `WantedItem`, `LanguageProfile`, `CleanupRule`, `SubtitleSidecar`) — designed as the foundation for upcoming request validation, frontend codegen, and contract testing. Series view restructured around a strict episode-vs-pill action split: per-language sidecar pills carry every sidecar-bound action (Vorschau, Editor, Download, NFO, HI removal, Common Fixes, Timing modal, Auto-Sync, Video-Sync, Health-Check, Restore); the per-episode `⋯` keeps only episode-scope actions (Vergleichen, Embedded Tracks, Interactive Search, History) — collapsing ~22 row-controls down to ~8 with no `firstSubPath` heuristic. Single-item `/wanted/<id>/extract`, batch-probe, and the auto-extract drain now share one pipeline (`services.embedded_extractor.extract_and_cleanup`) — sidecars correctly named by source-stream language, off-target sidecars trashed, response includes `extracted_count` / `sidecars_trashed`. New: **Subtitle backup management page** (`/settings/cleanup/subtitle-backups`) listing every `.bak.<ext>` with language pill, modifier badges (HI/FORCED/SDH/CC), parent video, age, orphan status — supports bulk purge orphans, purge aged (using `subtitle_bak_retention_days`), per-row Restore (atomic 3-step swap-rename, fully reversible) and Delete. Carry-over since 0.71: 12-backend translation platform (Lingarr parity) + 29-provider subtitle delivery (Bazarr parity) + named-class scoring penalty pipeline + multi-engine sync orchestrator + granular blacklist + persistent Subtitle Automation queue + LanguageProfile inheritance + Settings template scaffold + 100% EN/DE i18n parity. (see [Upgrade Guide](/getting-started/upgrade-guide))  <!-- Update at each release — source of truth: `backend/VERSION` -->

---

## Getting Started

| | |
|---|---|
| [Installation](/getting-started/installation) | Docker, Docker Compose, environment variables |
| [Quick Start Guide](/getting-started/quick-start) | Connect your *arr apps and find your first subtitles |
| [Environment Variables](/getting-started/environment-variables) | All `SUBLARR_*` configuration options |
| [Upgrade Guide](/getting-started/upgrade-guide) | Upgrading between versions, migration notes |
| [FAQ](/getting-started/faq) | Frequently asked questions |

## User Guide

| | |
|---|---|
| [Library](/user-guide/library) | Browsing and managing your media library |
| [Wanted](/user-guide/wanted) | Automatic missing subtitle detection and search |
| [Activity](/user-guide/activity) | Translation jobs, download history |
| [Language Profiles](/user-guide/language-profiles) | Per-series language targeting |
| [Translation & LLM](/user-guide/translation-llm) | Ollama, custom anime model, translation pipeline |
| [Integrations](/user-guide/integrations) | Sonarr, Radarr, Jellyfin, Emby, Plex, Kodi |
| [Cleanup](/user-guide/cleanup) | Deduplication, orphan detection, format upgrades |
| [Post-Processing](/user-guide/post-processing) | Shell command, video sync, HI removal, credit filtering |
| [Movie Subtitles](/user-guide/movie-subtitles) | Radarr movie subtitle management |
| [Localization](/user-guide/localization) | UI language (DE/EN), 74 subtitle languages |
| [Circuit Breaker](/user-guide/advanced/circuit-breaker) | Provider resilience and failure isolation |
| [Settings](/user-guide/settings/general) | Full settings reference |

## Troubleshooting

| | |
|---|---|
| [General Troubleshooting](/troubleshooting/general) | Common issues and solutions |
| [Reverse Proxy Guide](/troubleshooting/reverse-proxy) | nginx, Caddy, NPM setup |
| [Performance Tuning](/troubleshooting/performance-tuning) | Large libraries, translation throughput |

## Development

| | |
|---|---|
| [Architecture](/development/architecture) | System design, component overview |
| [Plugin Development](/development/plugin-development) | Writing custom provider/hook plugins |
| [API Reference](/development/api-reference) | REST API endpoints |
| [Database Schema](/development/database-schema) | SQLite tables and relationships |
| [PostgreSQL Setup](/development/postgresql) | Switching to PostgreSQL |
| [Contributing](/development/contributing) | Development workflow, PR guidelines |

---

## Community

- 💬 [Discord](https://discord.gg/WjatsKzHXz) — live chat, install help, beta testing
- 🔴 [Reddit /r/Sublarr](https://www.reddit.com/r/Sublarr/) — announcements, showcases, discussions
- 🐙 [GitHub Issues](https://github.com/Abrechen2/sublarr/issues) — bug reports & feature requests

## Links

- [sublarr.de](https://sublarr.de) — Landing page
- [GitHub](https://github.com/Abrechen2/sublarr) — Source code & releases
- [HuggingFace](https://huggingface.co/Sublarr) — Custom anime translation model
- [Donate](https://www.paypal.com/donate?hosted_button_id=GLXYTD3FV9Y78) — Support development
