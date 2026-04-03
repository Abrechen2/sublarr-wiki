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
