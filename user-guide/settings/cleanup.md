---
title: Settings — Cleanup
description: Orphan record cleanup, SQLite database maintenance, and provider cache management
published: true
date: 2026-03-15
tags: settings
editor: markdown
dateCreated: 2026-03-15
---

<div class="settings-tabs">
<a class="settings-tab " href="/user-guide/settings/general"><span class="mdi mdi-cog-outline"></span> General</a>
<a class="settings-tab " href="/user-guide/settings/media-management"><span class="mdi mdi-folder-cog-outline"></span> Media Management</a>
<a class="settings-tab " href="/user-guide/settings/profiles"><span class="mdi mdi-translate"></span> Profiles</a>
<a class="settings-tab " href="/user-guide/settings/providers"><span class="mdi mdi-cloud-search-outline"></span> Providers</a>
<a class="settings-tab " href="/user-guide/settings/translation"><span class="mdi mdi-brain"></span> Translation</a>
<a class="settings-tab " href="/user-guide/settings/integrations"><span class="mdi mdi-connection"></span> Integrations</a>
</div>

# Settings — Cleanup

![Settings — Cleanup](/img/screen-settings-cleanup.png)

The Cleanup settings page provides tools for keeping the Sublarr database and cache in a consistent, efficient state. None of these operations touch your media files or subtitle files on disk — they only manage Sublarr's internal data.

## Orphan Cleanup

An **orphaned subtitle record** is a database entry for a subtitle file that no longer exists on disk. Orphans accumulate when subtitle files are moved, renamed, or deleted outside of Sublarr — for example, when you reorganise your media library, or when a file system backup restore replaces files.

Orphan cleanup scans all subtitle file paths recorded in the database and removes any record whose file cannot be found at its stored path. Media records (episodes, movies) are never removed by orphan cleanup — only the subtitle entries.

| Setting | Default | Description |
|---------|---------|-------------|
| Remove orphaned subtitle records | `true` | Enable the automatic orphan cleanup job |
| Run on startup | `false` | Run a full orphan scan every time the container starts. Useful after library reorganisations that may have moved many files. |
| Run interval (hours) | `168` | How often to run orphan cleanup automatically. Default is 168 hours (7 days / once per week). Set to `0` to disable scheduled runs and rely on startup or manual triggers only. |

> [!NOTE]
> Orphan cleanup only removes **database records** — it never deletes files from disk. If you see a subtitle file on disk that has no corresponding database record, run a library scan (via Settings → General or the Wanted page) to re-index it.

**What triggers an orphan:**

- A subtitle file was deleted manually in the file manager
- The media directory was restored from a snapshot that predates the subtitle download
- A rename rule in Sonarr/Radarr moved the media file and Sublarr's path update webhook was not received
- The `/media` volume mount was changed to point to a different directory

After orphan cleanup runs, affected items will reappear on the Wanted list and be re-queued for provider search on the next scheduled run.

---

## Database Maintenance

Sublarr uses SQLite as its database engine. Over time, as records are inserted and deleted, the database file can develop internal fragmentation that wastes disk space and marginally slows queries. The `VACUUM` operation rewrites the database file to reclaim this space.

| Setting | Default | Description |
|---------|---------|-------------|
| VACUUM on startup | `false` | Run `VACUUM` every time the container starts. Increases startup time (typically 1–10 seconds for databases under 500 MB), but ensures the database is always compact. |
| VACUUM interval (days) | `30` | How often to run `VACUUM` automatically. Set to `0` to disable scheduled VACUUM and run it manually only. |
| Database size | *(read-only)* | Current on-disk size of `sublarr.db`. Updated in real time. Displayed for reference when evaluating whether a VACUUM is needed. |

> [!TIP]
> For most libraries, the database remains small (under 50 MB) even with tens of thousands of subtitle records. VACUUM is rarely necessary. Enable it only if you see the database growing unexpectedly or after a large batch deletion (e.g. removing a complete media series).

**VACUUM behaviour:**

- `VACUUM` is a full database rewrite — it creates a new file and atomically replaces the old one. The process requires free disk space approximately equal to the current database size.
- `VACUUM` cannot run while a write transaction is in progress. If a provider search or translation job is running, the VACUUM will wait or be deferred to the next scheduled window.
- `VACUUM` does **not** modify any data — it is purely a storage reorganisation.

---

## Cache Cleanup

Sublarr caches provider search results to avoid redundant API calls when the same title is searched multiple times within a short window. The cache is stored in memory and optionally persisted to disk between restarts.

| Setting | Default | Description |
|---------|---------|-------------|
| Cache TTL (hours) | `24` | How long a cached provider search result is considered valid. After this period, the next search for the same title will query providers again. |
| Clear provider search cache | *(button)* | Immediately invalidate all cached search results. The next search for any title will hit all providers fresh. |

**When to clear the cache:**

- After enabling a new provider — cached results from before it was enabled will not include its results
- After adding or changing API keys — cached negative results (no subtitle found) may be stale
- After adjusting provider priorities — cached results reflect the old priority order
- When troubleshooting a subtitle that should exist but is not found

---

## Manual Cleanup

The **Manual Cleanup** section at the bottom of the page provides one-click buttons for all cleanup operations. These run immediately and are logged to the Activity feed.

| Button | Action |
|--------|--------|
| **Run Orphan Cleanup Now** | Scan all subtitle records and remove those whose files no longer exist on disk. Shows a count of removed records on completion. |
| **Vacuum Database Now** | Run `VACUUM` immediately. The UI shows a spinner and the new database size when complete. |
| **Clear All Caches** | Invalidate all provider search caches. Equivalent to clicking the **Clear provider search cache** button but also clears any other in-memory caches (metadata lookups, artwork, etc.). |

> [!NOTE]
> Manual cleanup operations run in the background. You do not need to wait on the settings page — you can navigate away and check the **Activity** page to see when the operation completed and how many records were affected.
