---
title: Settings — Automation
description: Scheduled tasks, upgrade rules, auto-cleanup, and subtitle trash management
published: true
date: 2026-03-16
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
| Anime series only | `true` | When enabled, only series tagged as anime in Sonarr are included in the Wanted scan. Disable to scan all series regardless of genre. |
| Anime movies only | `false` | Filter Radarr movies by anime tag. When enabled, only movies tagged as anime are added to the Wanted list. |
| Auto-extract embedded subs | `false` | Automatically extract embedded subtitle streams from MKV files during the Wanted scan before querying any provider. |
| Max search attempts | `3` | Maximum number of provider search attempts per item before it is marked as failed and removed from active rotation. |
| Use embedded subtitles | `true` | Check embedded subtitle streams inside MKV files before querying providers. If a matching stream is found, Sublarr uses it directly without a network search. |
| Scan yield (ms) | `0` | Milliseconds to sleep between processing each series or movie during a scan. Set a small value (e.g. `50`) on low-powered hardware to yield CPU time back to API threads. `0` disables any yield. |

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
| Search on startup | `false` | Run a provider search pass immediately when the container starts, in addition to the scheduled interval. |
| Max items per run | `0` | Maximum number of Wanted items processed in a single scheduled search run. `0` means unlimited — all pending items are searched each run. Set a cap (e.g. `50`) to spread load across multiple runs. |
| Upgrade scan interval (hours) | `24` | How often to scan for subtitles that are eligible for a quality upgrade. `0` disables the upgrade scanner entirely. |

> [!NOTE]
> Each provider has its own rate limit. AnimeTosho and Kitsunekko are scraper-based and are more sensitive to bursts than API-based providers like OpenSubtitles or Jimaku. If you see providers returning HTTP 429 errors in the logs, lower **Max concurrent searches** to `1` or `2`.

---

## Adaptive Backoff

When a provider search attempt fails for an item, Sublarr does not retry immediately. Instead it waits progressively longer before each successive attempt — starting at **Backoff base** and doubling with each failure, up to **Backoff cap**. This prevents hammering providers on persistent failures (e.g. a subtitle that simply does not exist yet).

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Adaptive backoff enabled | `true` | `SUBLARR_WANTED_ADAPTIVE_BACKOFF_ENABLED` | Enable exponential backoff for failed provider searches. When disabled, failed items are retried at the normal search interval. |
| Backoff base (hours) | `1.0` | `SUBLARR_WANTED_BACKOFF_BASE_HOURS` | Initial wait after the first failure. Each subsequent failure doubles the wait. |
| Backoff cap (hours) | `168` | `SUBLARR_WANTED_BACKOFF_CAP_HOURS` | Maximum wait between retries, regardless of how many consecutive failures have occurred. Default is 7 days (`168` hours). |

> [!TIP]
> With the defaults, a first failure waits 1 hour before retry, a second failure waits 2 hours, a third waits 4 hours, and so on — capping at 168 hours (7 days). Once a subtitle is eventually found, the backoff counter resets.

---

## Early Exit

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Skip SRT if no ASS found | `true` | `SUBLARR_WANTED_SKIP_SRT_ON_NO_ASS` | When enabled, if provider search steps 1 and 2 return no ASS subtitle, Sublarr skips the remaining SRT-only steps for that item. |

> [!TIP]
> This is the recommended setting for anime-first setups. Anime subtitles in SRT format lack styling, fonts, and positioned signs — an ASS subtitle from a fansub group is almost always preferable. Enabling early exit avoids downloading a plain SRT just to fill the Wanted slot.

---

## Webhook Automation

Sublarr can receive webhook events from Sonarr and Radarr (and Jellyfin for playback-triggered translation) and react to them automatically without waiting for the next scheduled run.

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Webhook delay (minutes) | `5` | `SUBLARR_WEBHOOK_DELAY_MINUTES` | How many minutes Sublarr waits after receiving a webhook before processing the event. The delay allows the media file to be fully written to disk before scanning begins. |
| Auto scan on webhook | `true` | `SUBLARR_WEBHOOK_AUTO_SCAN` | Automatically run a Wanted scan for the affected item after a Sonarr/Radarr webhook event. |
| Auto search on webhook | `true` | `SUBLARR_WEBHOOK_AUTO_SEARCH` | Automatically query providers for the affected item immediately after the webhook scan. When combined with **Auto scan on webhook**, a single import event in Sonarr/Radarr triggers a full scan-then-search cycle. |
| Translate on Jellyfin play | `false` | `SUBLARR_JELLYFIN_PLAY_TRANSLATE_ENABLED` | When a Jellyfin playback-start event is received, automatically translate the active subtitle track if no translated version already exists. |

> [!NOTE]
> The HTTP streaming endpoint toggle lives in the **Advanced** section below. The **Webhook delay** exists because media servers sometimes fire the import webhook before the transcoder has fully flushed the output file — a 5-minute delay is conservative and works for most setups.

---

## Glossary

The **AI Glossary Builder** analyses subtitle files for recurring proper nouns — character names, place names, show-specific terminology — and surfaces them for review. Approved terms are automatically injected into the translation prompt so the AI uses consistent naming across all episodes.

See [AI Glossary](/user-guide/ai-glossary) for a full walkthrough of the review and approval workflow.

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Glossary enabled | `true` | `SUBLARR_GLOSSARY_ENABLED` | Enable the AI Glossary Builder. When disabled, no terms are extracted or injected. |
| Max terms per series | `100` | `SUBLARR_GLOSSARY_MAX_TERMS` | Maximum number of glossary terms stored per series. When the cap is reached, the oldest (least recently used) terms are evicted to make room for new ones. |

> [!TIP]
> For long-running shows with large casts, the default cap of `100` terms is usually sufficient. Increase it only if you notice important terms being evicted. Reducing it below `50` is not recommended — the AI benefits from seeing full cast names in the prompt.

---

## Video Sync

When **Auto sync** is enabled, Sublarr runs the configured sync engine on every subtitle it downloads, automatically aligning timing to the audio track of the video file. This corrects for subtitle rips sourced from a different encode than your local copy.

See [Video Sync](/user-guide/video-sync) for details on sync accuracy, supported formats, and manual sync controls.

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Auto sync after download | `false` | `SUBLARR_AUTO_SYNC_AFTER_DOWNLOAD` | Automatically run the sync engine on every downloaded subtitle. |
| Sync engine | `ffsubsync` | `SUBLARR_AUTO_SYNC_ENGINE` | Which sync engine to use. Options: `ffsubsync` (audio fingerprint-based, default) or `alass` (cross-subtitle alignment, faster on low-end hardware). |

> [!NOTE]
> Sync adds CPU time per subtitle. On low-powered hardware (e.g. a Proxmox LXC with 2 vCPUs), consider enabling sync only for the download pipeline rather than batch runs by keeping **Auto sync** off and triggering sync manually from the library view.

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

---

## Advanced

| Setting | Default | Description |
|---------|---------|-------------|
| **Streaming Enabled** | `true` | Enable the HTTP video streaming endpoint (`GET /api/v1/media/stream`). Serves video files with HTTP 206 range-request support for the built-in Web Player. Disable to block direct media access via the API. Configurable via `streaming_enabled`. |
| **Auto NFO Export** | `false` | Automatically write an XML `.nfo` sidecar alongside every downloaded or translated subtitle. Contains provider, language, score, translation backend, BLEU score, and Sublarr version. Expert feature. |
