---
title: Subtitle Provider System
description: How Sublarr's provider system works, all built-in providers, and how to add new ones
published: true
date: 2026-03-14
---

# Subtitle Provider System

Sublarr uses a modular provider system to search and download subtitles from multiple sources. Each provider is an independent module that implements a standard interface, allowing the system to treat all providers uniformly while supporting their unique APIs and features.

**Key features:**
- Multiple providers searched in parallel
- Priority-based ordering (configurable)
- Automatic fallback on failure
- Result scoring and ranking
- Provider health monitoring
- HTTP session management with retry logic
- Cache system for search results

---

## Built-in Providers

Sublarr includes 11 built-in providers. The first four are the original providers; the remaining seven were added via the plugin system.

### 1. AnimeTosho

**Best for:** Fansub ASS subtitles from release groups

| | |
|---|---|
| Authentication | None required |
| Formats | ASS (from archives: XZ, ZIP, RAR) |
| Rate limit | None (public feed) |
| API | `https://feed.animetosho.org/json` |

Uses AniDB episode IDs for matching. Extracts subtitles from full release torrents.

**Strengths:** Excellent ASS quality from fansub groups, no rate limits, good coverage for popular anime.
**Limitations:** Anime-focused only, requires parsing release names, may have delays for new episodes.

```env
# No API key needed
SUBLARR_PROVIDER_PRIORITIES=animetosho,jimaku,opensubtitles,subdl
```

---

### 2. Jimaku

**Best for:** Anime subtitles with AniList integration

| | |
|---|---|
| Authentication | API key required |
| Formats | ASS/SRT (ZIP, RAR archives) |
| Rate limit | Moderate |
| API | `https://jimaku.cc/api/` |

Uses AniList IDs for series matching. Good Japanese subtitle coverage.

**Strengths:** Excellent anime coverage, AniList integration improves matching, active community.
**Limitations:** Anime only, API key required.

```env
SUBLARR_JIMAKU_API_KEY=your_api_key_here
```

Get your key at [jimaku.cc](https://jimaku.cc) → Account Settings → Generate API token.

---

### 3. OpenSubtitles

**Best for:** Broad coverage of all media types

| | |
|---|---|
| Authentication | API key + optional login |
| Formats | ASS, SRT |
| Rate limit | 5 req/s; 200/day (anon), 1000/day (logged in) |
| API | `https://api.opensubtitles.com/api/v1/` |

Largest subtitle database. Uses file hash, IMDB ID, and episode matching.

**Strengths:** Massive database, excellent file hash matching, all media types, multiple languages.
**Limitations:** API key approval required (1–2 days), rate limits, quality inconsistent.

```env
SUBLARR_OPENSUBTITLES_API_KEY=your_api_key_here
SUBLARR_OPENSUBTITLES_USERNAME=your_username
SUBLARR_OPENSUBTITLES_PASSWORD=your_password
```

Get your key at [opensubtitles.com/en/consumers](https://www.opensubtitles.com/en/consumers).

---

### 4. SubDL

**Best for:** Subscene successor with broad coverage

| | |
|---|---|
| Authentication | API key required |
| Formats | SRT (ZIP archives) |
| Rate limit | Moderate; 2000 downloads/day |
| API | `https://api.subdl.com/api/v1/` |

Subscene spiritual successor (launched May 2024). Uses IMDB ID, TMDB ID, title matching.

**Strengths:** Curated quality, good movies/TV coverage, high daily limit.
**Limitations:** Relatively new service, API key required, download quota enforced.

```env
SUBLARR_SUBDL_API_KEY=your_api_key_here
```

Get your key at [subdl.com](https://subdl.com) → Settings → API section.

---

### 5. Gestdown

**Best for:** TV show subtitles via Addic7ed proxy

| | |
|---|---|
| Authentication | None |
| Formats | SRT |
| Rate limit | Moderate |

REST API proxy for Addic7ed content. Language mapping fetched from API with hardcoded fallback.

**Strengths:** No auth or API key required, good TV show coverage, clean REST API (no scraping).
**Limitations:** TV shows only, SRT only, depends on Gestdown service availability.

---

### 6. Podnapisi

**Best for:** Multilingual subtitles with large database

| | |
|---|---|
| Authentication | None |
| Formats | SRT |
| Rate limit | None |

XML API with lxml parsing (graceful fallback to stdlib `xml.etree`). Large multilingual database.

**Strengths:** No authentication, excellent multilingual coverage, no rate limits.
**Limitations:** SRT only, XML API can be slow for complex queries.

---

### 7. Kitsunekko

**Best for:** Japanese anime subtitles

| | |
|---|---|
| Authentication | None |
| Formats | ASS, SRT |
| Rate limit | Polite delays between requests |

HTML scraping with conditional BeautifulSoup import. Degrades gracefully if `bs4` not installed.

**Strengths:** Excellent Japanese subtitle coverage, no auth, good ASS availability.
**Limitations:** Japanese-focused only, HTML scraping (may break with site changes), optional BeautifulSoup dependency.

---

### 8. Napisy24

**Best for:** Polish subtitles with file hash matching

| | |
|---|---|
| Authentication | None |
| Formats | SRT |
| Matching | MD5 hash of first 10MB (Bazarr-compatible) |

Polish language focus. Good quality Polish subtitles.

**Strengths:** Excellent Polish coverage, file hash matching for precise results.
**Limitations:** Polish only, requires file access for hash computation, SRT only.

---

### 9. Titrari

**Best for:** Romanian subtitles

| | |
|---|---|
| Authentication | None (browser-like UA headers) |
| Formats | SRT |

Polite scraping with browser-like headers (User-Agent, Accept-Language).

**Strengths:** Good Romanian subtitle coverage, no auth.
**Limitations:** Romanian only, HTML scraping approach.

---

### 10. LegendasDivx

**Best for:** Portuguese subtitles

| | |
|---|---|
| Authentication | Username + password (session cookies) |
| Formats | SRT |
| Rate limit | 145 downloads/day |

Session-based auth with lazy login. Auto re-authentication on session expiry (302 redirect detection). Daily download limit tracking with safety margin.

**Strengths:** Good Portuguese coverage, automatic session management, daily limit tracking.
**Limitations:** Portuguese focus, login required, 145/day limit, HTML scraping.

Configure credentials via **Settings UI** — stored in `config_entries` DB, not in `.env`.

---

### 11. Whisper-Subgen (Deprecated)

This provider is deprecated. Whisper functionality has been moved to the dedicated Whisper Speech-to-Text system (Settings → Whisper). The provider class remains registered but all methods are no-ops.

See [Translation & LLM](/user-guide/translation-llm) for Whisper configuration.

---

## Provider Architecture

### Core Classes

**`SubtitleProvider`** (Abstract Base Class, `backend/providers/base.py`)

Defines the interface all providers must implement:

```python
class SubtitleProvider(ABC):
    @abstractmethod
    def search(self, query: VideoQuery) -> List[SubtitleResult]: ...

    @abstractmethod
    def download(self, result: SubtitleResult, dest: Path) -> Path: ...

    @abstractmethod
    def health_check(self) -> bool: ...
```

**`ProviderManager`** (Singleton, `backend/providers/__init__.py`)

Orchestrates all providers — searches all in parallel, sorts by score, returns ranked results.

**`RetryingSession`** (`backend/providers/http_session.py`)

HTTP session with automatic retry (3x, exponential backoff), rate-limit awareness, and `Retry-After` header support.

---

## Adding a New Built-in Provider

### Step 1: Create the module

Create `backend/providers/newprovider.py` implementing `SubtitleProvider`:

```python
from .base import SubtitleProvider, VideoQuery, SubtitleResult

class NewProviderProvider(SubtitleProvider):
    def search(self, query: VideoQuery) -> List[SubtitleResult]:
        if not self.api_key:
            return []  # always return list, never raise
        try:
            # ... API call ...
            return results
        except Exception as e:
            logger.error(f"NewProvider search error: {e}")
            return []

    def download(self, result: SubtitleResult, dest: Path) -> Path:
        response = self.session.get(result.download_url, timeout=30)
        response.raise_for_status()
        dest.write_bytes(response.content)
        return dest

    def health_check(self) -> bool:
        try:
            return self.session.get(f"{self.base_url}/health", timeout=5).status_code == 200
        except Exception:
            return False
```

### Step 2: Add config settings

Edit `backend/config.py`:

```python
newprovider_api_key: Optional[str] = None
newprovider_enabled: bool = True
```

### Step 3: Register the provider

Edit `backend/providers/__init__.py`:

```python
from .newprovider import NewProviderProvider

PROVIDER_REGISTRY = {
    # ... existing providers ...
    "newprovider": NewProviderProvider,
}
```

### Step 4: Update `.env.example`

```env
SUBLARR_NEWPROVIDER_API_KEY=
SUBLARR_NEWPROVIDER_ENABLED=true
```

---

## Provider Plugins

Since v0.9.0-beta, Sublarr supports loading custom providers as plugins from `/config/plugins/` — no core code changes needed.

See [Plugin Development](/development/plugin-development) for the full plugin guide, including the `config_fields` schema, UI configuration, hot-reload, and security rules.

---

## Provider Best Practices

| DO | DON'T |
|---|---|
| Return empty list on failure | Let exceptions propagate |
| Log all errors with context | Hide errors silently |
| Use `RetryingSession` for HTTP | Retry immediately after 429 |
| Respect `Retry-After` headers | Ignore rate limits |
| Support ZIP/RAR/XZ archive extraction | Extract all files blindly |
| Set reasonable timeouts (5–30s) | Download entire archive to memory |
| Store API keys in config | Log or hardcode API keys |
| Check for API key before requests | Crash when auth is missing |

---

## Testing Providers

**Unit test** with mocked HTTP:

```python
def test_search_series(provider, mocker):
    mocker.patch.object(provider.session, 'get',
        return_value=mocker.Mock(status_code=200, json=lambda: {"results": [...]}))
    results = provider.search(VideoQuery(series="Attack on Titan", season=1, episode=1, language="en"))
    assert len(results) == 1
```

**Test via API:**

```bash
curl -X POST http://localhost:5765/api/v1/providers/test/newprovider \
  -H "X-Api-Key: your-api-key"
```

**Enable debug logging:**

```env
SUBLARR_LOG_LEVEL=DEBUG
```

---

## Potential Future Providers

- **Argenteam** — Spanish/Latin American subtitles (API available)
- **Shooter** — Chinese subtitles (API available)
- **Subscene** — Popular but no official API (scraping required)

Community contributions welcome — see [Contributing](/development/contributing).
