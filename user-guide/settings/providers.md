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

## Provider Filtering

Limit which providers are active or visible without removing them entirely.

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| `providers_enabled` | *(empty)* | `SUBLARR_PROVIDERS_ENABLED` | Comma-separated list of provider names to enable. Empty = all registered providers enabled. |
| `providers_hidden` | *(empty)* | `SUBLARR_PROVIDERS_HIDDEN` | Comma-separated provider names hidden from the UI grid entirely (not just disabled — truly removed from view). |

> [!NOTE]
> `providers_hidden` is stronger than disabling a provider: the provider does not appear in the grid at all and is never searched, regardless of other settings.

## Additional Provider Credentials

Some providers support or require account credentials beyond an API key.

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| `addic7ed_username` | *(empty)* | `SUBLARR_ADDIC7ED_USERNAME` | Addic7ed username (optional — increases daily download limit). |
| `addic7ed_password` | *(empty)* | `SUBLARR_ADDIC7ED_PASSWORD` | Addic7ed password. |
| `turkcealtyazi_username` | *(empty)* | `SUBLARR_TURKCEALTYAZI_USERNAME` | Turkcealtyazi username (account required). |
| `turkcealtyazi_password` | *(empty)* | `SUBLARR_TURKCEALTYAZI_PASSWORD` | Turkcealtyazi password. |

> [!TIP]
> **Addic7ed** (via the Gestdown scraper) works without credentials but anonymous requests hit a stricter daily download cap. Add an account to raise it.
> **Turkcealtyazi** is a Turkish subtitle provider that requires a registered account to search.

## Search Behavior

Global settings that control how provider searches are executed and cached.

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| `provider_search_timeout` | `30` | `SUBLARR_PROVIDER_SEARCH_TIMEOUT` | Global timeout fallback in seconds used when no dynamic timeout is available. |
| `provider_cache_ttl_minutes` | `5` | `SUBLARR_PROVIDER_CACHE_TTL_MINUTES` | How long provider search results are cached (minutes). |
| `provider_auto_prioritize` | `true` | `SUBLARR_PROVIDER_AUTO_PRIORITIZE` | Auto-prioritize providers based on historical success rate. |
| `provider_rate_limit_enabled` | `true` | `SUBLARR_PROVIDER_RATE_LIMIT_ENABLED` | Enable per-provider rate limiting to avoid bans. |
| `dedup_on_download` | `true` | `SUBLARR_DEDUP_ON_DOWNLOAD` | Skip download if identical content already exists (SHA-256 comparison). |
| `github_token` | *(empty)* | `SUBLARR_GITHUB_TOKEN` | Optional GitHub API token. Raises rate limit from 60 to 5000 requests/hour (used by providers that fetch subtitle lists from GitHub releases). |

## Dynamic Timeouts

Sublarr tracks the p95 response time per provider and adjusts the request timeout automatically.

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| `provider_dynamic_timeout_enabled` | `true` | `SUBLARR_PROVIDER_DYNAMIC_TIMEOUT_ENABLED` | Enable dynamic per-provider timeouts. |
| `provider_dynamic_timeout_min_samples` | `5` | `SUBLARR_PROVIDER_DYNAMIC_TIMEOUT_MIN_SAMPLES` | Minimum samples required before dynamic timeout is applied (falls back to `provider_search_timeout` until then). |
| `provider_dynamic_timeout_multiplier` | `3.0` | `SUBLARR_PROVIDER_DYNAMIC_TIMEOUT_MULTIPLIER` | Multiplier applied to p95 response time. |
| `provider_dynamic_timeout_buffer_secs` | `2.0` | `SUBLARR_PROVIDER_DYNAMIC_TIMEOUT_BUFFER_SECS` | Fixed buffer added after the multiplied p95 value (seconds). |
| `provider_dynamic_timeout_min_secs` | `5` | `SUBLARR_PROVIDER_DYNAMIC_TIMEOUT_MIN_SECS` | Minimum allowed dynamic timeout (seconds). |
| `provider_dynamic_timeout_max_secs` | `30` | `SUBLARR_PROVIDER_DYNAMIC_TIMEOUT_MAX_SECS` | Maximum allowed dynamic timeout (seconds). |

> [!NOTE]
> Effective timeout = `p95 × multiplier + buffer`, clamped to `[min_secs, max_secs]`. Dynamic timeouts are stored in memory and reset on restart.

## Automatic Re-ranking

Re-ranking adjusts per-provider score modifiers automatically based on historical download success rates.

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| `provider_reranking_enabled` | `false` | `SUBLARR_PROVIDER_RERANKING_ENABLED` | Enable automatic re-ranking of provider score modifiers. Disabled by default. |
| `provider_reranking_min_downloads` | `20` | `SUBLARR_PROVIDER_RERANKING_MIN_DOWNLOADS` | Minimum successful downloads required before a computed modifier is applied. |
| `provider_reranking_max_modifier` | `50` | `SUBLARR_PROVIDER_RERANKING_MAX_MODIFIER` | Absolute cap on the auto-computed modifier (±50 max). |

> [!TIP]
> Re-ranking requires enough history to be meaningful. Until `min_downloads` is reached for a provider, the manually set score modifier (Settings → Providers) is used unchanged.

## Release Group Filtering

Bias subtitle results toward preferred release groups or block known low-quality ones.

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| `release_group_prefer` | *(empty)* | `SUBLARR_RELEASE_GROUP_PREFER` | Comma-separated list of preferred release groups (e.g. `SubsPlease,Erai-raws`). Matching results receive a score bonus. |
| `release_group_exclude` | *(empty)* | `SUBLARR_RELEASE_GROUP_EXCLUDE` | Comma-separated list of blocked release groups (e.g. `HorribleSubs`). Matching results are effectively removed. |
| `release_group_prefer_bonus` | `20` | `SUBLARR_RELEASE_GROUP_PREFER_BONUS` | Score bonus applied to preferred release group matches. |

> [!NOTE]
> Excluded release groups receive a score penalty of −999, which removes them from consideration in practice. Preferred groups receive +`release_group_prefer_bonus` (default +20).

## Circuit Breaker

Each provider has an independent circuit breaker that temporarily disables it after repeated failures.

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| `circuit_breaker_failure_threshold` | `5` | `SUBLARR_CIRCUIT_BREAKER_FAILURE_THRESHOLD` | Consecutive failures before the circuit opens and the provider is skipped. |
| `circuit_breaker_cooldown_seconds` | `60` | `SUBLARR_CIRCUIT_BREAKER_COOLDOWN_SECONDS` | Seconds in OPEN state before transitioning to HALF_OPEN for a probe request. |
| `provider_auto_disable_cooldown_minutes` | `30` | `SUBLARR_PROVIDER_AUTO_DISABLE_COOLDOWN_MINUTES` | Minutes before an auto-disabled provider is re-enabled and tried again. |

> [!NOTE]
> States: **CLOSED** (normal) → **OPEN** (skipped, after `failure_threshold` consecutive failures) → **HALF_OPEN** (single probe sent after `cooldown_seconds`) → back to CLOSED on success, or OPEN again on failure.

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