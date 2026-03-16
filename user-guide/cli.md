---
title: Command-Line Interface
description: Sublarr CLI reference — search, translate, sync, and status commands
published: true
date: 2026-03-16
tags: user-guide
editor: markdown
dateCreated: 2026-03-16
---

# Command-Line Interface

The `sublarr` CLI lets you trigger searches, translations, and syncs from a terminal or script without opening the web UI. It communicates with a running Sublarr instance via the REST API. The CLI was introduced in **v0.25.1-beta** and is bundled with the Docker image — no separate installation is required.

## Setup

The CLI entry point is `backend/sublarr_cli.py` inside the container. Run it via:

```bash
docker exec sublarr python /app/backend/sublarr_cli.py [command]
```

For convenience, set up a host-side alias if you prefer running it from outside the container:

```bash
alias sublarr='docker exec sublarr python /app/backend/sublarr_cli.py'
```

**Connection settings** — configure via environment variables or per-command flags:

| Option | Env Variable | Default | Description |
|--------|-------------|---------|-------------|
| `--url` | `SUBLARR_URL` | `http://localhost:5765` | Base URL of the Sublarr instance |
| `--api-key` | `SUBLARR_API_KEY` | *(empty)* | API key if authentication is enabled |

Example with env vars:

```bash
export SUBLARR_URL=http://192.168.1.100:5765
export SUBLARR_API_KEY=your-api-key
docker exec sublarr python /app/backend/sublarr_cli.py status
```

## Commands

### `sublarr search`

Search subtitle providers for all wanted items in a series.

```bash
sublarr search --series-id <id>
```

| Flag | Required | Description |
|------|----------|-------------|
| `--series-id` | Yes | Sonarr/standalone series ID to search |

Calls `GET /api/v1/wanted?series_id=<id>` to retrieve the wanted list for the series, then sends a `POST /api/v1/wanted/batch-search` request. Output shows each item and the result (found/not found).

**Example:**

```bash
docker exec sublarr python /app/backend/sublarr_cli.py search --series-id 42
# Searching 12 wanted items for series 42...
# S01E01 — found: AnimeTosho (score: 580)
# S01E02 — not found
```

---

### `sublarr translate`

Translate a subtitle file using the configured LLM backend.

```bash
sublarr translate --path <subtitle_file> [--force]
```

| Flag | Required | Description |
|------|----------|-------------|
| `--path` | Yes | Path to the `.srt` or `.ass` file to translate (inside the container) |
| `--force` | No | Re-translate even if a translated version already exists |

Calls `POST /api/v1/translate/sync`. For short files, translation completes synchronously and the output path is printed. For large files, a job ID is returned and translation continues in the background — use `sublarr status` to monitor progress.

**Example:**

```bash
docker exec sublarr python /app/backend/sublarr_cli.py translate \
  --path /media/anime/Show.S01E01.de.ass
# Translating: /media/anime/Show.S01E01.de.ass
# Output: /media/anime/Show.S01E01.de.trans.ass
```

With `--force` to overwrite an existing translation:

```bash
docker exec sublarr python /app/backend/sublarr_cli.py translate \
  --path /media/anime/Show.S01E01.de.ass --force
```

---

### `sublarr sync`

Sync subtitle timing to a video file.

```bash
sublarr sync --subtitle <path> --video <path> [--engine ffsubsync|alass]
```

| Flag | Required | Description |
|------|----------|-------------|
| `--subtitle` | Yes | Path to the subtitle file |
| `--video` | Yes | Path to the reference video file |
| `--engine` | No | Sync engine: `ffsubsync` (default) or `alass` |

Calls `POST /api/v1/tools/auto-sync`. The synced subtitle replaces the input file in-place; the original is backed up to a `.bak` file alongside it.

**Example:**

```bash
docker exec sublarr python /app/backend/sublarr_cli.py sync \
  --subtitle /media/anime/Show.S01E01.de.ass \
  --video /media/anime/Show.S01E01.mkv \
  --engine alass
# Syncing with alass...
# Done. Offset: +1.2s applied to 324 events.
```

See [Video Sync](/user-guide/video-sync) for details on choosing between `ffsubsync` and `alass`.

---

### `sublarr status`

Show active translation jobs and background task state.

```bash
sublarr status [--running]
```

| Flag | Required | Description |
|------|----------|-------------|
| `--running` | No | Show only in-progress jobs (filter out queued and completed) |

**Example output:**

```
Translation Jobs:
  [IN_PROGRESS] job_abc123 — Show.S01E01.de.ass (started 2m ago)
  [QUEUED]      job_def456 — Show.S01E02.de.ass

Background Tasks:
  wanted_scan    last_run: 2026-03-16 14:30 | next_run: 2026-03-16 20:30
  wanted_search  last_run: 2026-03-16 12:00 | next_run: 2026-03-17 12:00
```

Use `--running` to check whether a translation is actively in progress before queuing more work:

```bash
docker exec sublarr python /app/backend/sublarr_cli.py status --running
```

---

## Automation Examples

### Batch translate a full season

```bash
for ep in /media/anime/Show/Season\ 01/*.ass; do
  docker exec sublarr python /app/backend/sublarr_cli.py translate --path "$ep"
done
```

### Search, then translate after download

```bash
# 1. Trigger a batch search for series 42
docker exec sublarr python /app/backend/sublarr_cli.py search --series-id 42

# 2. After subtitles are downloaded, translate them
for ep in /media/anime/Show/Season\ 01/*.de.ass; do
  docker exec sublarr python /app/backend/sublarr_cli.py translate --path "$ep"
done
```

### Monitor active jobs in a loop

```bash
watch -n 5 'docker exec sublarr python /app/backend/sublarr_cli.py status --running'
```

### Sync all subtitles in a directory

```bash
for ep in /media/anime/Show/Season\ 01/; do
  base="${ep%.ass}"
  video="${base}.mkv"
  if [ -f "$video" ]; then
    docker exec sublarr python /app/backend/sublarr_cli.py sync \
      --subtitle "$ep" --video "$video" --engine alass
  fi
done
```
