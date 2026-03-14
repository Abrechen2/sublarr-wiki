---
title: Activity
description: Translation jobs, download history, and background task monitoring
published: true
date: 2026-03-14
---

# Activity

The Activity section shows all background operations: subtitle downloads, translation jobs, webhook events, and scheduled scanner runs.

## Translation Jobs

Lists all active and completed translation jobs with:
- Source and target language
- Progress (lines translated / total)
- Model used (e.g. `anime-translator-v6`)
- Status: `queued`, `running`, `done`, `failed`

## Tasks

The Tasks page (`/tasks` in the UI) gives you visibility into all background scheduler jobs.

**What it shows:**
- All scheduled tasks with name, description, interval, last run time, and next scheduled run
- Current status: enabled/disabled, currently running indicator
- Per-task progress bars for long-running operations

**Manual controls:**
- **Cancel** — stop a currently running task
- **Trigger** — run a task immediately outside its schedule

Available tasks include the wanted scanner, wanted search, upgrade scanner, cache cleanup, and backup scheduler.
