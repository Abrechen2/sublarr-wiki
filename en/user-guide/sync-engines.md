---
title: Multi-Engine Sync
description: Subtitle-to-video alignment with fallback chain across sync engines
published: true
date: 2026-04-20
---

# Multi-Engine Sync

As of v0.70.0-beta, Sublarr aligns subtitles to video through a **multi-engine
orchestrator** rather than a single CLI call. Engines run in a configured
order; the orchestrator early-exits on the first sane success and writes
every attempt to an audit table.

## Available Engines

| Engine | Installed by default | What it does |
|--------|----------------------|--------------|
| `ffsubsync` | ✅ bundled in the Docker image | Speech-detection-based sync against the video's audio track |
| `alass` | ❌ needs the `alass` binary on the host | Reference-subtitle-based sync (uses another subtitle as the time base) |

Check live status at **Settings → Sync Engines** or via:

```bash
curl -s -H "X-API-Key: $SUBLARR_API_KEY" \
  http://<host>:5765/api/v1/sync/engines
```

The endpoint returns each engine's `name`, `available` flag (is the binary
installed?), configured `timeout_s`, and the global `sanity_threshold_ms`
(default 60 000 ms = 60 s).

## Fallback Chain

The `SyncOrchestrator` runs engines in order. An engine result is **rejected
and the next engine is tried** when:

1. `is_available()` returns `False` — the binary isn't installed
2. `engine.sync()` raises an exception
3. The returned offset exceeds the `sanity_threshold_ms` — an obvious
   mis-sync result is treated as a failure

If all engines fail, the subtitle is saved without sync and a `WARNING` is
logged. The `sublarr_sync_all_failed_total` Prometheus counter increments so
operators can alert on chronic failures.

## Audit Trail — `sync_job_runs`

Every engine attempt (successful or not) writes a row to the
`sync_job_runs` table:

| Column | Meaning |
|--------|---------|
| `engine` | Engine name (`ffsubsync`, `alass`, …) |
| `status` | `ok`, `error`, `insanity_reject`, `skipped`, `failure` |
| `offset_ms` | Detected shift in milliseconds (NULL when engine didn't complete) |
| `duration_ms` | Time spent in that engine |
| `subtitle_path` / `video_path` | Inputs |
| `reason` | Short error description (64 char max) |
| `created_at` | Timestamp |

Query recent runs:

```bash
curl -s -H "X-API-Key: $SUBLARR_API_KEY" \
  "http://<host>:5765/api/v1/sync/runs?limit=50"
```

## Installing `alass` (optional)

Download the static binary from [alass releases](https://github.com/kaegi/alass/releases)
and mount it into the container's PATH, e.g. in `docker-compose.yml`:

```yaml
services:
  sublarr:
    volumes:
      - /path/to/alass:/usr/local/bin/alass:ro
```

After a container restart, `GET /api/v1/sync/engines` will show `alass:
available=true`.

## Relation to Post-Processing

Successful sync fires the `after_sync` trigger of the
[post-processing pipeline](/user-guide/post-processing) — you can chain a
Plex/Emby/Jellyfin refresh or a webhook notification right after the
subtitle lands.

## Future Engines

The orchestrator is deliberately engine-agnostic. Future additions
(NanoSync, LLM-assisted sync) drop in as new files under
`backend/services/sync_engines/` and are appended to the chain — no
orchestrator changes needed.
