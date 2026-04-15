---
title: Sublarr Wiki
description: Documentation for Sublarr — self-hosted subtitle manager for anime & media
published: true
date: 2026-04-15
---

# Sublarr

Self-hosted subtitle manager for anime & media libraries. Finds the best subtitles, translates them locally with a custom LLM model, and keeps everything in sync with your *arr stack.

> **Latest:** v0.51.17-beta — Sonarr/Radarr webhooks drive the full download-translate-extract-sync pipeline end-to-end, `ffsubsync` bundled for automatic subtitle-to-video alignment, post-extract sidecar cleanup by language profile, scheduled cleanup rules (orphan DB / old backups / format upgrade), and the save-subtitle return-path contract is now enforced so auto-sync always targets the real on-disk file. (see [Upgrade Guide](/getting-started/upgrade-guide))  <!-- Update at each release — source of truth: `backend/VERSION` -->

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

## Links

- [sublarr.app](https://sublarr.app) — Landing page
- [GitHub](https://github.com/Abrechen2/sublarr) — Source code & releases
- [HuggingFace](https://huggingface.co/Sublarr) — Custom anime translation model
- [Donate](https://www.paypal.com/donate?hosted_button_id=GLXYTD3FV9Y78) — Support development
