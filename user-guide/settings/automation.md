---
title: Settings — Automation
description: Scheduled tasks, upgrade rules, auto-cleanup, and subtitle trash management
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

# Settings — Automation

![Settings — Automation](/img/screen-settings-automation.png)

Automation controls when Sublarr looks for missing subtitles, how aggressively it upgrades existing ones, and how it manages old or unwanted files over time.

## Wanted Scan

The Wanted list is the queue of episodes and movies that are missing a subtitle in one or more configured languages. A **Wanted Scan** walks your media library and re-builds this list by comparing what is on disk against what the database expects.

| Setting | Default | Description |
|---------|---------|-------------|
| Scan interval (hours) | `0` | How often to run a full Wanted scan. `0` disables the timer — scans happen only via webhook events from Sonarr/Radarr or manually. |
| Scan on startup | `false` | Run a full Wanted scan immediately when the container starts. Useful after a long downtime to catch missed events. |

> [!TIP]
> If Sonarr and Radarr webhooks are configured, a periodic scan is largely redundant — events fire as soon as new media is imported. Enable **Scan on startup** only if your setup has occasional webhook delivery failures.

When the scan runs, Sublarr does **not** immediately search for subtitles — it only updates the Wanted list. Provider searches are controlled separately by the **Provider Search** schedule below.

---

## Provider Search

Provider Search is the background task that takes items from the Wanted list and queries subtitle providers for matches.

| Setting | Default | Description |
|---------|---------|-------------|
| Search interval (hours) | `24` | How often to process the Wanted queue. Sublarr searches the oldest items first. |
| Max concurrent searches | `3` | Maximum number of simultaneous provider searches. Higher values speed up large backlogs but increase the risk of rate-limiting by providers. Recommended range: 1–5. |

> [!NOTE]
> Each provider has its own rate limit. AnimeTosho and Kitsunekko are scraper-based and are more sensitive to bursts than API-based providers like OpenSubtitles or Jimaku. If you see providers returning HTTP 429 errors in the logs, lower **Max concurrent searches** to `1` or `2`.

---

## Upgrade

Upgrade rules control whether Sublarr replaces a subtitle that already exists with a higher-quality one found during a later search.

| Setting | Default | Description |
|---------|---------|-------------|
| Upgrade enabled | `true` | Allow replacing existing subtitles with better-scoring results |
| Minimum score delta | `50` | A candidate subtitle must score at least this many points higher than the current subtitle to trigger an upgrade. Prevents marginal replacements. |
| Upgrade on re-download | `false` | When a media file is re-imported (e.g. a higher-quality encode replaces the original), treat the existing subtitle as a candidate for upgrade rather than keeping it. |

Upgrade respects format promotion rules — an ASS subtitle will never be replaced with an SRT subtitle regardless of score. See [Providers → Upgrade Scoring Rules](/user-guide/settings/providers#upgrade-scoring-rules) for the full logic.

> [!WARNING]
> Setting the minimum score delta to `0` allows any subtitle to be replaced by any other subtitle of equal or higher score. This can cause frequent replacements and unnecessary provider traffic. Keep the delta at `50` or above for stable libraries.

---

## Auto-Cleanup

After a provider search or manual extraction, Sublarr may download multiple subtitle variants (different languages, different formats) before selecting the best match. Auto-cleanup removes the unwanted intermediary files automatically.

| Setting | Default | Description |
|---------|---------|-------------|
| Auto-cleanup after extract | `false` | Delete subtitle files that do not match the configured keep criteria after a batch extract or search run |
| Keep languages | *(empty)* | Comma-separated list of language codes to keep (e.g. `en,de,ja`). Empty means keep all languages. |
| Keep formats | `any` | Which subtitle formats to retain. Options: `any` (keep everything), `ass` (keep only ASS files, delete SRT), `srt` (keep only SRT files, delete ASS). |

> [!WARNING]
> Auto-cleanup deletes files from disk. Deleted subtitle files are moved to the **Subtitle Trash** (see below) before deletion and can be recovered during the trash retention window. After that window, recovery is not possible.

**Example:** To keep only English ASS subtitles and discard everything else after a batch search:

```
Keep languages: en
Keep formats:   ass
```

---

## Subtitle Trash

Instead of permanently deleting subtitle files immediately, Sublarr moves them to a trash directory (`/config/trash/` by default) and holds them for a configurable number of days before final purge. This gives a recovery window for accidental deletions.

| Setting | Default | Description |
|---------|---------|-------------|
| Retention days | `7` | How many days trashed subtitle files are kept before permanent deletion |
| Auto-purge on startup | `false` | Delete expired trash entries every time the container starts, in addition to the scheduled purge. |

### Subtitle Trash UI

The **Subtitle Trash** panel (accessible from **Settings → Automation → Subtitle Trash**) shows all files currently in the trash:

| Column | Description |
|--------|-------------|
| Filename | Original file name |
| Trashed | Date and time the file was moved to trash |
| Expires | Date the file will be permanently purged |
| Reason | Why the file was trashed (e.g. "auto-cleanup", "replaced by upgrade", "manual delete") |
| Size | File size |

**Actions available per file:**

- **Restore** — Move the file back to its original path. If the destination already exists (because a newer subtitle was written there), you will be prompted to confirm overwrite.
- **Delete permanently** — Remove the file immediately without waiting for the retention window to expire.

At the top of the panel, a **Purge All Expired** button removes all files that have passed their retention date in a single operation.

> [!NOTE]
> The trash only holds **subtitle files** (`.srt`, `.ass`, `.ssa`, `.vtt`). Database records for deleted subtitles are also removed from the library when the file is purged. Restoring a file from trash does not restore the database record — Sublarr will re-detect the file on the next library scan.
