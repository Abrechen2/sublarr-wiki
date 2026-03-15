---
title: Activity
description: Translation jobs, download history, active tasks
published: true
date: 2026-03-14T19:03:32.427Z
tags: 
editor: markdown
dateCreated: 2026-03-14T18:04:47.601Z
---

# Activity

![Activity — Translation Jobs & History](/img/screen-activity.png)

The Activity section gives you a real-time and historical view of all background operations: subtitle downloads, translation jobs, webhook events, and scheduled scanner runs. It is split into two main tabs — **Translation Jobs** and **Download History** — plus a **Tasks** page for the scheduler.

## Translation Jobs Tab

The Translation Jobs tab lists all LLM translation jobs — active, pending, completed, and failed.

### Job List Columns

| Column | Description |
|--------|-------------|
| **Series / Title** | The series name and episode (or movie title) being translated |
| **Episode** | Season/episode identifier (e.g. `S02E05`) |
| **Source → Target** | Language pair (e.g. `en → de`) |
| **Backend** | Translation backend used (e.g. `Ollama — anime-translator-v6`) |
| **Progress** | Lines translated out of total (e.g. `342 / 680`) |
| **Status** | `queued`, `running`, `done`, `failed` |
| **Started** | Timestamp when the job began |
| **Duration** | Elapsed time (for completed jobs: total time taken) |

### Job Status Meanings

| Status | Color | Meaning |
|--------|-------|---------|
| `queued` | Gray | Waiting for a worker thread to become available |
| `running` | Blue (pulsing) | Actively being translated — progress bar updates in real time |
| `done` | Green | Translation complete, subtitle file written to disk |
| `failed` | Red | Translation failed — click to expand error details |

### Cancelling a Running Job

1. Find the active job in the list (status: `running` or `queued`)
2. Click the **Cancel** button (X icon) in the Actions column
3. The job is stopped and marked as `cancelled`
4. The partial subtitle file is discarded — no changes are written to disk

> **Note:** Cancellation is asynchronous. A running job completes the current batch before stopping, which may take a few seconds.

### Retrying Failed Jobs

1. Click on a job with status `failed` to expand the error details
2. Review the error message (e.g. Ollama timeout, model not found, malformed response)
3. Click **Retry** to re-queue the job from the beginning
4. If the failure is due to a configuration issue (e.g. wrong model name), fix the configuration in [Settings → Translation](/user-guide/settings/translation) before retrying

> **Note:** Retried jobs are added to the end of the queue. If you have many queued jobs, the retry may wait before starting.

### Job Detail View

Click any job row to open the job detail panel, which shows:

- Full subtitle file path being translated
- Model name and version
- All configuration parameters used (batch size, temperature, timeout)
- Error trace for failed jobs
- Per-batch timing breakdown for completed jobs

## Download History Tab

The Download History tab records every subtitle download that Sublarr has performed.

### History Columns

| Column | Description |
|--------|-------------|
| **Series / Title** | Series or movie name |
| **Episode** | S01E01-style identifier |
| **Provider** | Which provider the subtitle was fetched from |
| **Score** | The scoring system score for this subtitle (higher is better) |
| **Format** | ASS or SRT |
| **Language** | Language code (ISO 639-1) |
| **Timestamp** | Date and time of download |
| **Source** | `webhook`, `manual`, `batch`, or `scheduler` — what triggered the download |

### Filtering History

Use the filter bar to narrow results:

- **Date range** — pick from/to dates
- **Provider** — filter by a specific provider
- **Status** — All, Success, Failed
- **Series** — type to search series names

### Webhook Events

In addition to download records, the Download History tab also logs incoming Sonarr and Radarr webhook events. Each event shows the payload type (`on_download`, `on_upgrade`), triggering host, and the resulting Sublarr action.

## Tasks Page

The Tasks page (`/tasks` in the UI) gives you visibility into all background scheduler jobs — the periodic automated processes that Sublarr runs independently of user interaction.

### What the Tasks Page Shows

| Column | Description |
|--------|-------------|
| **Task name** | Internal name of the scheduled task |
| **Description** | Human-readable purpose |
| **Interval** | How often it runs (e.g. every 6 hours) |
| **Last run** | When it last executed |
| **Next run** | When it is scheduled to run next |
| **Status** | Enabled / Disabled / Currently running |
| **Progress** | Progress bar for long-running operations |

### Available Scheduled Tasks

| Task | Default Interval | Description |
|------|-----------------|-------------|
| **Wanted Scanner** | 6 hours | Scans library for items missing target-language subtitles |
| **Wanted Search** | 24 hours | Runs provider searches for all items in the Wanted list |
| **Upgrade Scanner** | 24 hours | Looks for better-quality subtitles to replace existing ones |
| **Cache Cleanup** | 24 hours | Purges expired provider search cache and database records |
| **Backup Scheduler** | Daily | Creates an automatic database backup |

### Manual Task Controls

- **Trigger** (play icon) — runs the task immediately, outside its normal schedule. Useful for testing configuration changes or forcing an immediate scan after adding a new series.
- **Cancel** (X icon) — stops a currently running task. Only available when a task is actively running.

> **Note:** Triggering a task manually does not reset its normal schedule — it still runs at the next scheduled time after the manual run.

## Real-Time Updates

All Activity pages update in real time via WebSocket (Socket.IO). You do not need to refresh the page — new jobs, progress updates, and completions appear automatically.

If the WebSocket connection is lost (indicated by an orange indicator in the top bar), the page falls back to polling every 10 seconds until reconnected.
