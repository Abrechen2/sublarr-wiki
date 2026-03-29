---
title: Sublarr Wiki
description: Documentation for Sublarr — self-hosted subtitle manager for anime & media
published: true
date: 2026-03-29
---

# Sublarr

Self-hosted subtitle manager for anime & media libraries. Finds the best subtitles, translates them locally with a custom LLM model, and keeps everything in sync with your *arr stack.

> **Latest:** v0.36.0-beta — Standalone auto-mode, Language Profiles (mustContain/cutoff/audioExclude), OpenSubtitles anime fix, score video_codec weight  <!-- Update at each release — source of truth: `backend/VERSION` -->

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
| [Web Player](/user-guide/web-player) | In-browser video player with subtitle overlay |
| [Waveform Editor](/user-guide/waveform-editor) | Timeline-based subtitle editing |
| [AI Glossary](/user-guide/ai-glossary) | Auto-extract recurring terms for consistent translation |
| [Video Sync](/user-guide/video-sync) | Sync subtitles to audio via ffsubsync / alass |
| [Notifications](/user-guide/notifications) | Apprise-based push notifications |
| [Subtitle Trash](/user-guide/subtitle-trash) | Recovery window for deleted subtitles |
| [Credit Filtering](/user-guide/credit-filtering) | Strip opening/ending credit lines |
| [HI Removal](/user-guide/hi-removal) | Remove hearing-impaired annotations |
| [Stream Removal](/user-guide/stream-removal) | Remux: strip embedded subtitle streams |
| [Settings](/user-guide/settings/general) | Full settings reference |
| [Language Profiles](/user-guide/language-profiles) | Per-series language targeting |
| [Translation & LLM](/user-guide/translation-llm) | Ollama, custom anime model, translation pipeline |
| [Integrations](/user-guide/integrations) | Sonarr, Radarr, Jellyfin, Emby |

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
