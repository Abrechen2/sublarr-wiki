---
title: Settings — Language Profiles
description: Define which subtitle languages to target, their priority order, and quality thresholds per series
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

# Settings — Language Profiles

![Settings — Language Profiles](/img/screen-settings-languages.png)

Language Profiles define which subtitle languages Sublarr searches for, in what order of preference, and at what quality threshold a subtitle is considered acceptable or upgrade-worthy. Every series in your library is assigned exactly one profile — either explicitly, or via the Global Default.

> **Tip:** Create a separate profile for anime series with a Japanese original — set Japanese as a secondary (reference) language while German or English is the primary translation target. This lets Sublarr download the Japanese source subtitle alongside the translated one, which is useful for quality review and fine-tuning the AI Glossary.

---

## Global Default Profile

The Global Default is the profile applied to any series that has no specific profile assigned. It acts as a fallback so that newly added series are immediately covered without manual configuration.

| Setting | Description |
|---------|-------------|
| **Global Default Profile** | Dropdown — select any existing profile. Newly imported series inherit this profile automatically. |

To change the global default, select a profile from the dropdown and click **Save**. Existing series with explicit profile assignments are not affected — only unassigned series inherit the new default.

---

## Creating a Profile

Click **Add Profile** to open the profile editor.

| Field | Default | Description |
|-------|---------|-------------|
| **Name** | — | Descriptive name for the profile (e.g. `German Primary`, `Dual Sub — Anime`) |
| **Languages** | — | Ordered list of target languages. Add languages via the search dropdown and drag to reorder. |
| **Upgrade cutoff score** | `80` | If the current subtitle for a language scores below this threshold, Sublarr will continue searching for a better one. Once a subtitle reaches this score, no further upgrade attempts are made. |
| **Forced subtitle handling** | `Disabled` | Controls how forced subtitles (signs/songs-only tracks) are treated: `Disabled` (ignore), `Separate` (download as a separate `.forced` sidecar), `Auto` (merge into the main subtitle if only forced lines exist) |

After saving, the profile appears in the profile list and can be assigned to series.

---

## Language Priority

When a profile lists multiple languages, Sublarr searches for them **in the order they appear in the profile**. The priority logic works as follows:

1. Sublarr searches all configured providers for the highest-scoring subtitle available for **Language 1**.
2. If a subtitle is found with a score at or above the **minimum score** threshold, it is downloaded and Language 1 is considered satisfied.
3. If no acceptable subtitle is found for Language 1, Sublarr proceeds to Language 2, and so on.
4. All languages in the profile are eventually evaluated — finding a subtitle for Language 1 does not prevent searching for Language 2. Priority only affects tie-breaking and the order in which searches run.

This means a profile with German (priority 1) and Japanese (priority 2) will attempt to obtain a subtitle for both languages — German first, then Japanese.

---

## Per-Language Settings

Each language entry within a profile has its own settings accessible by clicking the language row.

| Setting | Default | Description |
|---------|---------|-------------|
| **Minimum score** | `60` | Subtitles scoring below this threshold are rejected. The provider result is discarded and the next provider or language is tried. |
| **Format preference** | `Any` | Preferred subtitle format: `Any` (accept whatever is found), `ASS preferred` (ASS first, SRT fallback), `SRT preferred` (SRT first, ASS fallback) |

**Guidance on minimum score:**

- `60` is the recommended floor — below this, sync errors and translation quality issues become common
- `80` is a high-quality threshold — appropriate for primary languages where quality matters most
- `40–59` may be acceptable for reference languages (e.g. original Japanese) where machine-readable content is the goal rather than watch quality

**Guidance on format preference:**

- ASS is recommended for anime — it supports styled text, signs, and karaoke timing that SRT cannot represent
- SRT is simpler and more compatible with basic media players and Jellyfin mobile clients
- Setting `ASS preferred` does not prevent SRT from being downloaded if no ASS subtitle is available above the minimum score

---

## Assigning Profiles

Profiles can be assigned at two levels:

**Per-series assignment:**
1. Go to **Library → Series**
2. Click the series you want to configure
3. In the series detail view, find the **Profile** dropdown
4. Select the desired profile and click **Save**

**Global default (all unassigned series):**
See the [Global Default Profile](#global-default-profile) section above.

**Bulk assignment:**
In **Library → Series**, use the multi-select checkboxes to select multiple series, then choose **Set Profile** from the bulk actions toolbar.

Profile changes take effect immediately — the next Wanted scan will use the updated language targeting for all affected series.
