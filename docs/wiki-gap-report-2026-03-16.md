---

# Settings Coverage Report

| Section | Field | Env Var | In Wiki | Wiki File |
|---------|-------|---------|---------|-----------|
| General | `port` | `SUBLARR_PORT` | ✅ | `SublarrWiki/user-guide/settings/api-keys.md` |
| General | `api_key` | `SUBLARR_API_KEY` | ✅ | `SublarrWiki/user-guide/settings/api-keys.md` |
| General | `log_level` | `SUBLARR_LOG_LEVEL` | ✅ | `SublarrWiki/user-guide/settings/general.md` |
| General | `log_file` | `SUBLARR_LOG_FILE` | ❌ | — |
| General | `media_path` | `SUBLARR_MEDIA_PATH` | ✅ | `SublarrWiki/user-guide/settings/general.md` |
| General | `db_path` | `SUBLARR_DB_PATH` | ❌ | — |
| Defaults to localhost dev origins; set "*" only in fully trusted environments. | `cors_origins` | `SUBLARR_CORS_ORIGINS` | ✅ | `SublarrWiki/user-guide/settings/general.md` |
| Ollama | `ollama_url` | `SUBLARR_OLLAMA_URL` | ✅ | `SublarrWiki/user-guide/settings/translation.md` |
| Ollama | `ollama_model` | `SUBLARR_OLLAMA_MODEL` | ✅ | `SublarrWiki/user-guide/settings/translation.md` |
| Ollama | `batch_size` | `SUBLARR_BATCH_SIZE` | ✅ | `SublarrWiki/user-guide/settings/translation.md` |
| Ollama | `request_timeout` | `SUBLARR_REQUEST_TIMEOUT` | ✅ | `SublarrWiki/user-guide/settings/translation.md` |
| Ollama | `temperature` | `SUBLARR_TEMPERATURE` | ✅ | `SublarrWiki/user-guide/settings/translation-backends.md` |
| Ollama | `max_retries` | `SUBLARR_MAX_RETRIES` | ✅ | `SublarrWiki/user-guide/settings/translation.md` |
| Ollama | `backoff_base` | `SUBLARR_BACKOFF_BASE` | ❌ | — |
| Translation | `source_language` | `SUBLARR_SOURCE_LANGUAGE` | ✅ | `SublarrWiki/user-guide/settings/profiles.md` |
| Translation | `target_language` | `SUBLARR_TARGET_LANGUAGE` | ✅ | `SublarrWiki/user-guide/settings/profiles.md` |
| Translation | `source_language_name` | `SUBLARR_SOURCE_LANGUAGE_NAME` | ✅ | `SublarrWiki/user-guide/settings/translation.md` |
| Translation | `target_language_name` | `SUBLARR_TARGET_LANGUAGE_NAME` | ✅ | `SublarrWiki/user-guide/settings/translation.md` |
| Translation | `prompt_template` | `SUBLARR_PROMPT_TEMPLATE` | ✅ | `SublarrWiki/user-guide/settings/translation.md` |
| Subtitle Providers | `provider_priorities` | `SUBLARR_PROVIDER_PRIORITIES` | ✅ | `SublarrWiki/user-guide/settings/providers.md` |
| Subtitle Providers | `providers_enabled` | `SUBLARR_PROVIDERS_ENABLED` | ❌ | — |
| Subtitle Providers | `providers_hidden` | `SUBLARR_PROVIDERS_HIDDEN` | ❌ | — |
| Turkcealtyazi (Turkish subtitles â€” account required) | `turkcealtyazi_username` | `SUBLARR_TURKCEALTYAZI_USERNAME` | ❌ | — |
| Turkcealtyazi (Turkish subtitles â€” account required) | `turkcealtyazi_password` | `SUBLARR_TURKCEALTYAZI_PASSWORD` | ❌ | — |
| Turkcealtyazi (Turkish subtitles â€” account required) | `provider_search_timeout` | `SUBLARR_PROVIDER_SEARCH_TIMEOUT` | ❌ | — |
| Turkcealtyazi (Turkish subtitles â€” account required) | `provider_cache_ttl_minutes` | `SUBLARR_PROVIDER_CACHE_TTL_MINUTES` | ❌ | — |
| Turkcealtyazi (Turkish subtitles â€” account required) | `provider_auto_prioritize` | `SUBLARR_PROVIDER_AUTO_PRIORITIZE` | ❌ | — |
| Turkcealtyazi (Turkish subtitles â€” account required) | `provider_rate_limit_enabled` | `SUBLARR_PROVIDER_RATE_LIMIT_ENABLED` | ❌ | — |
| Turkcealtyazi (Turkish subtitles â€” account required) | `dedup_on_download` | `SUBLARR_DEDUP_ON_DOWNLOAD` | ❌ | — |
| Turkcealtyazi (Turkish subtitles â€” account required) | `github_token` | `SUBLARR_GITHUB_TOKEN` | ❌ | — |
| Dynamic Provider Timeouts (Phase 3) | `provider_dynamic_timeout_enabled` | `SUBLARR_PROVIDER_DYNAMIC_TIMEOUT_ENABLED` | ❌ | — |
| Dynamic Provider Timeouts (Phase 3) | `provider_dynamic_timeout_min_samples` | `SUBLARR_PROVIDER_DYNAMIC_TIMEOUT_MIN_SAMPLES` | ❌ | — |
| Dynamic Provider Timeouts (Phase 3) | `provider_dynamic_timeout_multiplier` | `SUBLARR_PROVIDER_DYNAMIC_TIMEOUT_MULTIPLIER` | ❌ | — |
| Dynamic Provider Timeouts (Phase 3) | `provider_dynamic_timeout_buffer_secs` | `SUBLARR_PROVIDER_DYNAMIC_TIMEOUT_BUFFER_SECS` | ❌ | — |
| Dynamic Provider Timeouts (Phase 3) | `provider_dynamic_timeout_min_secs` | `SUBLARR_PROVIDER_DYNAMIC_TIMEOUT_MIN_SECS` | ❌ | — |
| Dynamic Provider Timeouts (Phase 3) | `provider_dynamic_timeout_max_secs` | `SUBLARR_PROVIDER_DYNAMIC_TIMEOUT_MAX_SECS` | ❌ | — |
| OpenSubtitles.com (API v2) | `opensubtitles_api_key` | `SUBLARR_OPENSUBTITLES_API_KEY` | ✅ | `SublarrWiki/user-guide/settings/api-keys.md` |
| OpenSubtitles.com (API v2) | `opensubtitles_username` | `SUBLARR_OPENSUBTITLES_USERNAME` | ✅ | `SublarrWiki/user-guide/settings/providers.md` |
| OpenSubtitles.com (API v2) | `opensubtitles_password` | `SUBLARR_OPENSUBTITLES_PASSWORD` | ✅ | `SublarrWiki/user-guide/settings/providers.md` |
| Jimaku (anime subtitles) | `jimaku_api_key` | `SUBLARR_JIMAKU_API_KEY` | ✅ | `SublarrWiki/user-guide/settings/api-keys.md` |
| SubDL (Subscene successor) | `subdl_api_key` | `SUBLARR_SUBDL_API_KEY` | ✅ | `SublarrWiki/user-guide/settings/api-keys.md` |
| Sonarr (optional) | `sonarr_url` | `SUBLARR_SONARR_URL` | ✅ | `SublarrWiki/user-guide/settings/integrations.md` |
| Sonarr (optional) | `sonarr_api_key` | `SUBLARR_SONARR_API_KEY` | ✅ | `SublarrWiki/user-guide/settings/api-keys.md` |
| Sonarr (optional) | `sonarr_instances_json` | `SUBLARR_SONARR_INSTANCES_JSON` | ✅ | `SublarrWiki/user-guide/settings/integrations.md` |
| Radarr (optional â€” for anime movies) | `radarr_url` | `SUBLARR_RADARR_URL` | ✅ | `SublarrWiki/user-guide/settings/integrations.md` |
| Radarr (optional â€” for anime movies) | `radarr_api_key` | `SUBLARR_RADARR_API_KEY` | ✅ | `SublarrWiki/user-guide/settings/api-keys.md` |
| Radarr (optional â€” for anime movies) | `radarr_instances_json` | `SUBLARR_RADARR_INSTANCES_JSON` | ❌ | — |
| Jellyfin/Emby (optional â€” library refresh) | `jellyfin_url` | `SUBLARR_JELLYFIN_URL` | ✅ | `SublarrWiki/user-guide/settings/integrations.md` |
| Jellyfin/Emby (optional â€” library refresh) | `jellyfin_api_key` | `SUBLARR_JELLYFIN_API_KEY` | ✅ | `SublarrWiki/user-guide/settings/integrations.md` |
| Media Servers (multi-backend: Jellyfin, Plex, Kodi) | `media_servers_json` | `SUBLARR_MEDIA_SERVERS_JSON` | ✅ | `SublarrWiki/user-guide/settings/integrations.md` |
| Example: "/data/media=Z:\Media;/anime=Z:\Anime" | `path_mapping` | `SUBLARR_PATH_MAPPING` | ✅ | `SublarrWiki/user-guide/settings/integrations.md` |
| ffmpeg / ffprobe | `ffmpeg_timeout` | `SUBLARR_FFMPEG_TIMEOUT` | ❌ | — |
| Scan Metadata Engine | `scan_metadata_engine` | `SUBLARR_SCAN_METADATA_ENGINE` | ❌ | — |
| Scan Metadata Engine | `scan_metadata_max_workers` | `SUBLARR_SCAN_METADATA_MAX_WORKERS` | ❌ | — |
| Translation Workers | `translation_max_workers` | `SUBLARR_TRANSLATION_MAX_WORKERS` | ✅ | `SublarrWiki/user-guide/settings/translation.md` |
| Wanted System | `wanted_scan_interval_hours` | `SUBLARR_WANTED_SCAN_INTERVAL_HOURS` | ❌ | — |
| Wanted System | `wanted_anime_only` | `SUBLARR_WANTED_ANIME_ONLY` | ❌ | — |
| Wanted System | `wanted_anime_movies_only` | `SUBLARR_WANTED_ANIME_MOVIES_ONLY` | ❌ | — |
| Wanted System | `wanted_scan_on_startup` | `SUBLARR_WANTED_SCAN_ON_STARTUP` | ❌ | — |
| Wanted System | `wanted_auto_extract` | `SUBLARR_WANTED_AUTO_EXTRACT` | ❌ | — |
| Wanted System | `wanted_auto_translate` | `SUBLARR_WANTED_AUTO_TRANSLATE` | ✅ | `SublarrWiki/user-guide/settings/translation.md` |
| Wanted System | `wanted_max_search_attempts` | `SUBLARR_WANTED_MAX_SEARCH_ATTEMPTS` | ❌ | — |
| Wanted System | `use_embedded_subs` | `SUBLARR_USE_EMBEDDED_SUBS` | ❌ | — |
| Wanted System | `scan_yield_ms` | `SUBLARR_SCAN_YIELD_MS` | ❌ | — |
| Provider Re-ranking | `provider_reranking_enabled` | `SUBLARR_PROVIDER_RERANKING_ENABLED` | ❌ | — |
| Provider Re-ranking | `provider_reranking_min_downloads` | `SUBLARR_PROVIDER_RERANKING_MIN_DOWNLOADS` | ❌ | — |
| Provider Re-ranking | `provider_reranking_max_modifier` | `SUBLARR_PROVIDER_RERANKING_MAX_MODIFIER` | ❌ | — |
| Release Group Filtering | `release_group_prefer` | `SUBLARR_RELEASE_GROUP_PREFER` | ❌ | — |
| Release Group Filtering | `release_group_exclude` | `SUBLARR_RELEASE_GROUP_EXCLUDE` | ❌ | — |
| Release Group Filtering | `release_group_prefer_bonus` | `SUBLARR_RELEASE_GROUP_PREFER_BONUS` | ❌ | — |
| Upgrade System | `upgrade_enabled` | `SUBLARR_UPGRADE_ENABLED` | ✅ | `SublarrWiki/user-guide/settings/media-management.md` |
| Upgrade System | `upgrade_min_score_delta` | `SUBLARR_UPGRADE_MIN_SCORE_DELTA` | ✅ | `SublarrWiki/user-guide/settings/media-management.md` |
| Upgrade System | `upgrade_window_days` | `SUBLARR_UPGRADE_WINDOW_DAYS` | ✅ | `SublarrWiki/user-guide/settings/media-management.md` |
| Upgrade System | `upgrade_prefer_ass` | `SUBLARR_UPGRADE_PREFER_ASS` | ✅ | `SublarrWiki/user-guide/settings/media-management.md` |
| Hearing Impaired | `hi_removal_enabled` | `SUBLARR_HI_REMOVAL_ENABLED` | ✅ | `SublarrWiki/user-guide/settings/media-management.md` |
| Hearing Impaired | `hi_preference` | `SUBLARR_HI_PREFERENCE` | ❌ | — |
| Staff Credit Filtering | `credit_threshold_sec` | `SUBLARR_CREDIT_THRESHOLD_SEC` | ✅ | `SublarrWiki/user-guide/settings/translation.md` |
| Staff Credit Filtering | `op_window_sec` | `SUBLARR_OP_WINDOW_SEC` | ✅ | `SublarrWiki/user-guide/settings/translation.md` |
| Forced Subtitles | `forced_preference` | `SUBLARR_FORCED_PREFERENCE` | ❌ | — |
| Webhook Automation | `webhook_delay_minutes` | `SUBLARR_WEBHOOK_DELAY_MINUTES` | ❌ | — |
| Webhook Automation | `webhook_auto_scan` | `SUBLARR_WEBHOOK_AUTO_SCAN` | ❌ | — |
| Webhook Automation | `webhook_auto_search` | `SUBLARR_WEBHOOK_AUTO_SEARCH` | ❌ | — |
| Webhook Automation | `webhook_auto_translate` | `SUBLARR_WEBHOOK_AUTO_TRANSLATE` | ✅ | `SublarrWiki/user-guide/settings/profiles.md` |
| Webhook Automation | `jellyfin_play_translate_enabled` | `SUBLARR_JELLYFIN_PLAY_TRANSLATE_ENABLED` | ❌ | — |
| Video Sync (ffsubsync / alass) | `auto_sync_after_download` | `SUBLARR_AUTO_SYNC_AFTER_DOWNLOAD` | ❌ | — |
| Video Sync (ffsubsync / alass) | `auto_sync_engine` | `SUBLARR_AUTO_SYNC_ENGINE` | ❌ | — |
| NFO Export | `auto_nfo_export` | `SUBLARR_AUTO_NFO_EXPORT` | ❌ | — |
| Glossary | `glossary_enabled` | `SUBLARR_GLOSSARY_ENABLED` | ❌ | — |
| Glossary | `glossary_max_terms` | `SUBLARR_GLOSSARY_MAX_TERMS` | ❌ | — |
| Web Player | `streaming_enabled` | `SUBLARR_STREAMING_ENABLED` | ✅ | `SublarrWiki/user-guide/settings/automation.md` |
| Wanted Search Scheduler | `wanted_search_interval_hours` | `SUBLARR_WANTED_SEARCH_INTERVAL_HOURS` | ❌ | — |
| Wanted Search Scheduler | `wanted_search_on_startup` | `SUBLARR_WANTED_SEARCH_ON_STARTUP` | ❌ | — |
| Wanted Search Scheduler | `wanted_search_max_items_per_run` | `SUBLARR_WANTED_SEARCH_MAX_ITEMS_PER_RUN` | ❌ | — |
| Upgrade Scheduler | `upgrade_scan_interval_hours` | `SUBLARR_UPGRADE_SCAN_INTERVAL_HOURS` | ❌ | — |
| Wanted Adaptive Backoff | `wanted_adaptive_backoff_enabled` | `SUBLARR_WANTED_ADAPTIVE_BACKOFF_ENABLED` | ❌ | — |
| Wanted Adaptive Backoff | `wanted_backoff_base_hours` | `SUBLARR_WANTED_BACKOFF_BASE_HOURS` | ❌ | — |
| Wanted Adaptive Backoff | `wanted_backoff_cap_hours` | `SUBLARR_WANTED_BACKOFF_CAP_HOURS` | ❌ | — |
| Wanted Early Exit | `wanted_skip_srt_on_no_ass` | `SUBLARR_WANTED_SKIP_SRT_ON_NO_ASS` | ❌ | — |
| Notifications (Apprise) | `notification_urls_json` | `SUBLARR_NOTIFICATION_URLS_JSON` | ❌ | — |
| Notifications (Apprise) | `notify_on_download` | `SUBLARR_NOTIFY_ON_DOWNLOAD` | ❌ | — |
| Notifications (Apprise) | `notify_on_upgrade` | `SUBLARR_NOTIFY_ON_UPGRADE` | ❌ | — |
| Notifications (Apprise) | `notify_on_batch_complete` | `SUBLARR_NOTIFY_ON_BATCH_COMPLETE` | ❌ | — |
| Notifications (Apprise) | `notify_on_error` | `SUBLARR_NOTIFY_ON_ERROR` | ❌ | — |
| Notifications (Apprise) | `notify_manual_actions` | `SUBLARR_NOTIFY_MANUAL_ACTIONS` | ❌ | — |
| Anti-Captcha | `anti_captcha_provider` | `SUBLARR_ANTI_CAPTCHA_PROVIDER` | ❌ | — |
| Anti-Captcha | `anti_captcha_api_key` | `SUBLARR_ANTI_CAPTCHA_API_KEY` | ❌ | — |
| Remux / Stream Removal | `remux_trash_dir` | `SUBLARR_REMUX_TRASH_DIR` | ❌ | — |
| Remux / Stream Removal | `remux_backup_retention_days` | `SUBLARR_REMUX_BACKUP_RETENTION_DAYS` | ❌ | — |
| Remux / Stream Removal | `remux_use_reflink` | `SUBLARR_REMUX_USE_REFLINK` | ✅ | `SublarrWiki/user-guide/settings/media-management.md` |
| Remux / Stream Removal | `remux_arr_pause_enabled` | `SUBLARR_REMUX_ARR_PAUSE_ENABLED` | ❌ | — |
| Circuit Breaker | `circuit_breaker_failure_threshold` | `SUBLARR_CIRCUIT_BREAKER_FAILURE_THRESHOLD` | ❌ | — |
| Circuit Breaker | `circuit_breaker_cooldown_seconds` | `SUBLARR_CIRCUIT_BREAKER_COOLDOWN_SECONDS` | ❌ | — |
| Circuit Breaker | `provider_auto_disable_cooldown_minutes` | `SUBLARR_PROVIDER_AUTO_DISABLE_COOLDOWN_MINUTES` | ❌ | — |
| Logging | `log_format` | `SUBLARR_LOG_FORMAT` | ❌ | — |
| Database Backup | `backup_dir` | `SUBLARR_BACKUP_DIR` | ❌ | — |
| Database Backup | `backup_retention_daily` | `SUBLARR_BACKUP_RETENTION_DAILY` | ❌ | — |
| Database Backup | `backup_retention_weekly` | `SUBLARR_BACKUP_RETENTION_WEEKLY` | ❌ | — |
| Database Backup | `backup_retention_monthly` | `SUBLARR_BACKUP_RETENTION_MONTHLY` | ❌ | — |
| Plugin System | `plugins_dir` | `SUBLARR_PLUGINS_DIR` | ❌ | — |
| Plugin System | `plugin_hot_reload` | `SUBLARR_PLUGIN_HOT_RELOAD` | ❌ | — |
| Standalone Mode | `standalone_enabled` | `SUBLARR_STANDALONE_ENABLED` | ❌ | — |
| Standalone Mode | `standalone_scan_interval_hours` | `SUBLARR_STANDALONE_SCAN_INTERVAL_HOURS` | ❌ | — |
| Standalone Mode | `standalone_debounce_seconds` | `SUBLARR_STANDALONE_DEBOUNCE_SECONDS` | ❌ | — |
| Standalone Mode | `standalone_skip_extras` | `SUBLARR_STANDALONE_SKIP_EXTRAS` | ✅ | `SublarrWiki/user-guide/settings/media-sources.md` |
| Standalone Mode | `tmdb_api_key` | `SUBLARR_TMDB_API_KEY` | ❌ | — |
| Standalone Mode | `tvdb_api_key` | `SUBLARR_TVDB_API_KEY` | ❌ | — |
| Standalone Mode | `tvdb_pin` | `SUBLARR_TVDB_PIN` | ❌ | — |
| Standalone Mode | `metadata_cache_ttl_days` | `SUBLARR_METADATA_CACHE_TTL_DAYS` | ❌ | — |
| Sidecar Auto-Cleanup | `auto_cleanup_after_extract` | `SUBLARR_AUTO_CLEANUP_AFTER_EXTRACT` | ❌ | — |
| Sidecar Auto-Cleanup | `auto_cleanup_keep_languages` | `SUBLARR_AUTO_CLEANUP_KEEP_LANGUAGES` | ❌ | — |
| Sidecar Auto-Cleanup | `auto_cleanup_keep_formats` | `SUBLARR_AUTO_CLEANUP_KEEP_FORMATS` | ❌ | — |
| Subtitle Trash / Soft-Delete | `subtitle_trash_retention_days` | `SUBLARR_SUBTITLE_TRASH_RETENTION_DAYS` | ✅ | `SublarrWiki/user-guide/settings/media-management.md` |
| AniDB Integration | `anidb_enabled` | `SUBLARR_ANIDB_ENABLED` | ❌ | — |
| AniDB Integration | `anidb_cache_ttl_days` | `SUBLARR_ANIDB_CACHE_TTL_DAYS` | ❌ | — |
| AniDB Integration | `anidb_custom_field_name` | `SUBLARR_ANIDB_CUSTOM_FIELD_NAME` | ❌ | — |
| AniDB Integration | `anidb_fallback_to_mapping` | `SUBLARR_ANIDB_FALLBACK_TO_MAPPING` | ❌ | — |
| Database (PERF-01, PERF-02) | `database_url` | `SUBLARR_DATABASE_URL` | ❌ | — |
| Database (PERF-01, PERF-02) | `db_pool_size` | `SUBLARR_DB_POOL_SIZE` | ❌ | — |
| Database (PERF-01, PERF-02) | `db_pool_max_overflow` | `SUBLARR_DB_POOL_MAX_OVERFLOW` | ❌ | — |
| Database (PERF-01, PERF-02) | `db_pool_recycle` | `SUBLARR_DB_POOL_RECYCLE` | ❌ | — |
| Redis (PERF-04, PERF-06) | `redis_url` | `SUBLARR_REDIS_URL` | ❌ | — |
| Redis (PERF-04, PERF-06) | `redis_cache_enabled` | `SUBLARR_REDIS_CACHE_ENABLED` | ❌ | — |
| Redis (PERF-04, PERF-06) | `redis_queue_enabled` | `SUBLARR_REDIS_QUEUE_ENABLED` | ❌ | — |

## Summary
- Total fields: **143**
- Documented: **45**
- **Missing: 98**

## Missing Fields

| Section | Field | Env Var |
|---------|-------|---------|
| General | `log_file` | `SUBLARR_LOG_FILE` |
| General | `db_path` | `SUBLARR_DB_PATH` |
| Ollama | `backoff_base` | `SUBLARR_BACKOFF_BASE` |
| Subtitle Providers | `providers_enabled` | `SUBLARR_PROVIDERS_ENABLED` |
| Subtitle Providers | `providers_hidden` | `SUBLARR_PROVIDERS_HIDDEN` |
| Turkcealtyazi (Turkish subtitles â€” account required) | `turkcealtyazi_username` | `SUBLARR_TURKCEALTYAZI_USERNAME` |
| Turkcealtyazi (Turkish subtitles â€” account required) | `turkcealtyazi_password` | `SUBLARR_TURKCEALTYAZI_PASSWORD` |
| Turkcealtyazi (Turkish subtitles â€” account required) | `provider_search_timeout` | `SUBLARR_PROVIDER_SEARCH_TIMEOUT` |
| Turkcealtyazi (Turkish subtitles â€” account required) | `provider_cache_ttl_minutes` | `SUBLARR_PROVIDER_CACHE_TTL_MINUTES` |
| Turkcealtyazi (Turkish subtitles â€” account required) | `provider_auto_prioritize` | `SUBLARR_PROVIDER_AUTO_PRIORITIZE` |
| Turkcealtyazi (Turkish subtitles â€” account required) | `provider_rate_limit_enabled` | `SUBLARR_PROVIDER_RATE_LIMIT_ENABLED` |
| Turkcealtyazi (Turkish subtitles â€” account required) | `dedup_on_download` | `SUBLARR_DEDUP_ON_DOWNLOAD` |
| Turkcealtyazi (Turkish subtitles â€” account required) | `github_token` | `SUBLARR_GITHUB_TOKEN` |
| Dynamic Provider Timeouts (Phase 3) | `provider_dynamic_timeout_enabled` | `SUBLARR_PROVIDER_DYNAMIC_TIMEOUT_ENABLED` |
| Dynamic Provider Timeouts (Phase 3) | `provider_dynamic_timeout_min_samples` | `SUBLARR_PROVIDER_DYNAMIC_TIMEOUT_MIN_SAMPLES` |
| Dynamic Provider Timeouts (Phase 3) | `provider_dynamic_timeout_multiplier` | `SUBLARR_PROVIDER_DYNAMIC_TIMEOUT_MULTIPLIER` |
| Dynamic Provider Timeouts (Phase 3) | `provider_dynamic_timeout_buffer_secs` | `SUBLARR_PROVIDER_DYNAMIC_TIMEOUT_BUFFER_SECS` |
| Dynamic Provider Timeouts (Phase 3) | `provider_dynamic_timeout_min_secs` | `SUBLARR_PROVIDER_DYNAMIC_TIMEOUT_MIN_SECS` |
| Dynamic Provider Timeouts (Phase 3) | `provider_dynamic_timeout_max_secs` | `SUBLARR_PROVIDER_DYNAMIC_TIMEOUT_MAX_SECS` |
| Radarr (optional â€” for anime movies) | `radarr_instances_json` | `SUBLARR_RADARR_INSTANCES_JSON` |
| ffmpeg / ffprobe | `ffmpeg_timeout` | `SUBLARR_FFMPEG_TIMEOUT` |
| Scan Metadata Engine | `scan_metadata_engine` | `SUBLARR_SCAN_METADATA_ENGINE` |
| Scan Metadata Engine | `scan_metadata_max_workers` | `SUBLARR_SCAN_METADATA_MAX_WORKERS` |
| Wanted System | `wanted_scan_interval_hours` | `SUBLARR_WANTED_SCAN_INTERVAL_HOURS` |
| Wanted System | `wanted_anime_only` | `SUBLARR_WANTED_ANIME_ONLY` |
| Wanted System | `wanted_anime_movies_only` | `SUBLARR_WANTED_ANIME_MOVIES_ONLY` |
| Wanted System | `wanted_scan_on_startup` | `SUBLARR_WANTED_SCAN_ON_STARTUP` |
| Wanted System | `wanted_auto_extract` | `SUBLARR_WANTED_AUTO_EXTRACT` |
| Wanted System | `wanted_max_search_attempts` | `SUBLARR_WANTED_MAX_SEARCH_ATTEMPTS` |
| Wanted System | `use_embedded_subs` | `SUBLARR_USE_EMBEDDED_SUBS` |
| Wanted System | `scan_yield_ms` | `SUBLARR_SCAN_YIELD_MS` |
| Provider Re-ranking | `provider_reranking_enabled` | `SUBLARR_PROVIDER_RERANKING_ENABLED` |
| Provider Re-ranking | `provider_reranking_min_downloads` | `SUBLARR_PROVIDER_RERANKING_MIN_DOWNLOADS` |
| Provider Re-ranking | `provider_reranking_max_modifier` | `SUBLARR_PROVIDER_RERANKING_MAX_MODIFIER` |
| Release Group Filtering | `release_group_prefer` | `SUBLARR_RELEASE_GROUP_PREFER` |
| Release Group Filtering | `release_group_exclude` | `SUBLARR_RELEASE_GROUP_EXCLUDE` |
| Release Group Filtering | `release_group_prefer_bonus` | `SUBLARR_RELEASE_GROUP_PREFER_BONUS` |
| Hearing Impaired | `hi_preference` | `SUBLARR_HI_PREFERENCE` |
| Forced Subtitles | `forced_preference` | `SUBLARR_FORCED_PREFERENCE` |
| Webhook Automation | `webhook_delay_minutes` | `SUBLARR_WEBHOOK_DELAY_MINUTES` |
| Webhook Automation | `webhook_auto_scan` | `SUBLARR_WEBHOOK_AUTO_SCAN` |
| Webhook Automation | `webhook_auto_search` | `SUBLARR_WEBHOOK_AUTO_SEARCH` |
| Webhook Automation | `jellyfin_play_translate_enabled` | `SUBLARR_JELLYFIN_PLAY_TRANSLATE_ENABLED` |
| Video Sync (ffsubsync / alass) | `auto_sync_after_download` | `SUBLARR_AUTO_SYNC_AFTER_DOWNLOAD` |
| Video Sync (ffsubsync / alass) | `auto_sync_engine` | `SUBLARR_AUTO_SYNC_ENGINE` |
| NFO Export | `auto_nfo_export` | `SUBLARR_AUTO_NFO_EXPORT` |
| Glossary | `glossary_enabled` | `SUBLARR_GLOSSARY_ENABLED` |
| Glossary | `glossary_max_terms` | `SUBLARR_GLOSSARY_MAX_TERMS` |
| Wanted Search Scheduler | `wanted_search_interval_hours` | `SUBLARR_WANTED_SEARCH_INTERVAL_HOURS` |
| Wanted Search Scheduler | `wanted_search_on_startup` | `SUBLARR_WANTED_SEARCH_ON_STARTUP` |
| Wanted Search Scheduler | `wanted_search_max_items_per_run` | `SUBLARR_WANTED_SEARCH_MAX_ITEMS_PER_RUN` |
| Upgrade Scheduler | `upgrade_scan_interval_hours` | `SUBLARR_UPGRADE_SCAN_INTERVAL_HOURS` |
| Wanted Adaptive Backoff | `wanted_adaptive_backoff_enabled` | `SUBLARR_WANTED_ADAPTIVE_BACKOFF_ENABLED` |
| Wanted Adaptive Backoff | `wanted_backoff_base_hours` | `SUBLARR_WANTED_BACKOFF_BASE_HOURS` |
| Wanted Adaptive Backoff | `wanted_backoff_cap_hours` | `SUBLARR_WANTED_BACKOFF_CAP_HOURS` |
| Wanted Early Exit | `wanted_skip_srt_on_no_ass` | `SUBLARR_WANTED_SKIP_SRT_ON_NO_ASS` |
| Notifications (Apprise) | `notification_urls_json` | `SUBLARR_NOTIFICATION_URLS_JSON` |
| Notifications (Apprise) | `notify_on_download` | `SUBLARR_NOTIFY_ON_DOWNLOAD` |
| Notifications (Apprise) | `notify_on_upgrade` | `SUBLARR_NOTIFY_ON_UPGRADE` |
| Notifications (Apprise) | `notify_on_batch_complete` | `SUBLARR_NOTIFY_ON_BATCH_COMPLETE` |
| Notifications (Apprise) | `notify_on_error` | `SUBLARR_NOTIFY_ON_ERROR` |
| Notifications (Apprise) | `notify_manual_actions` | `SUBLARR_NOTIFY_MANUAL_ACTIONS` |
| Anti-Captcha | `anti_captcha_provider` | `SUBLARR_ANTI_CAPTCHA_PROVIDER` |
| Anti-Captcha | `anti_captcha_api_key` | `SUBLARR_ANTI_CAPTCHA_API_KEY` |
| Remux / Stream Removal | `remux_trash_dir` | `SUBLARR_REMUX_TRASH_DIR` |
| Remux / Stream Removal | `remux_backup_retention_days` | `SUBLARR_REMUX_BACKUP_RETENTION_DAYS` |
| Remux / Stream Removal | `remux_arr_pause_enabled` | `SUBLARR_REMUX_ARR_PAUSE_ENABLED` |
| Circuit Breaker | `circuit_breaker_failure_threshold` | `SUBLARR_CIRCUIT_BREAKER_FAILURE_THRESHOLD` |
| Circuit Breaker | `circuit_breaker_cooldown_seconds` | `SUBLARR_CIRCUIT_BREAKER_COOLDOWN_SECONDS` |
| Circuit Breaker | `provider_auto_disable_cooldown_minutes` | `SUBLARR_PROVIDER_AUTO_DISABLE_COOLDOWN_MINUTES` |
| Logging | `log_format` | `SUBLARR_LOG_FORMAT` |
| Database Backup | `backup_dir` | `SUBLARR_BACKUP_DIR` |
| Database Backup | `backup_retention_daily` | `SUBLARR_BACKUP_RETENTION_DAILY` |
| Database Backup | `backup_retention_weekly` | `SUBLARR_BACKUP_RETENTION_WEEKLY` |
| Database Backup | `backup_retention_monthly` | `SUBLARR_BACKUP_RETENTION_MONTHLY` |
| Plugin System | `plugins_dir` | `SUBLARR_PLUGINS_DIR` |
| Plugin System | `plugin_hot_reload` | `SUBLARR_PLUGIN_HOT_RELOAD` |
| Standalone Mode | `standalone_enabled` | `SUBLARR_STANDALONE_ENABLED` |
| Standalone Mode | `standalone_scan_interval_hours` | `SUBLARR_STANDALONE_SCAN_INTERVAL_HOURS` |
| Standalone Mode | `standalone_debounce_seconds` | `SUBLARR_STANDALONE_DEBOUNCE_SECONDS` |
| Standalone Mode | `tmdb_api_key` | `SUBLARR_TMDB_API_KEY` |
| Standalone Mode | `tvdb_api_key` | `SUBLARR_TVDB_API_KEY` |
| Standalone Mode | `tvdb_pin` | `SUBLARR_TVDB_PIN` |
| Standalone Mode | `metadata_cache_ttl_days` | `SUBLARR_METADATA_CACHE_TTL_DAYS` |
| Sidecar Auto-Cleanup | `auto_cleanup_after_extract` | `SUBLARR_AUTO_CLEANUP_AFTER_EXTRACT` |
| Sidecar Auto-Cleanup | `auto_cleanup_keep_languages` | `SUBLARR_AUTO_CLEANUP_KEEP_LANGUAGES` |
| Sidecar Auto-Cleanup | `auto_cleanup_keep_formats` | `SUBLARR_AUTO_CLEANUP_KEEP_FORMATS` |
| AniDB Integration | `anidb_enabled` | `SUBLARR_ANIDB_ENABLED` |
| AniDB Integration | `anidb_cache_ttl_days` | `SUBLARR_ANIDB_CACHE_TTL_DAYS` |
| AniDB Integration | `anidb_custom_field_name` | `SUBLARR_ANIDB_CUSTOM_FIELD_NAME` |
| AniDB Integration | `anidb_fallback_to_mapping` | `SUBLARR_ANIDB_FALLBACK_TO_MAPPING` |
| Database (PERF-01, PERF-02) | `database_url` | `SUBLARR_DATABASE_URL` |
| Database (PERF-01, PERF-02) | `db_pool_size` | `SUBLARR_DB_POOL_SIZE` |
| Database (PERF-01, PERF-02) | `db_pool_max_overflow` | `SUBLARR_DB_POOL_MAX_OVERFLOW` |
| Database (PERF-01, PERF-02) | `db_pool_recycle` | `SUBLARR_DB_POOL_RECYCLE` |
| Redis (PERF-04, PERF-06) | `redis_url` | `SUBLARR_REDIS_URL` |
| Redis (PERF-04, PERF-06) | `redis_cache_enabled` | `SUBLARR_REDIS_CACHE_ENABLED` |
| Redis (PERF-04, PERF-06) | `redis_queue_enabled` | `SUBLARR_REDIS_QUEUE_ENABLED` |


---

# Feature Coverage Report

## CHANGELOG Features

| Version | Type | Feature | In Wiki | Wiki File |
|---------|------|---------|---------|-----------|
| `0.30.0-beta` | Added | Standalone | ✅ | `SublarrWiki/home.md` |
| `0.30.0-beta` | Added | Standalone | ✅ | `SublarrWiki/home.md` |
| `0.30.0-beta` | Fixed | Standalone | ✅ | `SublarrWiki/home.md` |
| `0.30.0-beta` | Fixed | Standalone | ✅ | `SublarrWiki/home.md` |
| `0.30.0-beta` | Fixed | Standalone | ✅ | `SublarrWiki/home.md` |
| `0.30.0-beta` | Fixed | Standalone | ✅ | `SublarrWiki/home.md` |
| `0.30.0-beta` | Fixed | Standalone | ✅ | `SublarrWiki/home.md` |
| `0.30.0-beta` | Fixed | Standalone | ✅ | `SublarrWiki/home.md` |
| `0.30.0-beta` | Fixed | Settings | ✅ | `SublarrWiki/home.md` |
| `0.30.0-beta` | Fixed | Wanted | ✅ | `SublarrWiki/home.md` |
| `0.30.0-beta` | Changed | Dependencies | ✅ | `SublarrWiki/development/plugin-development.md` |
| `0.29.0-beta` | Added | Web Player | ✅ | `SublarrWiki/home.md` |
| `0.29.0-beta` | Added | Web Player | ✅ | `SublarrWiki/home.md` |
| `0.29.0-beta` | Added | Web Player | ✅ | `SublarrWiki/home.md` |
| `0.29.0-beta` | Added | Web Player | ✅ | `SublarrWiki/home.md` |
| `0.29.0-beta` | Added | Web Player | ✅ | `SublarrWiki/home.md` |
| `0.29.0-beta` | Added | Web Player | ✅ | `SublarrWiki/home.md` |
| `0.28.0-beta` | Added | AI Glossary Builder | ✅ | `SublarrWiki/getting-started/upgrade-guide.md` |
| `0.28.0-beta` | Added | AI Glossary Builder | ✅ | `SublarrWiki/getting-started/upgrade-guide.md` |
| `0.28.0-beta` | Added | AI Glossary Builder | ✅ | `SublarrWiki/getting-started/upgrade-guide.md` |
| `0.28.0-beta` | Added | AI Glossary Builder | ✅ | `SublarrWiki/getting-started/upgrade-guide.md` |
| `0.28.0-beta` | Added | AI Glossary Builder | ✅ | `SublarrWiki/getting-started/upgrade-guide.md` |
| `0.28.0-beta` | Added | AI Glossary Builder | ✅ | `SublarrWiki/getting-started/upgrade-guide.md` |
| `0.28.0-beta` | Added | AI Glossary Builder | ✅ | `SublarrWiki/getting-started/upgrade-guide.md` |
| `0.28.0-beta` | Added | AI Glossary Builder | ✅ | `SublarrWiki/getting-started/upgrade-guide.md` |
| `0.27.0-beta` | Added | NFO Export | ✅ | `SublarrWiki/getting-started/upgrade-guide.md` |
| `0.27.0-beta` | Added | NFO Export | ✅ | `SublarrWiki/getting-started/upgrade-guide.md` |
| `0.27.0-beta` | Added | NFO Export | ✅ | `SublarrWiki/getting-started/upgrade-guide.md` |
| `0.27.0-beta` | Added | NFO Export | ✅ | `SublarrWiki/getting-started/upgrade-guide.md` |
| `0.26.0-beta` | Added | Single-Account Login | ✅ | `SublarrWiki/getting-started/upgrade-guide.md` |
| `0.26.0-beta` | Added | Single-Account Login | ✅ | `SublarrWiki/getting-started/upgrade-guide.md` |
| `0.26.0-beta` | Added | Single-Account Login | ✅ | `SublarrWiki/getting-started/upgrade-guide.md` |
| `0.26.0-beta` | Added | Single-Account Login | ✅ | `SublarrWiki/getting-started/upgrade-guide.md` |
| `0.26.0-beta` | Added | Settings → Security tab | ✅ | `SublarrWiki/getting-started/upgrade-guide.md` |
| `0.26.0-beta` | Added | Sidebar | ✅ | `SublarrWiki/getting-started/upgrade-guide.md` |
| `0.25.3-beta` | Added | List Virtualization | ✅ | `SublarrWiki/getting-started/upgrade-guide.md` |
| `0.25.3-beta` | Added | List Virtualization | ✅ | `SublarrWiki/getting-started/upgrade-guide.md` |
| `0.25.2-beta` | Added | Subtitle Diff Viewer | ✅ | `SublarrWiki/getting-started/upgrade-guide.md` |
| `0.25.1-beta` | Added | CLI | ❌ | — |
| `0.25.1-beta` | Added | CLI | ❌ | — |
| `0.25.1-beta` | Added | CLI | ❌ | — |
| `0.25.1-beta` | Added | CLI | ❌ | — |
| `0.25.1-beta` | Added | CLI | ❌ | — |
| `0.25.0-beta` | Added | Jellyfin Play-Start Webhook | ✅ | `SublarrWiki/home.md` |
| `0.25.0-beta` | Added | `JellyfinEmbyServer.get_item_path_by_id()` | ❌ | — |
| `0.25.0-beta` | Added | `MediaServerManager.get_item_path_from_jellyfin()` | ❌ | — |
| `0.25.0-beta` | Added | Config | ✅ | `SublarrWiki/home.md` |
| `0.24.4-beta` | Added | ChapterCache | ❌ | — |
| `0.24.4-beta` | Added | `backend/chapters.py` | ❌ | — |
| `0.24.4-beta` | Added | `GET /api/v1/tools/chapters?video_path=…` | ❌ | — |
| `0.24.4-beta` | Added | Advanced Sync | ✅ | `SublarrWiki/getting-started/installation.md` |
| `0.24.4-beta` | Added | SyncControls | ❌ | — |
| `0.24.3-beta` | Added | FansubPreference | ❌ | — |
| `0.24.3-beta` | Added | `GET/PUT/DELETE /api/v1/series/<id>/fansub-prefs` | ❌ | — |
| `0.24.3-beta` | Added | Scoring Hook | ✅ | `SublarrWiki/home.md` |
| `0.24.3-beta` | Added | SeriesFansubPrefsPanel | ❌ | — |
| `0.24.2-beta` | Added | SeriesSettings | ❌ | — |
| `0.24.2-beta` | Added | `GET/PUT /api/v1/series/<id>/audio-track-pref` | ❌ | — |
| `0.24.2-beta` | Added | WhisperQueue | ❌ | — |
| `0.24.2-beta` | Added | SeriesAudioTrackPicker | ❌ | — |
| `0.24.1-beta` | Added | OP/ED Detector | ✅ | `SublarrWiki/user-guide/credit-filtering.md` |
| `0.24.1-beta` | Added | `POST /api/v1/tools/detect-opening-ending` | ✅ | `SublarrWiki/getting-started/environment-variables.md` |
| `0.24.1-beta` | Added | Config | ✅ | `SublarrWiki/home.md` |
| `0.24.1-beta` | Changed | SubtitleEditorModal | ✅ | `SublarrWiki/getting-started/upgrade-guide.md` |
| `0.24.0-beta` | Added | Credit Remover | ✅ | `SublarrWiki/home.md` |
| `0.24.0-beta` | Added | `POST /api/v1/tools/remove-credits` | ✅ | `SublarrWiki/getting-started/environment-variables.md` |
| `0.24.0-beta` | Added | Config | ✅ | `SublarrWiki/home.md` |
| `0.24.0-beta` | Changed | SubtitleEditorModal | ✅ | `SublarrWiki/getting-started/upgrade-guide.md` |
| `0.23.0-beta` | Added | Batch Translate | ✅ | `SublarrWiki/user-guide/library.md` |
| `0.23.0-beta` | Added | Batch Search Extended | ✅ | `SublarrWiki/user-guide/library.md` |
| `0.23.0-beta` | Added | Library | ✅ | `SublarrWiki/home.md` |
| `0.23.0-beta` | Added | SeriesDetail | ✅ | `SublarrWiki/en/development/architecture.md` |
| `0.23.0-beta` | Added | Filter Presets | ✅ | `SublarrWiki/home.md` |
| `0.23.0-beta` | Added | Global Search (Ctrl+K) | ✅ | `SublarrWiki/en/development/postgresql.md` |
| `0.23.0-beta` | Added | Auto-Extract on Scan | ✅ | `SublarrWiki/en/home.md` |
| `0.22.0-beta` | Added | Marketplace | ✅ | `SublarrWiki/development/plugin-development.md` |
| `0.22.0-beta` | Added | Marketplace | ✅ | `SublarrWiki/development/plugin-development.md` |
| `0.22.0-beta` | Added | Marketplace | ✅ | `SublarrWiki/development/plugin-development.md` |
| `0.22.0-beta` | Added | Marketplace | ✅ | `SublarrWiki/development/plugin-development.md` |
| `0.22.0-beta` | Added | Marketplace | ✅ | `SublarrWiki/development/plugin-development.md` |
| `0.22.0-beta` | Added | Marketplace | ✅ | `SublarrWiki/development/plugin-development.md` |
| `0.22.0-beta` | Added | Marketplace | ✅ | `SublarrWiki/development/plugin-development.md` |
| `0.22.0-beta` | Added | Marketplace | ✅ | `SublarrWiki/development/plugin-development.md` |
| `0.22.0-beta` | Added | Config | ✅ | `SublarrWiki/home.md` |
| `0.22.0-beta` | Added | DB Migration `a2b3c4d5e6f7` | ✅ | `SublarrWiki/getting-started/upgrade-guide.md` |
| `0.22.0-beta` | Added | SSRF Prevention | ❌ | — |
| `0.22.0-beta` | Added | Path Traversal | ✅ | `SublarrWiki/en/development/architecture.md` |
| `0.22.0-beta` | Added | XSS Prevention | ✅ | `SublarrWiki/en/development/architecture.md` |
| `0.21.1-beta` | Added | Accessibility | ✅ | `SublarrWiki/user-guide/hi-removal.md` |
| `0.21.1-beta` | Added | Accessibility | ✅ | `SublarrWiki/user-guide/hi-removal.md` |
| `0.21.1-beta` | Added | Accessibility | ✅ | `SublarrWiki/user-guide/hi-removal.md` |
| `0.21.1-beta` | Added | Accessibility | ✅ | `SublarrWiki/user-guide/hi-removal.md` |
| `0.21.1-beta` | Added | Accessibility | ✅ | `SublarrWiki/user-guide/hi-removal.md` |
| `0.21.1-beta` | Added | Accessibility | ✅ | `SublarrWiki/user-guide/hi-removal.md` |
| `0.21.1-beta` | Added | StatusBadge | ✅ | `SublarrWiki/en/development/architecture.md` |
| `0.21.1-beta` | Added | Page-Specific Skeletons | ❌ | — |
| `0.21.1-beta` | Added | `prefers-reduced-motion` | ❌ | — |
| `0.21.1-beta` | Changed | Library Grid | ✅ | `SublarrWiki/home.md` |
| `0.21.1-beta` | Changed | Stagger Animation | ❌ | — |
| `0.21.1-beta` | Changed | CSS Hover | ❌ | — |
| `0.20.0-beta` | Added | PostgreSQL | ✅ | `SublarrWiki/en/home.md` |
| `0.20.0-beta` | Added | Incremental Metadata Cache | ✅ | `SublarrWiki/getting-started/upgrade-guide.md` |
| `0.20.0-beta` | Added | Background Wanted Scanner | ✅ | `SublarrWiki/user-guide/activity.md` |
| `0.20.0-beta` | Added | Parallel Translation Workers | ✅ | `SublarrWiki/en/development/architecture.md` |
| `0.20.0-beta` | Added | Redis Job Queue | ✅ | `SublarrWiki/getting-started/environment-variables.md` |
| `0.19.2-beta` | Fixed | Remux Engine | ✅ | `SublarrWiki/en/development/architecture.md` |
| `0.19.1-beta` | Fixed | Dockerfile | ❌ | — |
| `0.19.0-beta` | Added | Stream Removal | ✅ | `SublarrWiki/home.md` |
| `0.19.0-beta` | Added | Trash-Folder Backups | ❌ | — |
| `0.19.0-beta` | Added | Async Remux Jobs | ✅ | `SublarrWiki/user-guide/activity.md` |
| `0.19.0-beta` | Added | Backup Management API | ✅ | `SublarrWiki/home.md` |
| `0.19.0-beta` | Added | Undo / Restore | ❌ | — |
| `0.18.0-beta` | Added | HI Support | ❌ | — |
| `0.18.0-beta` | Added | Forced Subtitle Support | ✅ | `SublarrWiki/user-guide/library.md` |
| `0.18.0-beta` | Added | TRaSH Scoring Presets | ✅ | `SublarrWiki/home.md` |
| `0.18.0-beta` | Added | Anti-Captcha Integration | ✅ | `SublarrWiki/home.md` |
| `0.17.0-beta` | Added | Duplicate Detection | ✅ | `SublarrWiki/user-guide/translation-llm.md` |
| `0.17.0-beta` | Added | Smart Episode Matching | ❌ | — |
| `0.17.0-beta` | Added | Video Hash Pre-Compute | ✅ | `SublarrWiki/user-guide/settings/scoring.md` |
| `0.17.0-beta` | Added | Release Group Filtering | ✅ | `SublarrWiki/development/plugin-development.md` |
| `0.17.0-beta` | Added | Provider Result Re-ranking | ✅ | `SublarrWiki/user-guide/settings/languages.md` |
| `0.17.0-beta` | Added | Subtitle Upgrade Scheduler | ✅ | `SublarrWiki/en/development/architecture.md` |
| `0.17.0-beta` | Added | Translation Quality Dashboard | ✅ | `SublarrWiki/user-guide/translation-llm.md` |
| `0.17.0-beta` | Added | Custom Post-Processing Scripts | ✅ | `SublarrWiki/home.md` |
| `0.15.2-beta` | Added | Activity | ✅ | `SublarrWiki/home.md` |
| `0.15.2-beta` | Added | History | ✅ | `SublarrWiki/home.md` |
| `0.15.2-beta` | Added | SeriesDetail | ✅ | `SublarrWiki/en/development/architecture.md` |
| `0.15.2-beta` | Added | Activity | ✅ | `SublarrWiki/home.md` |
| `0.15.2-beta` | Fixed | Wanted | ✅ | `SublarrWiki/home.md` |
| `0.15.2-beta` | Fixed | Backend | ✅ | `SublarrWiki/home.md` |
| `0.15.1-beta` | Fixed | App | ❌ | — |
| `0.15.1-beta` | Fixed | App | ❌ | — |
| `0.15.1-beta` | Fixed | Scoring | ✅ | `SublarrWiki/home.md` |
| `0.15.0-beta` | Added | Sidebar | ✅ | `SublarrWiki/getting-started/upgrade-guide.md` |
| `0.15.0-beta` | Fixed | Wanted | ✅ | `SublarrWiki/home.md` |
| `0.14.2-beta` | Added | Wanted | ✅ | `SublarrWiki/home.md` |
| `0.14.2-beta` | Added | Wanted | ✅ | `SublarrWiki/home.md` |
| `0.14.2-beta` | Added | Wanted | ✅ | `SublarrWiki/home.md` |
| `0.14.2-beta` | Changed | Wanted | ✅ | `SublarrWiki/home.md` |
| `0.14.1-beta` | Added | Library | ✅ | `SublarrWiki/home.md` |
| `0.14.1-beta` | Added | Library | ✅ | `SublarrWiki/home.md` |
| `0.14.1-beta` | Added | Wanted | ✅ | `SublarrWiki/home.md` |
| `0.14.1-beta` | Added | Settings | ✅ | `SublarrWiki/home.md` |
| `0.14.1-beta` | Added | SeriesDetail | ✅ | `SublarrWiki/en/development/architecture.md` |
| `0.14.1-beta` | Fixed | Sidebar | ✅ | `SublarrWiki/getting-started/upgrade-guide.md` |
| `0.14.1-beta` | Fixed | i18n | ❌ | — |
| `0.14.1-beta` | Fixed | i18n | ❌ | — |
| `0.14.1-beta` | Fixed | i18n | ❌ | — |
| `0.14.1-beta` | Fixed | Settings | ✅ | `SublarrWiki/home.md` |
| `0.14.1-beta` | Changed | Statistics | ✅ | `SublarrWiki/en/development/api-reference.md` |
| `0.14.1-beta` | Changed | Statistics | ✅ | `SublarrWiki/en/development/api-reference.md` |
| `0.14.0-beta` | Added | Provider UI | ✅ | `SublarrWiki/home.md` |
| `0.14.0-beta` | Added | Provider | ✅ | `SublarrWiki/home.md` |
| `0.14.0-beta` | Added | Provider | ✅ | `SublarrWiki/home.md` |
| `0.14.0-beta` | Added | Provider | ✅ | `SublarrWiki/home.md` |
| `0.14.0-beta` | Added | Provider | ✅ | `SublarrWiki/home.md` |
| `0.14.0-beta` | Added | Language expansion | ✅ | `SublarrWiki/home.md` |
| `0.14.0-beta` | Added | LanguageSelect component | ❌ | — |
| `0.14.0-beta` | Changed | Settings | ✅ | `SublarrWiki/home.md` |
| `0.14.0-beta` | Changed | Provider reactive health checks | ✅ | `SublarrWiki/home.md` |
| `0.14.0-beta` | Changed | Provider UI grid | ✅ | `SublarrWiki/home.md` |
| `0.14.0-beta` | Changed | CI | ❌ | — |
| `0.13.2-beta` | Changed | Path traversal hardening | ✅ | `SublarrWiki/en/development/architecture.md` |
| `0.13.2-beta` | Changed | WebSocket authentication | ✅ | `SublarrWiki/en/getting-started/upgrade-guide.md` |
| `0.13.2-beta` | Changed | Secret masking in API responses | ✅ | `SublarrWiki/en/getting-started/upgrade-guide.md` |
| `0.13.2-beta` | Changed | Request size limit | ✅ | `SublarrWiki/development/plugin-development.md` |
| `0.13.2-beta` | Changed | Hook script path restriction | ❌ | — |
| `0.13.2-beta` | Changed | SQL injection in Bazarr migrator | ✅ | `SublarrWiki/en/development/architecture.md` |
| `0.13.2-beta` | Changed | XZ decompression bomb protection | ❌ | — |
| `0.13.2-beta` | Changed | Container hardening | ✅ | `SublarrWiki/getting-started/environment-variables.md` |
| `0.13.2-beta` | Changed | Dev/prod requirements split | ❌ | — |
| `0.13.2-beta` | Changed | CI | ❌ | — |
| `0.13.1-beta` | Added | Sidecar discovery APIs | ✅ | `SublarrWiki/home.md` |
| `0.13.1-beta` | Added | Sidecar delete API | ✅ | `SublarrWiki/home.md` |
| `0.13.1-beta` | Added | Trash management APIs | ✅ | `SublarrWiki/user-guide/settings/automation.md` |
| `0.13.1-beta` | Added | Batch delete API | ✅ | `SublarrWiki/user-guide/library.md` |
| `0.13.1-beta` | Added | Inline sidecar badges | ✅ | `SublarrWiki/user-guide/hi-removal.md` |
| `0.13.1-beta` | Added | Subtitle Cleanup Modal | ✅ | `SublarrWiki/home.md` |
| `0.13.1-beta` | Added | Live extraction progress | ❌ | — |
| `0.13.1-beta` | Added | Activity page visibility | ✅ | `SublarrWiki/user-guide/activity.md` |
| `0.13.1-beta` | Added | Always-visible series toolbar | ❌ | — |
| `0.13.1-beta` | Added | Auto-cleanup settings | ✅ | `SublarrWiki/home.md` |
| `0.13.1-beta` | Added | Settings UI | ✅ | `SublarrWiki/getting-started/environment-variables.md` |
| `0.13.1-beta` | Added | Wanted Batch Search card | ✅ | `SublarrWiki/home.md` |
| `0.13.1-beta` | Added | Batch Probe card | ✅ | `SublarrWiki/user-guide/activity.md` |
| `0.13.1-beta` | Added | Wanted Scanner card | ✅ | `SublarrWiki/getting-started/upgrade-guide.md` |
| `0.13.1-beta` | Changed | Subtitle badge semantics | ✅ | `SublarrWiki/home.md` |
| `0.13.1-beta` | Changed | Language code normalisation | ✅ | `SublarrWiki/development/plugin-development.md` |
| `0.13.1-beta` | Changed | SeriesDetail subtitle column | ✅ | `SublarrWiki/en/development/architecture.md` |
| `0.13.1-beta` | Changed | Sidecar query live refresh | ✅ | `SublarrWiki/home.md` |
| `0.13.1-beta` | Changed | Queue page polling | ✅ | `SublarrWiki/en/getting-started/upgrade-guide.md` |
| `0.13.1-beta` | Fixed | Batch-extract series_id 400 | ✅ | `SublarrWiki/user-guide/library.md` |
| `0.13.1-beta` | Fixed | Batch-probe deadlock | ❌ | — |
| `0.13.1-beta` | Fixed | wanted_item_searched event dropped | ❌ | — |
| `0.13.1-beta` | Fixed | Duplicate language badges | ✅ | `SublarrWiki/user-guide/translation-llm.md` |
| `0.12.3-beta` | Fixed | ZIP Slip | ❌ | — |
| `0.12.3-beta` | Fixed | Git clone SSRF/RCE | ✅ | `SublarrWiki/en/development/contributing.md` |
| `0.12.3-beta` | Fixed | Path traversal | ✅ | `SublarrWiki/en/development/architecture.md` |
| `0.12.3-beta` | Fixed | Symlink deletion bypass | ❌ | — |
| `0.12.3-beta` | Fixed | Hook env injection | ❌ | — |
| `0.12.3-beta` | Fixed | CORS wildcard Socket.IO | ❌ | — |
| `0.12.3-beta` | Changed | CI | ❌ | — |
| `0.12.3-beta` | Changed | Claude Code Review | ✅ | `SublarrWiki/user-guide/settings/prompt-templates.md` |
| `0.12.0-beta` | Added | Settings UX Redesign | ✅ | `SublarrWiki/home.md` |
| `0.12.0-beta` | Added | SettingsCard component | ❌ | — |
| `0.12.0-beta` | Added | ConnectionBadge component | ❌ | — |
| `0.12.0-beta` | Added | Advanced Settings toggle | ✅ | `SublarrWiki/getting-started/installation.md` |
| `0.12.0-beta` | Added | SettingRow descriptions | ❌ | — |
| `0.12.0-beta` | Added | InfoTooltip improvements | ❌ | — |
| `0.12.0-beta` | Added | Dirty-state Save button | ❌ | — |
| `0.12.0-beta` | Added | Navigation warning | ✅ | `SublarrWiki/user-guide/library.md` |
| `0.12.0-beta` | Added | ProvidersTab descriptions | ❌ | — |
| `0.12.0-beta` | Added | MediaServersTab & WhisperTab descriptions | ❌ | — |
| `0.12.0-beta` | Added | TranslationTab descriptions | ❌ | — |
| `0.12.0-beta` | Added | MigrationTab improvements | ❌ | — |
| `0.11.1-beta` | Added | Scan Auto-Extract | ❌ | — |
| `0.11.1-beta` | Added | Batch Extract Endpoint | ✅ | `SublarrWiki/user-guide/library.md` |
| `0.11.1-beta` | Added | Multi-Series Batch Search | ❌ | — |
| `0.11.1-beta` | Added | SeriesDetail Batch Toolbar | ✅ | `SublarrWiki/en/development/architecture.md` |
| `0.11.1-beta` | Added | Library Batch Toolbar | ✅ | `SublarrWiki/home.md` |
| `0.11.0-beta` | Added | Track Manifest | ✅ | `SublarrWiki/home.md` |
| `0.11.0-beta` | Added | Video Sync Backend | ✅ | `SublarrWiki/home.md` |
| `0.11.0-beta` | Added | Video Sync Frontend | ✅ | `SublarrWiki/home.md` |
| `0.11.0-beta` | Added | Waveform Editor | ✅ | `SublarrWiki/home.md` |
| `0.11.0-beta` | Added | Format Conversion | ✅ | `SublarrWiki/en/getting-started/faq.md` |
| `0.11.0-beta` | Added | Batch OCR Pipeline | ✅ | `SublarrWiki/user-guide/activity.md` |
| `0.11.0-beta` | Added | Quality Fixes Toolbar | ✅ | `SublarrWiki/development/plugin-development.md` |
| `0.10.0-beta` | Added | Context Window Batching | ✅ | `SublarrWiki/user-guide/translation-llm.md` |
| `0.10.0-beta` | Added | Translation Memory Cache | ✅ | `SublarrWiki/en/getting-started/faq.md` |
| `0.10.0-beta` | Added | Per-Line Quality Scoring | ✅ | `SublarrWiki/user-guide/web-player.md` |
| `0.10.0-beta` | Added | Bulk Auto-Sync | ❌ | — |
| `0.10.0-beta` | Added | Machine Translation Detection | ✅ | `SublarrWiki/content/en/development/scoring.md` |
| `0.10.0-beta` | Added | Uploader Trust Scoring | ✅ | `SublarrWiki/content/en/development/scoring.md` |
| `0.10.0-beta` | Added | AniDB Absolute Episode Order | ✅ | `SublarrWiki/en/development/architecture.md` |
| `0.10.0-beta` | Added | Whisper Fallback Threshold | ✅ | `SublarrWiki/home.md` |
| `0.10.0-beta` | Added | Tag-Based Profile Assignment | ✅ | `SublarrWiki/en/development/architecture.md` |
| `0.10.0-beta` | Added | LLM Backend Presets | ✅ | `SublarrWiki/user-guide/settings/translation-backends.md` |
| `0.9.0-beta` | Added | Gestdown | ✅ | `SublarrWiki/content/en/development/providers.md` |
| `0.9.0-beta` | Added | Podnapisi | ✅ | `SublarrWiki/content/en/development/providers.md` |
| `0.9.0-beta` | Added | Kitsunekko | ✅ | `SublarrWiki/content/en/development/providers.md` |
| `0.9.0-beta` | Added | Napisy24 | ✅ | `SublarrWiki/content/en/development/providers.md` |
| `0.9.0-beta` | Added | Whisper-Subgen | ✅ | `SublarrWiki/content/en/development/providers.md` |
| `0.9.0-beta` | Added | Titrari | ✅ | `SublarrWiki/content/en/development/providers.md` |
| `0.9.0-beta` | Added | LegendasDivx | ✅ | `SublarrWiki/content/en/development/providers.md` |
| `0.9.0-beta` | Added | DeepL | ✅ | `SublarrWiki/user-guide/translation-llm.md` |
| `0.9.0-beta` | Added | LibreTranslate | ✅ | `SublarrWiki/user-guide/translation-llm.md` |
| `0.9.0-beta` | Added | OpenAI-compatible | ✅ | `SublarrWiki/user-guide/translation-llm.md` |
| `0.9.0-beta` | Added | Google Cloud Translation | ✅ | `SublarrWiki/user-guide/translation-llm.md` |
| `0.9.0-beta` | Added | Plex | ❌ | — |
| `0.9.0-beta` | Added | Kodi | ❌ | — |
| `0.9.0-beta` | Added | faster-whisper | ✅ | `SublarrWiki/en/getting-started/upgrade-guide.md` |
| `0.9.0-beta` | Added | Subgen | ✅ | `SublarrWiki/content/en/development/providers.md` |
| `0.9.0-beta` | Added | TMDB | ❌ | — |
| `0.9.0-beta` | Added | AniList | ✅ | `SublarrWiki/getting-started/installation.md` |
| `0.9.0-beta` | Added | TVDB | ❌ | — |
| `0.9.0-beta` | Changed | Architecture: | ❌ | — |
| `0.9.0-beta` | Changed | Database: | ✅ | `SublarrWiki/en/getting-started/upgrade-guide.md` |
| `0.9.0-beta` | Changed | Frontend: | ✅ | `SublarrWiki/en/development/architecture.md` |
| `0.9.0-beta` | Changed | Translation: | ✅ | `SublarrWiki/en/development/api-reference.md` |
| `0.9.0-beta` | Changed | Settings: | ✅ | `SublarrWiki/content/en/development/scoring.md` |
| `0.9.0-beta` | Changed | Version numbering: | ✅ | `SublarrWiki/home.md` |
| `0.9.0-beta` | Changed | Gunicorn: | ❌ | — |
| `1.0.0-beta` | Added | Provider System: | ✅ | `SublarrWiki/home.md` |
| `1.0.0-beta` | Added | Wanted System: | ✅ | `SublarrWiki/home.md` |
| `1.0.0-beta` | Added | Search & Download Workflow: | ✅ | `SublarrWiki/en/development/architecture.md` |
| `1.0.0-beta` | Added | Upgrade System: | ✅ | `SublarrWiki/home.md` |
| `1.0.0-beta` | Added | Language Profiles: | ✅ | `SublarrWiki/home.md` |
| `1.0.0-beta` | Added | LLM Translation: | ❌ | — |
| `1.0.0-beta` | Added | Glossary System: | ✅ | `SublarrWiki/home.md` |
| `1.0.0-beta` | Added | Prompt Presets: | ✅ | `SublarrWiki/home.md` |
| `1.0.0-beta` | Added | Blacklist & History: | ✅ | `SublarrWiki/en/development/api-reference.md` |
| `1.0.0-beta` | Added | HI Removal: | ✅ | `SublarrWiki/user-guide/hi-removal.md` |
| `1.0.0-beta` | Added | Embedded Subtitle Detection: | ✅ | `SublarrWiki/home.md` |
| `1.0.0-beta` | Added | AniDB Integration: | ✅ | `SublarrWiki/content/en/development/providers.md` |
| `1.0.0-beta` | Added | Webhook Automation: | ✅ | `SublarrWiki/home.md` |
| `1.0.0-beta` | Added | Multi-Instance Support: | ✅ | `SublarrWiki/en/getting-started/environment-variables.md` |
| `1.0.0-beta` | Added | Notification System: | ✅ | `SublarrWiki/home.md` |
| `1.0.0-beta` | Added | Onboarding Wizard: | ✅ | `SublarrWiki/getting-started/installation.md` |
| `1.0.0-beta` | Added | Provider Caching: | ✅ | `SublarrWiki/home.md` |
| `1.0.0-beta` | Added | Re-Translation: | ❌ | — |
| `1.0.0-beta` | Added | Config Export/Import: | ✅ | `SublarrWiki/home.md` |
| `1.0.0-beta` | Added | Docker Multi-Arch: | ✅ | `SublarrWiki/home.md` |
| `1.0.0-beta` | Added | Unraid Template: | ✅ | `SublarrWiki/getting-started/installation.md` |

**CHANGELOG entries scanned:** 283
**Missing from wiki:** 68

## Git `feat:` Commits

| SHA | Commit | In Wiki |
|-----|--------|---------|
| `926c656` | fix: filter media extras from standalone scan + fix Wanted list layout | ❌ |
| `985df3b` | NFO metadata integration for standalone scanner | ✅ |
| `90a6ac9` | v0.29.0 â€” Web Player | ✅ |
| `cea9d05` | v0.28.0 â€” AI Glossary Builder (#81) | ✅ |
| `964e8de` | v0.27.0 â€” Subtitle NFO Export (#80) | ✅ |
| `8c0b1ac` | v0.26.0 â€” Single-Account Login (#79) | ✅ |
| `de75613` | v0.25.3 â€” List Virtualization (#78) | ✅ |
| `dfb47fa` | v0.25.2 â€” Subtitle Diff Viewer (per-cue accept/reject) (#77) | ✅ |
| `bcad901` | v0.25.1 â€” CLI Mode (#76) | ✅ |
| `8fdc86b` | v0.25.0 â€” Jellyfin Play-Start Auto-Translate (#75) | ❌ |
| `70390cc` | v0.24.4 â€” Chapter-Aware Sync (#74) | ❌ |
| `dd89e45` | v0.24.3 â€” Fansub Preference Rules (#73) | ✅ |
| `1e50648` | v0.24.2 â€” Multi-Audio Track Support for Whisper (#72) | ❌ |
| `a52f318` | v0.24.1 â€” OP/ED Detection | ❌ |
| `61f1b86` | v0.24.0 â€” Staff Credit Filtering (#70) | ❌ |
| `fbf2b1b` | v0.23.0 â€” Batch Operations & Smart Filter Presets (#69) | ❌ |
| `eb9116e` | provider ecosystem v0.22.0 â€” GitHub marketplace, SHA256 integrity, c | ✅ |
| `b6802bc` | v0.21.1 UI/UX polish & accessibility (#67) | ❌ |
| `3eee1b8` | subtitle export API + series ZIP batch download (#66) | ✅ |
| `530785c` | Redis/RQ job queue support with worker entry point (#64) | ❌ |
| `a1d3040` | configurable translation worker count + unify translate route through  | ✅ |
| `d70e176` | background wanted scanner â€” batch commits + scan yield (#62) | ✅ |
| `d4526c6` | PostgreSQL first-class support (#60) | ✅ |
| `f5e89bb` | stream removal / safe remux with trash-folder backups and undo (#57) | ✅ |
| `745146e` | anti-captcha integration for provider 403 bypass (#56) | ✅ |
| `8929ba2` | TRaSH-compatible scoring presets (#55) | ❌ |
| `612c68a` | HI and forced subtitle preference scoring (#54) | ❌ |
| `fcf82db` | wire subtitle_downloaded event for post-processing hooks (#53) | ❌ |
| `8de4102` | translation quality dashboard in Statistics page (#52) | ✅ |
| `010fa5b` | subtitle upgrade candidate scheduler (#51) (#51) | ✅ |
| `69fb280` | provider result re-ranking based on download history (#50) (#50) | ✅ |
| `178e6a4` | release group filtering for provider search results (#49) | ✅ |
| `098c054` | smart episode matching â€” multi-episode, OVA/special detection, video | ❌ |
| `fb6305a` | SHA-256 duplicate detection at download time (#45) | ✅ |
| `5632949` | redesign Activity/History pages with parsed media titles and blacklist | ❌ |
| `4935af7` | show update available badge in sidebar when newer GitHub release exist | ❌ |
| `7236f19` | extraction redesign â€” "extracted" status + sidecar cleanup | ✅ |
| `8783cce` | phase5 â€” settings search + statistics empty state + download stats r | ❌ |
| `9904eae` | phase4 â€” library grid view + status/profile filters | ❌ |
| `e7ba99d` | phase3 â€” wanted transparency (error reason + retry_after display) | ❌ |
| `1fb1a3f` | phase2 â€” EpisodeActionMenu replaces 8 icon-only action buttons | ❌ |
| `6d37838` | provider UI redesign + 4 new providers + language expansion (#29) | ✅ |
| `dd808b0` | Tasks page â€” 5 new tasks, cancel button, progress bars (#25) | ✅ |
| `4b7fb85` | queue visibility â€” batch probe + wanted scanner status cards (#22) | ✅ |
| `5bcb275` | subtitle sidecar management + live extraction progress (v0.13.0-beta)  | ✅ |
| `b509eca` | batch-probe + batch-extract async pipeline for embedded subtitles | ❌ |
| `3d63c7c` | batch-probe extracts all embedded subtitle tracks (language-agnostic) | ❌ |
| `8436e04` | batch metadata pre-scan for embedded subtitle detection | ✅ |
| `2919a4d` | Merge pull request #16 from Abrechen2/feat/batch-extract-async | ✅ |
| `e89e503` | non-blocking batch-extract with background thread and progress trackin | ❌ |
| `d52c9da` | expose 35 settings in UI that were previously env-only (#14) | ✅ |
| `9aba910` | Revert "feat: expose 35 settings in UI that were previously env-only" | ✅ |
| `c29c876` | expose 35 settings in UI that were previously env-only | ✅ |
| `50890dd` | add anime-translator-v6 community backend template | ❌ |
| `70e6b1b` | *arr-style UI redesign (Sonarr/Radarr aesthetic) | ❌ |
| `10970a8` | provider tile grid with edit modal (Sonarr-style) | ✅ |
| `b6a8640` | Settings UX redesign â€” card groupings, descriptions, Advanced toggle | ✅ |
| `1c88540` | ProvidersTab â€” add description texts to all credential fields | ❌ |
| `c73a6cc` | MediaServersTab + WhisperTab â€” add description texts to all fields | ❌ |
| `0531587` | TranslationTab backend descriptions + PromptPresets variable docs; fix | ❌ |
| `ccb66f5` | General tab card groupings â€” Server, Sicherheit, Protokollierung, Pf | ✅ |
| `60e421d` | isDirty save button, Advanced toggle in header, useBlocker navigation  | ❌ |
| `e9369a0` | InfoTooltip 15px icon + ESC close + accent hover; extend FieldConfig w | ❌ |
| `fae7f10` | AdvancedSettingsContext + extended SettingRow (description, advanced p | ❌ |
| `1b2ca29` | add SettingsCard and ConnectionBadge shared components | ❌ |
| `7376647` | Library series checkboxes + batch toolbar; bump to 0.11.1-beta | ✅ |
| `5d39cf1` | add episode checkboxes and batch toolbar to SeriesDetail | ❌ |
| `58433a3` | add batchExtractEmbedded and startSeriesBatchSearch API client functio | ❌ |
| `f92c84a` | add wanted_auto_extract/wanted_auto_translate settings UI in AdvancedT | ❌ |
| `3e3eca3` | batch-search accepts series_ids array for multi-series processing | ✅ |
| `ae4cfec` | add POST /wanted/batch-extract endpoint for multi-item embedded sub ex | ❌ |
| `fc4f0aa` | auto-extract embedded subs on scan when wanted_auto_extract=True | ✅ |
| `08dd576` | add scan_auto_extract and scan_auto_translate settings | ❌ |
| `167eaaf` | add compose-based deploy with version from backend/VERSION | ❌ |
| `295e94a` | Implement Safety Roadmap S1-S8 (error handling, circuit breaker, backu | ✅ |
| `403216f` | Enhance setup and testing documentation, improve provider error handli | ❌ |
| `a1c4de7` | Add AniDB mapping, HI removal, notifications, upgrade scoring, and new | ❌ |
| `c777af3` | Add SubDL provider, language profiles, and remove Bazarr (Milestone 6) | ✅ |
| `aed1688` | Add upgrade system, automation, and notifications (Milestone 5) | ❌ |
| `9bc55dc` | Add wanted system, search workflow, and provider UI (Milestones 2-4) | ❌ |
| `aa3ea90` | Add subtitle provider system â€” replace Bazarr dependency (Milestone  | ❌ |
| `ae9d8c4` | Polish UI components, enhance backend robustness and add new pages | ✅ |
| `0192d55` | Enhance Bazarr and Sonarr integration with path mapping and episode fi | ❌ |
| `7ac1e90` | Sublarr rebranding - complete rewrite with arr-style UI | ✅ |
| `ced0a03` | AnimeTranslator v2 â€” vollstaendige Implementierung | ❌ |

## Missing Features (CHANGELOG only)

| Version | Type | Feature |
|---------|------|---------|
| `0.25.1-beta` | Added | CLI |
| `0.25.1-beta` | Added | CLI |
| `0.25.1-beta` | Added | CLI |
| `0.25.1-beta` | Added | CLI |
| `0.25.1-beta` | Added | CLI |
| `0.25.0-beta` | Added | `JellyfinEmbyServer.get_item_path_by_id()` |
| `0.25.0-beta` | Added | `MediaServerManager.get_item_path_from_jellyfin()` |
| `0.24.4-beta` | Added | ChapterCache |
| `0.24.4-beta` | Added | `backend/chapters.py` |
| `0.24.4-beta` | Added | `GET /api/v1/tools/chapters?video_path=…` |
| `0.24.4-beta` | Added | SyncControls |
| `0.24.3-beta` | Added | FansubPreference |
| `0.24.3-beta` | Added | `GET/PUT/DELETE /api/v1/series/<id>/fansub-prefs` |
| `0.24.3-beta` | Added | SeriesFansubPrefsPanel |
| `0.24.2-beta` | Added | SeriesSettings |
| `0.24.2-beta` | Added | `GET/PUT /api/v1/series/<id>/audio-track-pref` |
| `0.24.2-beta` | Added | WhisperQueue |
| `0.24.2-beta` | Added | SeriesAudioTrackPicker |
| `0.22.0-beta` | Added | SSRF Prevention |
| `0.21.1-beta` | Added | Page-Specific Skeletons |
| `0.21.1-beta` | Added | `prefers-reduced-motion` |
| `0.21.1-beta` | Changed | Stagger Animation |
| `0.21.1-beta` | Changed | CSS Hover |
| `0.19.1-beta` | Fixed | Dockerfile |
| `0.19.0-beta` | Added | Trash-Folder Backups |
| `0.19.0-beta` | Added | Undo / Restore |
| `0.18.0-beta` | Added | HI Support |
| `0.17.0-beta` | Added | Smart Episode Matching |
| `0.15.1-beta` | Fixed | App |
| `0.15.1-beta` | Fixed | App |
| `0.14.1-beta` | Fixed | i18n |
| `0.14.1-beta` | Fixed | i18n |
| `0.14.1-beta` | Fixed | i18n |
| `0.14.0-beta` | Added | LanguageSelect component |
| `0.14.0-beta` | Changed | CI |
| `0.13.2-beta` | Changed | Hook script path restriction |
| `0.13.2-beta` | Changed | XZ decompression bomb protection |
| `0.13.2-beta` | Changed | Dev/prod requirements split |
| `0.13.2-beta` | Changed | CI |
| `0.13.1-beta` | Added | Live extraction progress |
| `0.13.1-beta` | Added | Always-visible series toolbar |
| `0.13.1-beta` | Fixed | Batch-probe deadlock |
| `0.13.1-beta` | Fixed | wanted_item_searched event dropped |
| `0.12.3-beta` | Fixed | ZIP Slip |
| `0.12.3-beta` | Fixed | Symlink deletion bypass |
| `0.12.3-beta` | Fixed | Hook env injection |
| `0.12.3-beta` | Fixed | CORS wildcard Socket.IO |
| `0.12.3-beta` | Changed | CI |
| `0.12.0-beta` | Added | SettingsCard component |
| `0.12.0-beta` | Added | ConnectionBadge component |
| `0.12.0-beta` | Added | SettingRow descriptions |
| `0.12.0-beta` | Added | InfoTooltip improvements |
| `0.12.0-beta` | Added | Dirty-state Save button |
| `0.12.0-beta` | Added | ProvidersTab descriptions |
| `0.12.0-beta` | Added | MediaServersTab & WhisperTab descriptions |
| `0.12.0-beta` | Added | TranslationTab descriptions |
| `0.12.0-beta` | Added | MigrationTab improvements |
| `0.11.1-beta` | Added | Scan Auto-Extract |
| `0.11.1-beta` | Added | Multi-Series Batch Search |
| `0.10.0-beta` | Added | Bulk Auto-Sync |
| `0.9.0-beta` | Added | Plex |
| `0.9.0-beta` | Added | Kodi |
| `0.9.0-beta` | Added | TMDB |
| `0.9.0-beta` | Added | TVDB |
| `0.9.0-beta` | Changed | Architecture: |
| `0.9.0-beta` | Changed | Gunicorn: |
| `1.0.0-beta` | Added | LLM Translation: |
| `1.0.0-beta` | Added | Re-Translation: |
