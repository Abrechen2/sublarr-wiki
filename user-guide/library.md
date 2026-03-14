---
title: Library
description: Browsing and managing your media library
published: true
date: 2026-03-14T19:01:49.518Z
tags: 
editor: markdown
dateCreated: 2026-03-14T18:04:52.627Z
---

# Library

The Library view shows all series and movies that Sublarr is aware of, sourced from your connected Sonarr, Radarr, Jellyfin, Emby, or standalone watched-folder integrations. It is your central dashboard for understanding subtitle coverage and taking manual action on any item.

## Series / Movie Grid

The main Library page displays your media as a sortable list (or grid, depending on your layout preference). Each row or card shows:

- **Title** — series or movie name, plus year
- **Subtitle status summary** — how many episodes have target-language subtitles vs. total episodes (e.g. `12 / 24`)
- **Language profile** — the profile currently assigned (or *Global Default* if none)
- **Last provider** — which provider found the most recent subtitle
- **Format** — whether the active subtitle is ASS or SRT

> **Note:** The Library is populated automatically whenever Sublarr syncs with Sonarr/Radarr or finishes a standalone folder scan. New items appear within seconds of a webhook event.

## Subtitle Status Indicators

Every episode and series carries a status badge:

| Badge | Meaning |
|-------|---------|
| **Subtitled** (green) | Target-language subtitle exists and meets the minimum score |
| **Missing** (red) | No subtitle found for this item yet |
| **Wanted** (amber) | Item is queued in the [Wanted](/user-guide/wanted) list — a search is pending |
| **Searching** (blue pulsing) | Active provider search in progress |
| **Translated** (teal) | Subtitle was downloaded and then passed through the translation pipeline |
| **Error** (orange) | Last search or translation attempt failed — hover for the error message |
| **Extracted** (teal) | Subtitle was extracted from an embedded stream in the video file |

## Series Detail View

Click any series title to open the Series Detail view. This shows a season-by-season, episode-by-episode breakdown.

Each episode row displays:

- Episode number and title
- Subtitle file path (relative to media root)
- Subtitle score and provider
- Format (ASS / SRT)
- Status badge

### Episode Actions

Each episode has an action menu (three-dot icon or right-click):

- **Search** — triggers an immediate provider search for this episode only
- **Translate** — sends the current subtitle through the LLM translation pipeline
- **Play** — opens the [Web Player](/user-guide/web-player) with this episode loaded (added in v0.29.0-beta)
- **Delete** — moves the subtitle to the [Trash](/user-guide/settings/media-management#subtitle-trash) (soft delete, recoverable)
- **Download Raw** — downloads the raw subtitle file to your browser

### Embedded Subtitle Tracks

If the video file contains embedded subtitle streams, they appear in a **Tracks** panel within the episode detail. From here you can:

- Extract a track to a sidecar file (`.ass` or `.srt`) without modifying the video
- Remove a track permanently via mkvmerge / ffmpeg remux (stream removal)
- Undo a removal — a `.bak` backup is kept for the configured retention period (default 7 days)

> **Warning:** Stream removal modifies the video file. The original is backed up to `.sublarr/trash/<date>/` before any changes. Verify the result before the backup expires.

## Filtering and Sorting

Use the filter bar at the top of the Library to narrow results:

| Filter | Options |
|--------|---------|
| **Status** | All, Subtitled, Missing, Wanted, Error |
| **Language profile** | Any profile you have created |
| **Format** | Any, ASS only, SRT only |
| **Provider** | Filter by the provider that delivered the subtitle |
| **Source** | Sonarr, Radarr, Standalone |

Click any column header to sort ascending or descending. The sort state persists across page navigations within the session.

## Per-Series Subtitle Management

### Assigning a Language Profile

1. Open the Series Detail view
2. Click the **Profile** dropdown near the series title
3. Select a profile from the list
4. Click **Save**

The new profile takes effect immediately for future scans and webhook events. Existing subtitles are not retroactively changed unless you trigger a re-search.

### Forced Subtitle Handling

Each series has a **Forced Preference** setting (inherited from the language profile, but overridable per series):

| Option | Behavior |
|--------|---------|
| **Disabled** | Forced subtitle tracks are ignored |
| **Separate** | Forced subs are searched and stored as a separate sidecar (`.forced.ass`) |
| **Auto** | Sublarr detects and handles forced subs automatically based on stream metadata |

For anime with signs/songs in Japanese while dialogue is in English, **Separate** is the recommended setting.

## Batch Operations

Select multiple series using the checkbox column (click the header checkbox to select all visible), then choose a batch action from the toolbar:

- **Batch Search** — queues a provider search for all episodes across selected series that are still missing subtitles
- **Batch Translate** — queues translation for all downloaded-but-untranslated subtitles in selected series
- **Batch Delete** — moves all subtitles for selected series to Trash
- **Assign Profile** — sets the same language profile on all selected series at once

> **Note:** Batch operations are processed as background jobs. Progress is visible on the [Activity](/user-guide/activity) page.

## Sidecar Auto-Cleanup

After batch-extracting embedded subtitles, Sublarr can automatically remove extra-language sidecar files to keep your media directory tidy.

Configure in **Settings → Automation → Auto-Cleanup**:

| Setting | Default | Description |
|---------|---------|-------------|
| `auto_cleanup_after_extract` | `false` | Enable auto-cleanup after batch extract |
| `auto_cleanup_keep_languages` | *(empty)* | Comma-separated ISO 639-1 codes to keep (e.g. `en,de`) |
| `auto_cleanup_keep_formats` | `any` | `ass`, `srt`, or `any` — when both formats exist for the same language, delete the lower-priority one |

> **Warning:** Cleanup permanently deletes sidecar files that do not match the keep criteria. The subtitle trash does not apply here. Double-check your language list before enabling.

## Backup and Restore Subtitles

Sublarr provides soft-delete via the Subtitle Trash rather than permanent deletion. Additionally, you can create a full config + database backup from **Settings → General → Backup**.

**Creating a backup:**
1. Go to **Settings → General → Backup**
2. Click **Create Backup Now**
3. A ZIP file is downloaded containing the SQLite database and all config entries

**Scheduled backups** run automatically (default: daily, keep 7 copies). Backup files are stored in `/config/backups/`.

**Restoring subtitles from Trash:**
1. Go to **Settings → Automation → Subtitle Trash**
2. Trashed files are listed with their original path and deletion timestamp
3. Click **Restore** to move the file back to its original location
4. Files are auto-purged after `SUBLARR_SUBTITLE_TRASH_RETENTION_DAYS` days (default: 7)

## Troubleshooting

**Series not appearing in Library:**
- Verify Sonarr/Radarr is connected and the API key is correct
- Check path mappings if Sonarr sees different paths than Sublarr
- For standalone mode, confirm the watched folder is accessible inside the container

**Subtitle score is 0 or very low:**
- The provider may have returned a subtitle that does not match the video file
- Try a manual search — results are shown with individual scores before downloading
- Enable more providers for broader coverage (see [Settings → Providers](/user-guide/settings/providers))

**Subtitle exists but shows as Missing:**
- The file may use a non-standard language code — check the filename against the expected pattern `{name}.{lang}.{ext}`
- Trigger a manual rescan from the Series Detail view
