---
title: Upgrade Guide
description: Upgrading Sublarr between versions — migration notes and breaking changes
published: true
date: 2026-04-13
---

# Migration Guide

## Current Version: 0.51.3-beta

---

## Upgrading to 0.51.x

### v0.51.3-beta — Security Hardening & V1 Readiness

No breaking changes. Key improvements:

- **OpenAPI security declarations** added to all 286 API routes
- **Circuit breaker cooldown** increased from 60 to 300 seconds (reduces probe frequency against downed providers)
- **Auth errors propagate to circuit breaker** — HTTP 401/403 from providers now count toward the failure threshold. If your API key expires, the circuit opens after 5 failures instead of retrying indefinitely
- **Firefox subtitle rendering** fixed with WebVTT fallback via native `<track>` element (Chrome continues using SubtitleOctopus)
- **3600+ tests** (737 new) covering translator, translation backends, and repositories
- **SECURITY.md** and **MIGRATION.md** added for V1 release preparation
- **Locust load testing** configuration added

#### Changed Defaults

| Setting | Old Default | New Default | Notes |
|---------|-------------|-------------|-------|
| `circuit_breaker_cooldown_seconds` | `60` | `300` | Reduces probe frequency, lowers ban risk |
| `subtitle_trash_retention_days` | `7` | `30` | Longer recovery window for deleted subtitles |

> [!WARNING]
> If you relied on the 60-second circuit breaker cooldown for fast provider recovery, you may want to explicitly set `SUBLARR_CIRCUIT_BREAKER_COOLDOWN_SECONDS=60` in your `.env` file.

### v0.51.2-beta — Version Bump

Maintenance release with version synchronization.

### v0.51.0-beta — Feature Gate & Quality

- **Translation feature gate** — translation must now be explicitly enabled via `SUBLARR_TRANSLATION_ENABLED=true` (defaults to `false`). Existing users with translation configured will need to enable this setting
- New settings added: interface preferences, quiet hours, disk monitoring, auto backup, scan filters, download limits, per-language score thresholds, session security

> [!NOTE]
> If you were using translation features before v0.51.0, you need to explicitly enable translation after upgrading: go to **Settings → Translation** and toggle **Translation Enabled** to on, or set `SUBLARR_TRANSLATION_ENABLED=true` in your environment.

---

## Upgrading to 0.37.x

### v0.37.0-beta — DateTime Migration (Breaking)

**⚠️ Breaking change: all timestamp columns migrated from TEXT to `DateTime(timezone=True)`.**

#### What changed

Previous versions stored timestamps as ISO 8601 strings with a `T` separator and `+00:00` offset:
```
2024-01-15T10:30:00+00:00
```

From 0.37.0-beta onward, timestamps are stored in SQLAlchemy's native SQLite format (space separator, no offset):
```
2024-01-15 10:30:00
```

#### Migration is automatic

For **Docker deployments**, the Alembic migration `b0c1d2e3f4a5` runs automatically on container startup — no manual action required.

#### Verifying the migration (optional)

A standalone check script is included:

```bash
# Before upgrade — snapshot current row counts
python scripts/check_datetime_migration.py --db /config/sublarr.db --mode before

# After upgrade — verify format and compare counts
python scripts/check_datetime_migration.py --db /config/sublarr.db --mode after
```

Exit code 0 = all checks passed. Exit code 1 = issues found (see output).

#### Session timeout default changed

The default session timeout is now **8 hours** (previously Flask's 31-day default). Configurable via `SUBLARR_SESSION_TIMEOUT_MINUTES`.

---

## Current Version (archive): 0.19.2-beta

This guide covers upgrades from any previous version of Sublarr. For the full change history, see [CHANGELOG.md](../CHANGELOG.md).

For switching from SQLite to PostgreSQL see [POSTGRESQL.md](POSTGRESQL.md).

---

## Upgrading to 0.13.x

### v0.13.2-beta — Security Hardening

No breaking changes. New security features active by default:
- WebSocket authentication now enforced (`SUBLARR_API_KEY` if set)
- Secret masking in logs (API keys, passwords no longer visible in debug output)
- DoS limits on log/batch endpoints
- Path traversal protection improvements

### v0.13.1-beta — Sidecar Management

New settings (all optional, off by default):
- `SUBLARR_AUTO_CLEANUP_AFTER_EXTRACT` — auto-delete extra-language sidecars after batch extract
- `SUBLARR_AUTO_CLEANUP_KEEP_LANGUAGES` — languages to preserve during cleanup
- `SUBLARR_AUTO_CLEANUP_KEEP_FORMATS` — format preference for cleanup
- `SUBLARR_SUBTITLE_TRASH_RETENTION_DAYS` — soft-delete retention period

Database: new `upgrade_history` and `filter_presets` tables added automatically on startup.

### v0.13.0-beta — Tasks Page & Queue Visibility

New endpoints:
- `GET /api/v1/tasks` — background task list
- `POST /api/v1/tasks/<name>/cancel` — cancel running task

Frontend: new Tasks page (`/tasks`), Queue page (`/queue`), Statistics page (`/statistics`).

---

## Upgrading from v0.9.x → v0.13.x (Historical)

## Original Version Renumbering Note

Sublarr adopted standard pre-release versioning early on. The initial v1.0.0-beta was premature — v1.0.0 is reserved for the final stable release. The sequence was:

```
v1.0.0-beta (old) -> v0.9.0-beta -> ... -> v0.13.2-beta (current) -> v1.0.0 (stable)
```

## What Changed in v0.9.0

v0.9.0-beta added: plugin extensibility, multi-backend translation (DeepL, LibreTranslate, OpenAI-compatible, Google), Whisper speech-to-text, standalone mode without Sonarr/Radarr, Plex/Kodi support, forced subtitle management, event hooks, UI internationalization (EN/DE), dark/light theming, backup/restore, statistics, OpenAPI documentation, and performance improvements.

## Upgrade Steps

### Docker (Recommended)

1. **Pull the new image:**
   ```bash
   docker pull ghcr.io/abrechen2/sublarr:0.13.2-beta
   ```

2. **Stop the existing container:**
   ```bash
   docker stop sublarr
   ```

3. **Back up your data** (recommended):
   ```bash
   cp -r /path/to/appdata/sublarr /path/to/appdata/sublarr.backup
   ```

4. **Update your docker-compose.yml** (if using version tags):
   ```yaml
   services:
     sublarr:
       image: ghcr.io/abrechen2/sublarr:0.13.2-beta
       # ... rest of config unchanged
   ```

5. **Start with new image:**
   ```bash
   docker compose up -d
   ```

6. **Database migrations run automatically on startup.** No manual steps required.

### Unraid

1. Go to Docker tab in Unraid
2. Click the Sublarr container icon and select "Edit"
3. Update the Repository field to `ghcr.io/abrechen2/sublarr:0.13.2-beta`
4. Click Apply
5. The container will re-pull and restart automatically

### Configuration Changes

#### New Environment Variables

No new required environment variables. All new features are configured through the web UI Settings page.

Optional variables added for advanced use:

| Variable | Default | Purpose |
|----------|---------|---------|
| `SUBLARR_PLUGIN_HOT_RELOAD` | `false` | Enable watchdog-based plugin hot-reload |
| `SUBLARR_WHISPER_ENABLED` | `false` | Enable Whisper speech-to-text fallback |
| `SUBLARR_WHISPER_BACKEND` | `subgen` | Active Whisper backend (subgen or faster_whisper) |
| `SUBLARR_MAX_CONCURRENT_WHISPER` | `1` | Maximum concurrent Whisper jobs |

#### Removed Variables

None. All existing v1.0.0-beta environment variables continue to work.

#### Changed Defaults

None. All defaults remain the same.

### Breaking Changes

#### API Changes

No breaking API changes. All v1.0.0-beta endpoints continue to work at the same paths with the same request/response formats.

**New endpoints added** (non-breaking):
- `GET /api/v1/openapi.json` -- OpenAPI specification
- `GET /api/docs/` -- Swagger UI
- `GET /api/v1/health/detailed` -- Extended health with 11 subsystem categories
- `GET /api/v1/tasks` -- Background scheduler status
- Provider, translation, media server, whisper, standalone, hooks, and scoring management endpoints

#### Architecture Changes

The backend was refactored from a monolithic `server.py` into 30 Flask Blueprint modules using the Application Factory pattern. **This does not affect the public API** — all endpoints remain at the same paths.

If you referenced internal Python modules (e.g., in custom scripts), update imports:
- `from server import app` becomes `from app import create_app`
- `from database import ...` becomes `from db.xxx import ...` (split into domain modules)

### Plugin System

Custom provider plugins can now be installed in the `/config/plugins/` directory (mapped as a Docker volume). Each plugin is a Python file implementing the `SubtitleProvider` ABC.

See [docs/PROVIDERS.md](PROVIDERS.md) for the plugin development guide.

### New Features Requiring Configuration

All new features work out of the box with sensible defaults. Optional configuration through the Settings UI:

| Feature | Where to Configure | Notes |
|---------|-------------------|---------|
| Translation backends | Settings > Translation Backends | Ollama is the default; add DeepL, LibreTranslate, etc. |
| Media servers | Settings > Media Servers | Plex, Kodi alongside existing Jellyfin/Emby |
| Whisper | Settings > Whisper | Enable and configure faster-whisper or Subgen |
| Standalone mode | Settings > Library Sources | Add watched folders for non-Sonarr/Radarr media |
| Forced subtitles | Language Profiles > Forced Preference | Per-profile forced sub handling |
| Event hooks | Settings > Events & Hooks | Shell scripts and outgoing webhooks |
| Custom scoring | Settings > Scoring | Adjust weights and per-provider modifiers |
| Language | UI header > Language switcher | Toggle between English and German |
| Theme | UI header > Theme toggle | Dark, light, or system preference |
| Backup schedule | Settings > Backup | Configure automatic backup interval |

## Troubleshooting

### Database Migration Issues

Database migrations run automatically on startup. If you encounter issues:

1. **Check container logs:**
   ```bash
   docker logs sublarr
   ```

2. **Restore from backup:**
   ```bash
   docker stop sublarr
   cp /path/to/appdata/sublarr.backup/sublarr.db /path/to/appdata/sublarr/sublarr.db
   docker start sublarr
   ```

3. **Use the built-in backup/restore:** If you created a backup via the UI before upgrading, you can restore it from Settings > Backup > Restore.

### Plugin Loading Errors

Common plugin issues and solutions:

| Problem | Solution |
|---------|----------|
| Plugin not appearing | Check file is in `/config/plugins/` with `.py` extension |
| Import errors | Ensure all dependencies are installed in the container |
| Name collision | Built-in providers always win; rename your plugin class |
| Config not saving | Check plugin `config_fields` match the expected format |
| Hot-reload not working | Enable `SUBLARR_PLUGIN_HOT_RELOAD=true` and ensure `watchdog` is installed |

### Jellyfin/Emby Migration

If you had Jellyfin/Emby configured via the old `SUBLARR_JELLYFIN_URL` / `SUBLARR_JELLYFIN_API_KEY` environment variables, these are **automatically migrated** to the new media server system on first startup. No action required.

To verify: Go to Settings > Media Servers and confirm your Jellyfin/Emby instance appears in the list.

### Performance Issues After Upgrade

The incremental wanted scan is enabled by default. If you notice missing items:

1. Trigger a manual full scan from the Wanted page
2. The system automatically runs a full scan every 6th cycle
3. Check `/api/v1/health/detailed` for subsystem status

### API Key Authentication

If you use the `SUBLARR_API_KEY` setting, all existing authenticated endpoints continue to work with the `X-Api-Key` header. New endpoints follow the same authentication pattern.

## Questions or Issues?

- Open a [GitHub Issue](https://github.com/Abrechen2/sublarr/issues) for bugs or feature requests
- Check the [User Guide](USER-GUIDE.md) for setup instructions
- Browse the [API documentation](http://localhost:5765/api/docs) via Swagger UI
