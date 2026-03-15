---
title: Settings — Subtitle Tools
description: Post-processing tools — HI removal, credit filtering, sync, common fixes, and subtitle preview
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

# Settings — Subtitle Tools

![Settings — Subtitle Tools](/img/screen-settings-subtitle-tools.png)

Subtitle Tools is a collection of post-processing steps that Sublarr can apply automatically after a subtitle is downloaded or translated. Each tool is independent and can be enabled or disabled without affecting the others.

## HI Removal

Hearing-impaired (HI) lines are written annotations for viewers who cannot hear audio cues — sound effects like `[DOOR SLAMS]`, speaker labels like `JOHN:`, and parenthetical descriptions like `(whispering)`. While essential for accessibility, many viewers prefer clean subtitles without them.

| Setting | Default | Description |
|---------|---------|-------------|
| Enable HI Removal | `false` | Strip hearing-impaired annotations from downloaded subtitles |
| Aggressive mode | `false` | Remove additional HI patterns such as all-caps lines, bracketed sound effects mid-sentence, and inline stage directions. May occasionally strip legitimate dialogue. |
| Backup before removal | `true` | Save the original subtitle file alongside the cleaned version before any lines are removed. The backup is named `<filename>.hi-backup.srt` (or `.ass`). |

> [!TIP]
> Start with aggressive mode **off**. Enable it only if the standard remover leaves noticeable HI content — for example, on subtitles produced by automated speech recognition pipelines that embed sound descriptions inline.

Standard mode removes:
- Lines entirely enclosed in brackets or parentheses: `[Crowd cheering]`, `(sighs deeply)`
- Speaker labels at the start of a line: `NARRATOR:`, `MARY:`
- Music notation lines: `♪ La la la ♪`

Aggressive mode additionally removes:
- All-caps lines shorter than 60 characters that do not end with punctuation
- Bracketed or parenthetical phrases embedded within dialogue lines
- Lines that consist only of a sound description with no spoken content

---

## Credit Filtering

Credits filtering removes opening and ending theme song subtitles, karaoke lyrics, and on-screen credits that appear in a configurable time window at the start or end of the episode. This is most useful for anime, where OP/ED subtitles are often embedded in the main subtitle track.

| Setting | Default | Description |
|---------|---------|-------------|
| Enable Credit Filtering | `false` | Remove subtitle lines that fall within the opening or ending time windows |
| Start window (seconds) | `120` | Lines with a start time within the first N seconds of the episode are considered opening credits |
| End window (seconds) | `60` | Lines with a start time within the last N seconds of the episode are considered ending credits |
| Custom patterns | *(empty)* | Newline-separated regex patterns. Any subtitle line matching one of these patterns is removed regardless of timing. Useful for watermarks or translator credit lines. |

> [!NOTE]
> Credit Filtering uses the **subtitle timestamps**, not the actual video duration. For accurate end-window calculation, Sublarr reads the total duration from the video file via FFprobe. If the video file is not accessible at processing time, the end window filter is skipped and only the start window is applied.

**Custom pattern examples:**

```
^Translated by.*
^\[TL Note\].*
^Fansub by.*
```

Patterns are case-insensitive and matched against the plain-text content of each cue (HTML/ASS tags are stripped before matching).

---

## Sync

Subtitle sync corrects timing offsets between a subtitle file and its corresponding video. This is common when a subtitle from one release is applied to a different encode of the same episode.

| Setting | Default | Description |
|---------|---------|-------------|
| Sync backend | `ffsubsync` | The tool used for synchronisation. Options: `ffsubsync` (audio-based, uses the video's audio track), `alass` (sample-based, uses a reference subtitle for alignment) |
| Auto-sync on download | `false` | Automatically run sync after every subtitle download. Adds processing time per download — recommended only if your library consistently uses re-encoded files. |

**ffsubsync** aligns subtitles by comparing the audio fingerprint of the video to the speech patterns in the subtitle. It works without a reference subtitle and handles both constant and variable offsets.

**alass** aligns two subtitle files against each other. It is faster than ffsubsync but requires a reference subtitle (usually the subtitle embedded in the video file or a previously downloaded one). Sublarr supplies the embedded track automatically when available.

> [!TIP]
> For anime with dual audio (Japanese + English dub), set ffsubsync to use the Japanese audio track to avoid sync errors caused by dub timing differences. This is configurable per-profile under **Profiles → Sync Track**.

---

## Common Fixes

These lightweight text-level fixes are applied automatically on every subtitle, regardless of other tool settings.

| Setting | Default | Description |
|---------|---------|-------------|
| Fix overlapping cues | `true` | When two subtitle cues overlap in time, shrink the earlier cue's end time to match the later cue's start time. Prevents double-stacking of lines in players that do not merge overlaps. |
| Remove duplicate lines | `true` | Remove consecutive cues with identical text (a common artefact of ASS-to-SRT conversion tools). |
| Fix encoding | `true` | Detect and convert Latin-1 (ISO 8859-1) encoded subtitle files to UTF-8. Affects `.srt` files only — `.ass` files are always assumed to be UTF-8. |

These fixes run in a single pass after download and after every other post-processing step, ensuring the output is always clean regardless of the order operations are applied.

> [!NOTE]
> Encoding detection uses the `chardet` library. Detection accuracy on short or heavily-ASCII files can be low. If you see garbled characters in converted subtitles, disable **Fix encoding** and handle encoding conversion manually with `iconv`.

---

## Preview Tool

The **Subtitle Preview** panel (accessible from the Subtitle Tools settings page and from individual subtitle entries in the Library) lets you paste raw subtitle content and test all enabled tools against it without writing anything to disk.

**Workflow:**

1. Paste or upload a `.srt` or `.ass` file into the preview input box.
2. Choose which tools to apply (HI Removal, Credit Filtering, Common Fixes).
3. Click **Preview** — the processed output appears in the right panel with a diff highlight showing removed and changed lines.
4. If the result looks correct, click **Apply to File** to process the actual file on disk, or close the panel to discard.

The preview tool uses the same processing code as the automated pipeline — it is not a simplified simulation. If the preview produces the expected output, the automated pipeline will produce identical results.
