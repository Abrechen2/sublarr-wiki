---
title: Wanted
description: Automatic missing subtitle detection and search
published: true
date: 2026-03-14T19:00:48.624Z
tags: 
editor: markdown
dateCreated: 2026-03-14T18:05:06.256Z
---

# Wanted

The Wanted list tracks all episodes and movies in your library that are missing subtitles matching your [Language Profile](/user-guide/settings/profiles). Sublarr automatically populates this list during scheduled scans and immediately via webhook events from Sonarr and Radarr.

## What the Wanted List Shows

Each row in the Wanted list represents one episode or movie that is missing at least one target-language subtitle. The columns are:

- **Title** — series name + episode (S01E01) or movie title
- **Language** — target language that is missing
- **Added** — when this item was added to the list
- **Attempts** — how many provider searches have already been tried
- **Status** — current status badge (see below)
- **Actions** — Search, Process, Ignore buttons

## Status Badges

| Badge | Meaning |
|-------|---------|
| **Wanted** (amber) | Waiting to be searched — no attempt made yet |
| **Searching** (blue, pulsing) | Active provider search running right now |
| **Found** (green) | A matching subtitle was found and downloaded successfully |
| **Failed** (red) | All search attempts exhausted — no match found |
| **Ignored** (gray) | Manually excluded from future searches |
| **Extracted** (teal) | Subtitle was extracted from an embedded stream rather than downloaded from a provider |

Items with status **Found** or **Extracted** are removed from the active Wanted list but remain visible if you enable the *Show completed* filter toggle.

## Automatic Scan Cycle

Sublarr runs a background scanner on a configurable schedule to keep the Wanted list up to date:

- **Default interval:** every 6 hours (configurable via `SUBLARR_WANTED_SCAN_INTERVAL_HOURS`)
- **Incremental scan:** only items modified since the last scan are re-checked — fast and lightweight
- **Full scan:** forced every 6th cycle as a safety net, or triggered manually via the **Scan Now** button
- **Startup scan:** optionally run on container start with `SUBLARR_WANTED_SCAN_ON_STARTUP=true`

The scanner checks all series and movies in the library against your Language Profile. Any episode missing a target-language subtitle is added to the Wanted list with status *Wanted*.

> **Note:** If you use Sonarr/Radarr webhooks, new downloads are processed immediately without waiting for the next scan cycle. Configure webhooks in Sonarr/Radarr > Settings > Connect. See [Getting Started](/getting-started/quick-start) for setup instructions.

## Extraction of Embedded Subtitles

Before searching external providers, the Wanted scanner optionally checks MKV files for embedded subtitle streams that match your target language. If `SUBLARR_WANTED_AUTO_EXTRACT=true`, matching streams are extracted to sidecar files automatically and the item status is set to **Extracted** instead of remaining **Wanted**.

This is especially useful for anime releases where fansub subtitles are embedded in the MKV and you simply want to surface them as sidecar files for your media server.

> **Note:** Auto-extract does not modify the video file. It only copies the subtitle stream into a `.{lang}.ass` or `.{lang}.srt` file alongside the video.

## Manual Search

To trigger an immediate search for a single item:

1. Find the item in the Wanted list
2. Click the **Search** button (magnifying glass icon)
3. Sublarr queries all enabled providers and returns scored results
4. Review the results in the search modal — each result shows provider, score, format, and release name
5. Click **Download** on the result you want, or let Sublarr auto-pick the highest score

To download and translate the best available result without reviewing:

1. Click **Process** (lightning bolt icon)
2. Sublarr downloads the top-scored result and, if the Language Profile includes a translation backend, queues it for translation automatically

## Batch Search

To search multiple items at once:

1. Select items using the checkbox column (click the header checkbox to select all visible)
2. Click **Batch Search** in the toolbar
3. Sublarr queues a provider search for each selected item
4. Progress is visible on the [Activity](/user-guide/activity) page

The batch search respects `SUBLARR_WANTED_SEARCH_MAX_ITEMS_PER_RUN` (default: 50 items per scheduler run).

## Ignore Functionality

If you do not want subtitles for a specific item (e.g., a bonus episode or a language you no longer need):

1. Click the **Ignore** button on the item
2. The status changes to **Ignored** (gray)
3. The item is excluded from all future automatic searches
4. It remains visible in the list when *Show ignored* is toggled on

To un-ignore an item, click **Ignore** again — the status returns to **Wanted** and the next scan cycle will include it.

## Cleanup Sidecars

After batch-extracting embedded subtitles, you may end up with extra-language sidecar files (e.g., Japanese `.ja.ass` files when you only want German). The **Cleanup Sidecars** button removes these according to your auto-cleanup configuration.

1. Click **Cleanup Sidecars** in the Wanted toolbar
2. A confirmation dialog appears showing which files would be deleted and which would be kept
3. Click **Confirm** to proceed

> **Warning:** Sidecar cleanup permanently deletes files. The subtitle trash does not apply to this operation. Configure `SUBLARR_AUTO_CLEANUP_KEEP_LANGUAGES` carefully before running.

## Adaptive Backoff

Items that repeatedly fail are subject to exponential backoff — the gap between retry attempts grows automatically to avoid hammering providers with requests for obscure items:

| Setting | Default | Description |
|---------|---------|-------------|
| `SUBLARR_WANTED_ADAPTIVE_BACKOFF_ENABLED` | `true` | Enable exponential backoff for failing items |
| `SUBLARR_WANTED_BACKOFF_BASE_HOURS` | `1.0` | Starting backoff interval in hours |
| `SUBLARR_WANTED_BACKOFF_CAP_HOURS` | `168` | Maximum backoff (7 days) |

After a manual **Search** action, the backoff is reset regardless of previous failures.

## Wanted Settings Reference

| Setting | Default | Description |
|---------|---------|-------------|
| `SUBLARR_WANTED_SCAN_INTERVAL_HOURS` | `0` (off) | Scan interval in hours; `0` = event-driven only |
| `SUBLARR_WANTED_SCAN_ON_STARTUP` | `false` | Run scan on container start |
| `SUBLARR_WANTED_ANIME_ONLY` | `true` | Restrict scan to anime series |
| `SUBLARR_WANTED_MAX_SEARCH_ATTEMPTS` | `3` | Max provider attempts per item before marking Failed |
| `SUBLARR_WANTED_AUTO_EXTRACT` | `false` | Auto-extract embedded subs during scan |
| `SUBLARR_WANTED_AUTO_TRANSLATE` | `false` | Auto-translate after auto-extract |
| `SUBLARR_WANTED_SEARCH_INTERVAL_HOURS` | `24` | How often the auto-search scheduler runs |
| `SUBLARR_WANTED_SEARCH_MAX_ITEMS_PER_RUN` | `50` | Items processed per scheduler run |

All of these are also configurable in **Settings → Automation** without editing environment variables.
