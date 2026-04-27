---
title: Settings — Scheduler & Automation
description: APScheduler profile, wanted-search pacing, and the persistent Subtitle Automation queue
published: true
date: 2026-04-27
---

# Settings — Scheduler & Automation

This page documents the knobs that control **when** Sublarr does work — the scheduler profile that picks intervals for all recurring jobs, the wanted-search pacing strategy that keeps premium series ahead of backlog, and the persistent Subtitle Automation queue that drains pending embedded-track extractions in the background.

> [!TIP]
> Settings UI lives at **Settings → System → Scheduler** (job list, run-now, history) and **Settings → Wanted → Scheduling** (pacing). The fields below are the underlying config — both pages write to the same `SUBLARR_*` env-var-equivalent values.

## Scheduler Profile *(v0.55.0-beta)*

A single preset that maps to a complete set of recurring-job intervals (wanted scan, wanted search, cleanup, AniDB sync, scheduler-history cleanup). Use the preset for 95 % of installs; switch to `custom` only when you have a specific reason to deviate from a preset interval.

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Scheduler Profile | `balanced` | `SUBLARR_SCHEDULER_PROFILE` | One of: `light`, `balanced`, `aggressive`, `custom`. `light` ticks once per hour; `balanced` is the default cadence shipped with Sublarr; `aggressive` ticks every 15 min for premium-heavy libraries; `custom` lets you edit each job interval individually under **Settings → System → Scheduler**. |
| Scheduler History Retention (days) | `30` | `SUBLARR_SCHEDULER_HISTORY_RETENTION_DAYS` | How many days of `scheduler_job_runs` rows are kept before the `scheduler_history_cleanup` job deletes them. Values below 7 hide most diagnostic context; values above 90 grow the table noticeably. |

## Wanted-Search Pacing *(v1 budget scheduler — v0.55.0-beta)*

Controls how the wanted-search tick decides which items to attempt. The defaults give a fair rotation that always reserves capacity for premium and standard items even when backlog is overflowing.

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Search Order | `fair` | `SUBLARR_WANTED_SEARCH_ORDER` | Item ordering inside a tick: `fair` rotates oldest-searched first; `newest_first` services freshly added items immediately; `weighted` mixes both, weighted by language-profile priority. |
| Priority Weighting Enabled | `true` | `SUBLARR_WANTED_SCHEDULER_PRIORITY_WEIGHTING_ENABLED` | When `true`, the search ORDER BY prepends a priority rank (premium=0, standard=1, backlog=2) so premium items always win the first slice of the per-tick budget. Disable only when you want pure FIFO scheduling. |
| Backlog Reserve (%) | `50` | `SUBLARR_WANTED_SCHEDULER_BACKLOG_RESERVE_PCT` | Day-budget spent percentage above which backlog-priority items are deferred to the next tick. Ensures premium+standard always get a fair slice of the daily quota. |

> [!NOTE]
> The combined effect: in a typical tick, premium items run first; backlog items run only while the day-budget is below `backlog_reserve_pct`. Once the budget passes that threshold, only premium and standard items are searched until the next UTC day rolls over.

## Subtitle Automation Queue *(v0.71.0-beta)*

Persistent queue for the post-scan automation pipeline (embedded-track extraction, SDH penalty, foreign-track cleanup). Items enter the queue when the wanted-scanner detects an embedded subtitle that satisfies the language profile; a drain worker processes them in the background so the scan itself stays fast.

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Subtitle Automation Enabled | `false` | `SUBLARR_SUBTITLE_AUTOMATION_ENABLED` | Master gate. When `false`, embedded subtitles are handled exactly as before: scanned synchronously inside the wanted-scanner tick. Turn on to route them through the persistent queue + drain worker pipeline. |
| Queue Enabled | `true` | `SUBLARR_SUBTITLE_AUTOMATION_QUEUE_ENABLED` | Drain-worker switch. Only relevant when automation is enabled. When `false`, items are written to the queue but never drained — useful for diagnostics or maintenance windows. |
| Drain Interval (minutes) | `2` | `SUBLARR_SUBTITLE_AUTOMATION_DRAIN_INTERVAL_MINUTES` | Scheduler cadence for the drain worker. Each tick processes a batch of pending entries; failures are retried with exponential backoff. Lower values speed up new-item turnaround at the cost of more wakeups. |

> [!TIP]
> The queue dashboard lives at **Settings → Subtitle Automation**. It shows pending / running / done / failed counts and the last drain timestamp; failed entries can be retried or dropped from there.
