---
title: Settings — Profiles
description: Quality profiles, language profiles, and scoring configuration
published: true
date: 2026-04-13
---

# Settings — Profiles

## Language Profiles

Language Profiles define the subtitle search strategy per series. See the dedicated [Language Profiles](/user-guide/language-profiles) page for full documentation.

## Quality / Score Thresholds

Each profile sets a minimum score (0–100) a subtitle must reach before it is downloaded.

| Score range | Meaning |
|-------------|----------|
| 80–100 | High confidence match — exact episode hash or release name match |
| 60–79 | Good match — title + season/episode match |
| 40–59 | Weak match — title only |
| < 40 | Rejected — too uncertain |

See [Settings → Providers → Scoring](/user-guide/settings/providers#scoring) for how scores are calculated.

## Upgrade Settings

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Upgrade Enabled | `true` | `SUBLARR_UPGRADE_ENABLED` | Allow replacing existing subtitles when a better version is found |

> [!NOTE]
> When upgrade is enabled, Sublarr periodically checks if higher-quality subtitles are available for items that already have subtitles. The minimum score improvement required is configured in the [Upgrade System](/getting-started/environment-variables#upgrade-system) section.

## Hearing Impaired Preferences

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| HI Removal Enabled | `false` | `SUBLARR_HI_REMOVAL_ENABLED` | Strip hearing-impaired annotations (e.g. `[sighs]`, `[music]`) from downloaded subtitles |
| HI Preference | `include` | `SUBLARR_HI_PREFERENCE` | How to handle HI subtitles in search results |

**HI Preference values:**

| Value | Behavior |
|-------|----------|
| `include` | Search results include HI subtitles with no preference (default) |
| `prefer` | Score bonus for HI subtitles |
| `exclude` | Score penalty for HI subtitles |
| `only` | Only download HI subtitles |

## Forced Subtitle Preferences

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Forced Preference | `include` | `SUBLARR_FORCED_PREFERENCE` | How to handle forced (signs/songs) subtitles in search results |

**Forced Preference values:**

| Value | Behavior |
|-------|----------|
| `include` | Search results include forced subtitles with no preference (default) |
| `prefer` | Score bonus for forced subtitles |
| `exclude` | Score penalty for forced subtitles |
| `only` | Only download forced subtitles |

## Credit Filtering

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Credit Threshold (sec) | `90` | `SUBLARR_CREDIT_THRESHOLD_SEC` | Seconds from the end of a subtitle file treated as the credits region |
| OP Window (sec) | `300` | `SUBLARR_OP_WINDOW_SEC` | Seconds from the start and end of a subtitle file considered the OP/ED detection window |

> [!TIP]
> Reduce `op_window_sec` for short episodes (under 15 minutes) to avoid false positive OP/ED detections. The default of 300 seconds (5 minutes) works well for standard 24-minute anime episodes.

## Delay Profiles

Delay profiles add a wait time before searching, allowing better subtitle releases to appear. Useful for newly aired episodes where only machine-translated subtitles are available initially.

Configure per language profile: **Delay (hours)** — default `0`.
