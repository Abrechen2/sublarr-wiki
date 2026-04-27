---
title: Sublarr Wiki
description: Documentation for Sublarr — self-hosted subtitle manager for anime & media
published: true
date: 2026-04-15
---

# Sublarr

Self-hosted subtitle manager for anime & media libraries. Finds the best subtitles, translates them locally with a custom LLM model, and keeps everything in sync with your *arr stack.

> **Latest:** v0.76.7-beta — **Plan A + Plan B complete + Subtitle Automation pipeline + Inheritance system + Settings template scaffold.** Translation platform reaches Lingarr parity (12 backends: Ollama, OpenAI-Compat, Claude, Gemini, DeepSeek, Mistral, ChatGPT, DeepL, Google, LibreTranslate, Azure Translator, MyMemory) with live queue dashboard + cost tracking + concurrency + context-windowing. Subtitle delivery reaches Bazarr parity: **29 providers** (16 native + 7 Subliminal-flavor via adapter), **named-class scoring penalty pipeline** (15 configurable rules), **subtitle repair** on every save path (BOM / newlines / decimals / overlaps / encoding), **embedded track-selection** by language+forced+HI flags, **post-processing pipeline** with 8 ops + opt-in shell escape, **multi-engine sync orchestrator** with fallback chain + audit trail, **granular blacklist** by file-hash. New since 0.71: **persistent Subtitle Automation queue** (drain worker + SDH tolerance + foreign-track cleanup), **per-series cleanup override** + 3-state StatusStripe, **LanguageProfile inheritance** (backend API + per-series/movie selectors + Library bulk-assign toolbar), **15 Settings pages migrated** to the Codex template scaffold (FormLayout / CollectionLayout / RulesLayout) + sticky section TOCs, **i18n EN/DE parity** at 100% across 10 namespace files (2611 keys), and the 0.76.7 **slow-mode bypass** + retroactive backlog resurrection migration that returns 2032 frozen wanted-items to the search rotation over a 30-day window. (see [Upgrade Guide](/getting-started/upgrade-guide))  <!-- Update at each release — source of truth: `backend/VERSION` -->

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
