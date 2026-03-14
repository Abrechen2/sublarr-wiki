---
title: Settings — Providers
description: Subtitle provider configuration, supported providers, and scoring algorithm
published: true
date: 2026-03-14T19:29:01.803Z
tags: settings
editor: markdown
dateCreated: 2026-03-14T18:05:01.100Z
---

<div class="settings-tabs">
<a class="settings-tab " href="/user-guide/settings/general"><span class="mdi mdi-cog-outline"></span> General</a>
<a class="settings-tab " href="/user-guide/settings/media-management"><span class="mdi mdi-folder-cog-outline"></span> Media Management</a>
<a class="settings-tab " href="/user-guide/settings/profiles"><span class="mdi mdi-translate"></span> Profiles</a>
<a class="settings-tab active" href="/user-guide/settings/providers"><span class="mdi mdi-cloud-search-outline"></span> Providers</a>
<a class="settings-tab " href="/user-guide/settings/translation"><span class="mdi mdi-brain"></span> Translation</a>
<a class="settings-tab " href="/user-guide/settings/integrations"><span class="mdi mdi-connection"></span> Integrations</a>
</div>

# Settings — Providers

Sublarr includes 11 built-in subtitle providers, all searched in parallel with automatic fallback.

## Provider Overview

| Provider | Auth | Best For | Format |
|----------|------|---------|--------|
| **AnimeTosho** | None | Anime fansub ASS | ASS |
| **Jimaku** | API key | Anime with AniList | ASS/SRT |
| **OpenSubtitles** | API key | Broad coverage, all types | ASS/SRT |
| **SubDL** | API key | Subscene successor | SRT |
| **Gestdown** | None | TV shows (Addic7ed) | SRT |
| **Podnapisi** | None | Multilingual | SRT |
| **Kitsunekko** | None | Japanese anime | ASS/SRT |
| **Napisy24** | None | Polish | SRT |
| **Titrari** | None | Romanian | SRT |
| **LegendasDivx** | Login | Portuguese | SRT |

## API Keys

```env
SUBLARR_JIMAKU_API_KEY=your_key
SUBLARR_OPENSUBTITLES_API_KEY=your_key
SUBLARR_OPENSUBTITLES_USERNAME=your_user
SUBLARR_OPENSUBTITLES_PASSWORD=your_pass
SUBLARR_SUBDL_API_KEY=your_key
```

Obtain keys from provider websites. AnimeTosho, Gestdown, Podnapisi, Kitsunekko, Napisy24, and Titrari require no authentication.

## Provider Priorities

Control search order in **Settings → Providers → Priority**. Higher priority providers are searched first. Recommended for anime:

```env
SUBLARR_PROVIDER_PRIORITIES=animetosho,jimaku,opensubtitles,subdl
```

## Subtitle Scoring

Every search result is scored before downloading. Highest score wins.

### Score Components

| Signal | Value |
|--------|-------|
| ASS format | +300 base |
| SRT format | +150 base |
| Exact hash match | +100 |
| File > 200 KB | +40 |
| Series/episode match | +15–30 each |
| Release group match | +20 |
| ASS preferred (profile) | +50 |
| Trusted uploader (Gold) | +20 |
| Machine-translated | −50 |
| Excluded fansub group | −999 |

### Upgrade Scoring Rules

1. **Never downgrade** — ASS → SRT blocked regardless of score
2. **Always upgrade format** — SRT → ASS proceeds if `upgrade_prefer_ass=true`
3. **Same format** — upgrade only if score delta ≥ `upgrade_min_score_delta` (default: 50)
4. **Recency protection** — recent subs (within `upgrade_window_days`) require 2× delta

### Per-Provider Score Modifier

Each provider has a manual score modifier (−100 to +100) in Settings. Use positive modifiers for trusted providers, negative for historically poor-quality ones.

## Custom Plugins

Since v0.9.0-beta, Sublarr supports loading custom providers as plugins. Place plugin Python files in `/config/plugins/`.

Supported config field types: `text`, `password`, `boolean`, `number`, `select`

> **Note:** Plugins run with the same permissions as Sublarr. Only install plugins you trust.