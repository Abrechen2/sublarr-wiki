# Wiki Expansion Design Spec
**Date:** 2026-03-15
**Status:** Draft
**Scope:** Comprehensive expansion of the Sublarr wiki — design system, missing features, settings overhaul, screenshots

---

## 1. Overview

The Sublarr wiki currently has several gaps: the environment variables page lists too many vars (including UI-configurable settings), several features have no documentation, no pages have screenshots, the design is inconsistent, and one navigation icon is missing. This spec covers a full expansion addressing all six areas.

---

## 2. Goals

1. **Env vars cleanup** — Reduce env-vars page to ~8 true `.env` variables; reference Settings UI for everything else
2. **Library icon fix** — Add missing icon to the Library link on the home page
3. **Screenshots** — Embed UI screenshots on all user-guide pages
4. **Missing features** — Document 9 undocumented features with dedicated pages
5. **Settings overhaul** — One dedicated page per settings section with full field tables + screenshots
6. **Design consistency** — Apply uniform patterns (callouts, tables, screenshots, code blocks) across all pages

---

## 3. Page Structure

### New and modified pages

```
en/
├── home.md                               ← Library icon fix + nav table updates
├── getting-started/
│   └── environment-variables.md         ← Reduce to ~8 real .env vars
└── user-guide/
    ├── library.md                        ← Add screenshot + detail
    ├── wanted.md                         ← Add screenshot
    ├── activity.md                       ← Add screenshot
    ├── web-player.md                     ← NEW
    ├── waveform-editor.md                ← NEW
    ├── ai-glossary.md                    ← NEW
    ├── video-sync.md                     ← NEW (ffsubsync/alass)
    ├── notifications.md                  ← NEW (Apprise)
    ├── subtitle-trash.md                 ← NEW
    ├── credit-filtering.md               ← NEW
    ├── stream-removal.md                 ← NEW (Remux)
    ├── hi-removal.md                     ← NEW
    └── settings/
        ├── general.md                    ← Expand with full fields + screenshot
        ├── api-keys.md                   ← NEW
        ├── sonarr-radarr.md              ← NEW
        ├── media-sources.md              ← NEW
        ├── media-server.md               ← NEW
        ├── translation.md                ← NEW
        ├── translation-backends.md       ← NEW
        ├── prompt-templates.md           ← NEW
        ├── languages.md                  ← NEW
        ├── automation.md                 ← NEW
        ├── wanted-settings.md            ← NEW
        ├── whisper.md                    ← NEW
        ├── providers.md                  ← NEW
        ├── scoring.md                    ← NEW
        ├── subtitle-tools.md             ← NEW
        ├── notifications-templates.md    ← NEW
        ├── events-hooks.md               ← NEW
        ├── backup.md                     ← NEW
        ├── cleanup.md                    ← NEW
        ├── integrations.md               ← NEW
        └── security.md                   ← NEW
```

**Total:** ~30 new/modified pages

---

## 4. Design System

Derived from SublarrWeb (`#1DB8D4` teal, dark theme, Inter). Wiki.js-compatible markdown patterns.

### Screenshot embeds

All screenshots are taken from the live Sublarr UI (dark theme) and stored in `SublarrWiki/img/` on disk. They are served from `/img/` in Wiki.js via the asset manager.

**Upload process:** Wiki.js Admin → Assets → upload PNG files. They will be served at `/img/filename.png` and can be referenced directly in markdown. Alternatively, files can be placed directly on the filesystem at the Wiki.js storage path and registered via the admin panel.

Embed directly after the H1:

```markdown
![Settings — General](/img/screen-settings-general.png)
```

Available screenshots (already captured):
- `screen-dashboard.png`
- `screen-library.png`
- `screen-wanted.png`
- `screen-activity.png`
- `screen-settings.png`
- `screen-settings-translation.png`
- `screen-settings-general.png`
- `screen-settings-providers.png`
- `screen-settings-subtitle-tools.png`
- `screen-library-detail.png`
- `screen-web-player.png`
- `screen-interactive-search.png`

Missing screenshots (to be captured during implementation):
- Settings: API Keys, Sonarr, Radarr, Media Sources, Media Server, Translation Backends, Prompt Templates, Languages, Automation, Wanted, Whisper, Scoring, Events & Hooks, Backup, Cleanup, Integrations, Notification Templates, Security
- Features: Waveform Editor (requires episode with subtitle), AI Glossary, Notifications

### Callouts (Wiki.js native)

```markdown
> [!NOTE]
> Informational note

> [!WARNING]
> Important warning

> [!TIP]
> Best practice tip
```

### Settings table format

```markdown
| Setting | Default | Description |
|---------|---------|-------------|
| Port | `5765` | HTTP port Sublarr listens on |
```

### Code blocks

Always include language tag: `bash`, `yaml`, `env`, `json`.

### Page templates

**Settings page:**
```markdown
---
title: Settings — [Name]
description: [One-line description]
published: true
date: YYYY-MM-DD
---

# Settings — [Name]

[One-sentence description of this section]

![Settings — [Name]](/img/screen-settings-xxx.png)

## [Section 1]

| Setting | Default | Description |
|---------|---------|-------------|
| ...     | ...     | ...         |

## [Section 2]
...
```

**Feature page:**
```markdown
---
title: [Feature Name]
description: [One-line description]
published: true
date: YYYY-MM-DD
---

# [Feature Name]

[One-sentence description]

![Feature Screenshot](/img/screen-xxx.png)

## Usage

...

## [Feature-specific sections]

...

> [!TIP]
> ...
```

---

## 5. Content Plan

### Environment Variables (getting-started/environment-variables.md)

Reduce to only variables that **cannot** be set via the Settings UI:

| Variable | Default | Description |
|----------|---------|-------------|
| `SUBLARR_PORT` | `5765` | HTTP port |
| `SUBLARR_CONFIG_DIR` | `/config` | Config + database directory |
| `SUBLARR_MEDIA_PATH` | `/media` | Root media directory |
| `SUBLARR_LOG_LEVEL` | `INFO` | Log level: DEBUG, INFO, WARNING, ERROR |
| `SUBLARR_DB_PATH` | `/config/sublarr.db` | SQLite database path |
| `SUBLARR_REDIS_URL` | _(optional)_ | Redis URL for task queue |
| `SUBLARR_API_KEY` | _(empty)_ | API key for X-Api-Key auth |
| `SUBLARR_POSTGRES_URL` | _(optional)_ | Postgres URL (if not using SQLite) |

All other settings → reference link to the relevant Settings page.

### Home Page

Add icon to Library navigation entry (matching other entries in the nav tables).
After implementation, update home navigation table to include links to all new feature and settings pages.

### User Guide pages with screenshots

- **library.md** — Add `screen-library.png` after H1, add Series Detail section with `screen-library-detail.png`, add Interactive Search section with `screen-interactive-search.png`
- **wanted.md** — Add `screen-wanted.png` after H1
- **activity.md** — Add `screen-activity.png` after H1

### New feature pages

| Page | Screenshot | Key content |
|------|-----------|-------------|
| web-player.md | screen-web-player.png | Video preview modal, subtitle overlay, keyboard shortcuts |
| waveform-editor.md | TBD (needs subtitle present) | Visual timeline editor, drag-to-adjust cues, save/backup |
| ai-glossary.md | screen-ai-glossary.png (TBD) | Per-series term overrides, format, how to build |
| video-sync.md | None needed (CLI tool) | ffsubsync vs alass, when to use, how to trigger |
| notifications.md | Settings screenshot | Apprise URL format, supported services, test notification |
| subtitle-trash.md | None | Delete flow, trash location, restore, auto-purge |
| credit-filtering.md | None | What gets filtered, configuration |
| stream-removal.md | None | Remux explanation, trigger conditions |
| hi-removal.md | screen-settings-subtitle-tools.png | HI markers, what gets removed, backup |

### Settings pages

Each page: frontmatter, H1, one-sentence description, screenshot, complete field tables with defaults and descriptions.

Settings sections identified from live UI:
- **General:** Server (Port, Media Path), Security (API Key), Logging (Log Level), Path Mapping, Config Export/Import, Database
- **API Keys:** External provider API keys
- **Sonarr / Radarr:** URL, API key, sync settings
- **Media Sources:** Library source connections
- **Media Server:** Jellyfin/Emby connection
- **Translation:** LLM provider selection, model, language pair
- **Translation Backends:** Per-backend configuration
- **Prompt Templates:** Custom translation prompts
- **Languages:** Language profile definitions
- **Automation:** Search schedules, triggers
- **Wanted:** Cutoff, upgrade settings
- **Whisper:** Audio transcription settings
- **Providers:** Provider list, priority, Anti-Captcha
- **Scoring:** Score weights, cutoffs
- **Subtitle Tools:** HI removal, timing adjust, common fixes, preview
- **Notification Templates:** Per-event message templates
- **Events & Hooks:** Webhook triggers, event types
- **Backup:** Schedule, retention, restore procedure
- **Cleanup:** Orphaned files, database maintenance
- **Integrations:** Third-party service connections
- **Security:** API key, session settings

---

## 6. Implementation Sequence

### Wave 1 — Quick wins
1. `home.md` — Library icon fix
2. `environment-variables.md` — Reduce to ~8 vars

### Wave 2 — Screenshot embeds on existing pages
3. `library.md` — Screenshots + detail sections
4. `wanted.md` — Screenshot
5. `activity.md` — Screenshot
6. `settings/general.md` — Full expansion with screenshot

### Wave 3 — New feature pages (parallelizable)
7. `web-player.md`, `waveform-editor.md`, `ai-glossary.md`
8. `video-sync.md`, `notifications.md`, `subtitle-trash.md`
9. `credit-filtering.md`, `stream-removal.md`, `hi-removal.md`

### Wave 4 — New settings pages (parallelizable with Wave 3)
10. **Prerequisite:** Capture all missing settings screenshots via Playwright (blocks step 11)
11. Write all ~20 new settings pages (after screenshots are available)
12. Update `home.md` nav table with all new links

---

## 7. Out of Scope

- Wiki.js theme/CSS customization (not supported in Wiki.js 2.x without module)
- Video content / GIFs
- Localization / German-language wiki pages
- API reference documentation
- Architecture / developer docs
