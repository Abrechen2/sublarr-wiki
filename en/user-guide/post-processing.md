---
title: Post-Processing
description: Execute a shell command after every subtitle download
published: true
date: 2026-04-03
---

# Post-Processing

Sublarr can run a shell command automatically after every successful subtitle
download. This lets you notify Plex, rename files, or trigger any automation
without requiring a plugin.

> **Disabled by default.** Post-processing must be explicitly enabled in
> Settings → Automation before the command is executed.

## Enable Post-Processing

1. Go to **Settings → Automation**
2. Toggle **Post-Processing** on
3. Enter your command in the **Post-Download Command** field
4. Click **Save**

## Available Variables

Variables are substituted into the command string before execution.

| Variable | Example value | Description |
|---|---|---|
| `{subtitle_path}` | `/media/anime/Naruto.srt` | Absolute path to the saved subtitle file |
| `{path}` | `/media/anime/Naruto.srt` | Alias for `{subtitle_path}` (Bazarr compatibility) |
| `{language}` | `de` | ISO 639-1 language code |
| `{provider}` | `jimaku` | Provider name that supplied the subtitle |
| `{score}` | `93` | Integer match score (0–100) |
| `{media_type}` | `series` | `series`, `movie`, or empty string |
| `{video_path}` | _(empty)_ | Reserved — always empty in current release |

## Examples

**Notify Plex after download:**
```bash
curl -s "http://plex:32400/library/sections/1/refresh?X-Plex-Token=TOKEN" \
  -o /dev/null
```

**Write a log line:**
```bash
/usr/local/bin/log-subtitle.sh {subtitle_path} {language} {provider}
```

**Discord webhook on download:**
```bash
curl -s -X POST https://discord.com/api/webhooks/YOUR_ID/YOUR_TOKEN \
  -H "Content-Type: application/json" \
  -d '{"content":"Subtitle downloaded: {subtitle_path} ({language}) via {provider}"}'
```

> Note: The command is tokenised with `shlex.split` — quote paths that may
> contain spaces, or pass them through a wrapper script.

## Behavior & Limits

- **Timeout:** 60 seconds. Commands exceeding this are killed; Sublarr logs a warning and continues.
- **Non-blocking errors:** A failing command (non-zero exit, crash, or timeout) is logged as a warning. It never blocks or retries the download pipeline.
- **No shell expansion:** The command runs with `shell=False`. Shell features like `&&`, `|`, `$VAR`, or glob patterns are not available. Use a wrapper script for complex logic.
- **Execution context:** The command runs as the same user Sublarr runs as (container: `sublarr` user, default UID 1000). Ensure the command and any target paths are accessible to that user.

## Troubleshooting

**Command does not execute**
- Confirm Post-Processing is toggled **on** in Settings → Automation.
- Check that the `Post-Download Command` field is not empty.

**"invalid shell syntax" in logs**
- Sublarr uses `shlex.split` to tokenise the command. Unmatched quotes or
  unsupported shell syntax causes this error. Test your command with
  `python3 -c "import shlex; print(shlex.split('YOUR COMMAND'))"`.

**Timeout warning in logs**
- Your command exceeds 60 seconds. Move long-running work to a background
  job and have the post-processing command only trigger it.

---

## Curated-Ops Pipeline (v0.69.0-beta+)

As of v0.69.0-beta a new **curated-ops pipeline** runs alongside the legacy
single-command mechanism above. It fires on three triggers and ships with
eight built-in ops. Configure under **Settings → Post-Processing**.

### Triggers

| Trigger | Fires when |
|---------|------------|
| `after_download` | A subtitle has been downloaded + repaired + saved |
| `after_translate` | A subtitle translation pass has finished writing output |
| `after_sync` | `ffsubsync` / `alass` finished aligning a subtitle to the video |

Each trigger has its own ordered list of op_ids. Ops run sequentially on a
dedicated 2-worker thread pool so request handlers are never blocked.

### Built-In Ops (8)

**Text ops** — fix the file on disk:

- `strip_html` — remove `<i>`, `<b>`, `<font>`, `<br>` tags
- `remove_bom` — strip UTF-8 BOM from file start
- `convert_encoding` — re-encode to UTF-8 (auto-detects source via chardet)

**HTTP ops** — notify other services:

- `webhook` — POST to a URL with `{subtitle_path}` / `{video_path}` / `{lang}` / `{score}` substitution, SSRF-protected via `validate_service_url` (blocks `file://`, metadata IPs, link-local)
- `discord_notify` — send a Discord webhook message

**Media server refresh**:

- `plex_refresh` — trigger a Plex library scan via `X-Plex-Token`
- `emby_refresh` — trigger an Emby scan
- `jellyfin_refresh` — trigger a Jellyfin scan

### Shell Escape Hatch (opt-in)

Set the environment variable `SUBLARR_ALLOW_SHELL_SCRIPTS=true` in your
`docker-compose.yml` to enable a shell-script op. It uses `shlex.quote` for
every substituted value, `subprocess.run(shell=False, args=shlex.split(…))`,
a 30-second timeout, and a PATH-only restricted env. stdout + stderr are
captured to the `post_processing_runs` audit table. This path exists for
operators who explicitly want it; the curated ops above cover the 90% case
without the security surface.

### Audit Trail

Every pipeline run writes a row to `post_processing_runs`:

- `trigger` — which trigger fired
- `ops_executed` — JSON list with per-op `{op_id, ok, duration_ms, message}`
- `duration_ms` — total pipeline time
- `outcome` — `ok` / `partial_failure` / `failure`
- `created_at` — timestamp

Query recent runs via `GET /api/v1/post-processing/runs?limit=50` or view
them in the Settings → Post-Processing tab's run history.
