---
title: Settings — Providers
description: Subtitle provider configuration, supported providers, and scoring algorithm
published: true
date: 2026-03-14
---

# Subtitle Provider System

This document describes how Sublarr's subtitle provider system works and how to add new providers.

## Table of Contents

- [Overview](#overview)
- [Existing Providers](#existing-providers)
- [Provider Architecture](#provider-architecture)
- [Scoring System](#scoring-system)
- [Adding a New Provider](#adding-a-new-provider)
- [Provider Best Practices](#provider-best-practices)
- [Testing Providers](#testing-providers)

## Overview

Sublarr uses a modular provider system to search and download subtitles from multiple sources. Each provider is an independent module that implements a standard interface, allowing the system to treat all providers uniformly while supporting their unique APIs and features.

**Key Features**
- Multiple providers searched in parallel
- Priority-based ordering (configurable)
- Automatic fallback on failure
- Result scoring and ranking
- Provider health monitoring
- HTTP session management with retry logic
- Cache system for search results

## Existing Providers

Sublarr includes 11 built-in providers, each with different strengths. The first four are the original providers from v1.0.0-beta; the remaining seven were added in v0.9.0-beta via the plugin system.

### 1. AnimeTosho

**Best for**: Fansub ASS subtitles from release groups

**Characteristics**
- No authentication required
- Specializes in anime subtitles
- High-quality ASS format from fansubs
- Extracts subtitles from full release torrents
- XZ-compressed subtitle archives
- Feed API (JSON format)
- Uses AniDB episode IDs for matching

**Configuration**
```env
# No API key needed
SUBLARR_PROVIDER_PRIORITIES=animetosho,jimaku,opensubtitles,subdl
```

### 2. Jimaku

**Best for**: Anime subtitles with AniList integration

**Configuration**
```env
SUBLARR_JIMAKU_API_KEY=your_api_key_here
```

Get API key at https://jimaku.cc → Account Settings → Generate API token.

### 3. OpenSubtitles

**Best for**: Broad coverage of all media types

**Configuration**
```env
SUBLARR_OPENSUBTITLES_API_KEY=your_api_key_here
SUBLARR_OPENSUBTITLES_USERNAME=your_username
SUBLARR_OPENSUBTITLES_PASSWORD=your_password
```

Get API key at https://www.opensubtitles.com/en/consumers (approval 1-2 days).

### 4. SubDL

**Best for**: Subscene successor with broad coverage

**Configuration**
```env
SUBLARR_SUBDL_API_KEY=your_api_key_here
```

Get API key at https://subdl.com → Settings → API.

### 5. Gestdown

TV show subtitles via Addic7ed proxy. No API key needed.

### 6. Podnapisi

Multilingual subtitles with large database. No API key needed.

### 7. Kitsunekko

Japanese anime subtitles. No API key needed.

### 8. Napisy24

Polish subtitles with file hash matching. No API key needed.

### 9. Titrari

Romanian subtitles. No API key needed.

### 10. LegendasDivx

Portuguese subtitles. Requires login — configure via Settings UI.

### 11. Whisper-Subgen (Deprecated)

Deprecated in v0.9.0-beta. Use Settings → Whisper instead.

## Plugin Development

Since v0.9.0-beta, Sublarr supports loading custom subtitle providers as plugins. Place plugin files in `/config/plugins/`. See [Plugin Development](/development/plugin-development) for full documentation.

---

## Subtitle Scoring

Sublarr scores every subtitle search result before downloading. Higher scores win.

### Base Score by Format

| Format | Base Score |
|---|---|
| ASS | 300 |
| SSA | 280 |
| SRT | 150 |
| VTT | 100 |
| Other | 50 |

ASS is scored highest because it preserves fansub styling (fonts, colors, typesetting) that SRT cannot represent.

### File Size Bonus

| File Size | Bonus |
|---|---|
| > 200 KB | +40 |
| > 100 KB | +30 |
| > 50 KB | +20 |
| ≤ 50 KB | +0 |

### Hash Match Bonus

| Match | Bonus |
|---|---|
| Exact hash match | +100 |
| No hash match | +0 |

### Metadata Bonuses

| Signal | Bonus |
|---|---|
| Series name match | +30 |
| Year match | +20 |
| Season match | +15 |
| Episode match | +15 |
| Release group match | +20 |
| ASS format (language profile pref) | +50 |

### Uploader Trust Bonus

| Uploader Rank | Bonus |
|---|---|
| Gold/Diamond | +20 |
| Silver | +15 |
| Bronze | +10 |
| Unranked | +0 |

### Machine Translation Penalty

| Flag | Penalty |
|---|---|
| `mt` or `ai` tag | -50 |

### Per-Provider Score Modifier

Each provider can have a manual score modifier (-100 to +100) set in Settings. Use positive modifiers for trusted providers, negative for historically poor quality.

### Upgrade Scoring

**Format Rules**

1. **Never downgrade** — ASS → SRT is blocked regardless of score
2. **Always upgrade format** — SRT → ASS always proceeds if `upgrade_prefer_ass = true`
3. **Same format** — only upgrade if score delta exceeds `upgrade_min_score_delta` (default: 50)

**Recency Protection:** Subtitles downloaded within `upgrade_window_days` (default: 7 days) require **2x the minimum score delta**.

### Fansub Group Rules (v0.24.3+)

| Rule | Effect |
|------|--------|
| **Preferred group** match | +`bonus` points (default: +20) |
| **Excluded group** match | −999 points (effectively excluded) |

Configure via **Series Detail → Fansub Preferences**.

### Final Score Formula

```
score = format_base
      + size_bonus
      + hash_match_bonus
      + series_bonus + year_bonus + season_bonus + episode_bonus
      + release_group_bonus
      + format_preference_bonus
      + uploader_trust_bonus
      - machine_translation_penalty
      + provider_modifier
      + fansub_preference_bonus
```

The highest-scoring result above the minimum threshold is downloaded.
