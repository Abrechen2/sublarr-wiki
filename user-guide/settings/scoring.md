---
title: Settings — Scoring
description: Subtitle scoring thresholds, score weights, and cutoff behaviour for download and upgrade decisions
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

# Settings — Scoring

![Settings — Scoring](/img/screen-settings-scoring.png)

The Scoring settings page controls how Sublarr evaluates subtitle candidates, when it considers a result good enough to download, and when it stops trying to improve an existing subtitle.

## How Scoring Works

When Sublarr queries subtitle providers for a media item, each provider returns a list of candidate subtitles with associated metadata. Sublarr evaluates every candidate against the media file and assigns it a **score from 0 to 100**.

The score is built from several signals:

1. **Filename similarity** — How closely the subtitle's release name matches the video file's name. Exact release group matches score highest; generic names score lower.
2. **Video hash match** — Some providers (notably OpenSubtitles) supply a hash of the video file computed at the first and last 64 KB. An exact hash match is a near-certain indicator that the subtitle was made for this exact file.
3. **Language match** — The subtitle must be in the requested language. A subtitle in the wrong language receives a zero language score.
4. **Preferred format** — If the active profile prefers ASS over SRT (the default for anime), ASS subtitles receive a bonus.

After scoring all candidates, Sublarr selects the **highest-scoring result that is at or above the minimum download threshold**. If no candidate clears the threshold, the item stays on the Wanted list and the search is retried on the next scheduled run.

---

## Score Thresholds

| Setting | Default | Description |
|---------|---------|-------------|
| Minimum download score | `60` | A subtitle must score at least this value to be downloaded. Results below this threshold are discarded. Raise this to prefer precision; lower it to accept more results. |
| Minimum upgrade score delta | `50` | When a subtitle already exists, a new candidate must score at least this many points **higher** than the current subtitle to trigger a replacement. |
| Perfect score | `100` *(read-only)* | The theoretical maximum score. Displayed for reference. Reaching 100 requires an exact filename match, a hash match, correct language, and the preferred format — typically only possible for provider uploads made specifically for the exact video file. |

> [!TIP]
> For a general library with mixed content, `60` is a reasonable minimum download score. For anime where subtitle quality matters more (e.g. sign/song translations in ASS), raise the minimum to `70–75` to filter out low-effort SRT-only uploads. You can always lower the threshold temporarily from the Wanted page for specific items that have no high-scoring results.

---

## Score Weights

Score weights control the relative contribution of each signal to the final 0–100 score. The four weights must collectively represent the full score space — their values are proportional, not absolute.

| Setting | Default | Description |
|---------|---------|-------------|
| Filename match weight | `30` | Contribution of release-name similarity. Higher values reward subtitles from matching release groups more strongly. |
| Hash match weight | `40` | Contribution of an exact video file hash match. The highest-weighted signal by default because a hash match is the most reliable quality indicator. |
| Language match weight | `20` | Contribution of correct language. Since all candidates in a search are already pre-filtered by language, this weight primarily breaks ties and rewards subtitles with additional matching language metadata (e.g. country variant). |
| Format bonus | `10` | Bonus applied when the subtitle format matches the profile's preferred format (ASS or SRT). |

> [!NOTE]
> Changing score weights does **not** retroactively re-score existing subtitles in the library. New weights apply only to future searches. If you want to re-evaluate your library with new weights, use **Settings → Cleanup → Run Orphan Cleanup Now** followed by a fresh Wanted scan to re-queue everything.

**Example: Prioritising hash matches for a scene-release library**

If your library consists primarily of exact scene releases (BluRay remuxes, WEB-DL releases with known filenames), you can increase the hash match weight to strongly prefer subtitles made for the exact file:

```
Filename match weight:  20
Hash match weight:      60
Language match weight:  15
Format bonus:           5
```

**Example: Relaxed scoring for a mixed/HEVC library**

For re-encoded libraries where filenames and hashes rarely match provider entries, shift weight towards filename similarity:

```
Filename match weight:  50
Hash match weight:      20
Language match weight:  20
Format bonus:           10
```

---

## Cutoff Behaviour

Cutoff settings determine when Sublarr decides an existing subtitle is "good enough" and stops trying to find better ones.

| Setting | Default | Description |
|---------|---------|-------------|
| Stop searching after perfect score | `true` | If a downloaded subtitle achieves a score of 100, do not add the item to the upgrade queue — treat it as permanently satisfied. |
| Upgrade cutoff score | `80` | If an existing subtitle already has a score at or above this value, skip it during upgrade searches. This prevents unnecessary provider traffic for subtitles that are already high quality. |

> [!WARNING]
> Setting **Upgrade cutoff score** to `100` means Sublarr will keep retrying to upgrade every subtitle that scored below 100 — including subtitles that are already excellent. This generates significant provider traffic over time. Keep the cutoff at `80` or above unless you have a specific reason to allow aggressive upgrading.

**Upgrade cutoff interaction with minimum score delta:**

Both rules apply simultaneously. An upgrade is attempted only when:

1. The existing subtitle scores **below** the upgrade cutoff, **and**
2. The candidate subtitle scores at least **minimum score delta** points higher than the existing subtitle.

If the existing subtitle scores 82 and the cutoff is 80, no upgrade attempt is made regardless of what providers return.
