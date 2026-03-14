---
title: Upgrade Guide
description: Upgrading Sublarr between versions — migration notes and breaking changes
published: true
date: 2026-03-14T18:59:44.788Z
tags: 
editor: markdown
dateCreated: 2026-03-14T18:04:38.961Z
---

# Upgrade Guide

## Current Version: 0.29.0-beta

This guide covers upgrades from any previous version of Sublarr. Database migrations run **automatically on startup** — no manual SQL required.

For switching from SQLite to PostgreSQL, see [PostgreSQL Setup](/development/postgresql).

---

## Standard Upgrade (Docker)

```bash
# 1. Pull new image
docker pull ghcr.io/abrechen2/sublarr:0.29.0-beta

# 2. Backup your config (recommended)
cp -r /path/to/appdata/sublarr /path/to/appdata/sublarr.backup

# 3. Stop and restart with new image
docker compose pull && docker compose up -d
```

> **Note:** Always back up your `/config` volume before upgrading. Migrations are automatic and non-destructive, but a backup is the safety net.

### Unraid

1. Go to Docker tab → click the Sublarr container icon → **Edit**
2. Update the Repository field to `ghcr.io/abrechen2/sublarr:0.29.0-beta`
3. Click **Apply** — the container re-pulls and restarts automatically

---

## What Changed Per Version

### v0.29.0-beta — Web Player

**New features:**
- Built-in video player (HTML5 `<video>`) accessible via "Preview" button on episode cards
- ASS/SRT subtitle overlay using SubtitleOctopus (libass WASM) — styled ASS subtitles rendered natively in-browser
- Subtitle track selector — switch between all available sidecar files; "Off" to disable overlay
- Seek-to-cue — clicking a cue in the SubtitleEditorModal jumps the player to that timestamp
- `GET /api/v1/media/stream?path=` streaming endpoint with HTTP 206 range-request support
- New setting: `streaming_enabled` in Settings → Automation (default: on)
- New env var: `SUBLARR_STREAMING_ENABLED` (default `true`)

**No breaking changes.** Existing configurations require no changes.

---

### v0.28.0-beta — AI Glossary Builder

**New features:**
- AI-powered glossary with character/place/other term types, confidence scoring, and approval workflow
- `POST /api/v1/series/<id>/glossary/suggest` — auto-detects recurring proper nouns from subtitle sidecars
- `GET /api/v1/glossary/export` — export approved terms as TSV
- Approved glossary terms are injected into the LLM system prompt during translation (capped at 50 terms)
- V8 single-line fast-path: `Translate to German: {line}` when subtitle has exactly one cue
- New config: `SUBLARR_GLOSSARY_ENABLED` (default `true`), `glossary_max_terms` (default 100)

**DB migration:** Adds `term_type`, `confidence`, `approved` columns to `glossary_entries` — automatic.

---

### v0.27.0-beta — NFO Export

**New features:**
- XML `.nfo` sidecar files alongside downloaded/translated subtitles (optional, off by default)
- `POST /api/v1/subtitles/export-nfo` — single-file export
- `POST /api/v1/series/<id>/subtitles/export-nfo` — bulk export for a series
- Enable via `auto_nfo_export` in Settings → Automation (advanced section)

**No breaking changes.**

---

### v0.26.0-beta — Single-Account Login

**New features:**
- Optional UI authentication (username/password via Flask session)
- First-run setup wizard at `/setup`
- `AuthGuard` in React — redirects to `/setup` or `/login` as needed
- Settings → Security tab: enable/disable auth, change password
- Sidebar logout button
- `POST /auth/setup`, `/auth/login`, `/auth/logout`, `/auth/change-password`, `/auth/toggle`

> **Note:** Authentication is **off by default**. Existing instances continue to work without any changes. Enable it in Settings → Security if desired.

---

### v0.25.x — CLI, Subtitle Diff, List Virtualization

**v0.25.3:** Library table and Wanted list now use virtual scroll (`@tanstack/react-virtual`) — no more pagination limits on large libraries.

**v0.25.2:** Subtitle Diff Viewer with per-cue accept/reject workflow.

**v0.25.1:** `sublarr` CLI tool — `search`, `translate`, `sync`, `status` commands. Configure via `SUBLARR_URL` + `SUBLARR_API_KEY`.

---

## Troubleshooting Upgrades

### Database migration failed

```bash
# Check container logs
docker logs sublarr

# Restore from backup if needed
docker stop sublarr
cp /path/to/appdata/sublarr.backup/sublarr.db /path/to/appdata/sublarr/sublarr.db
docker start sublarr
```

You can also restore from a UI backup: Settings → Backup → Restore.

### Container won't start after upgrade

1. Check logs: `docker logs sublarr`
2. Verify volume mounts: `/config` and `/media` paths must exist and be accessible
3. Check environment variables — see [Environment Variables](/getting-started/environment-variables) for the full reference

### "Missing subtitles" after upgrade

The incremental wanted scanner runs every 6 hours. To force a full rescan immediately:

1. Go to **Wanted** → click **Scan Now**
2. Or: `POST /api/v1/wanted/scanner/trigger` via the API

---

## Questions or Issues?

- Open a [GitHub Issue](https://github.com/Abrechen2/sublarr/issues)
- Check the [Troubleshooting Guide](/troubleshooting/general)
- Browse the API docs at `http://your-sublarr-host:5765/api/docs`
