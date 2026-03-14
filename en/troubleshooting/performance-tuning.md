---
title: Performance Tuning
description: Optimizing Sublarr for large libraries and high translation throughput
published: true
date: 2026-03-14
---

# Performance Tuning

Sublarr performs well out of the box, but there are several knobs available for large libraries or resource-constrained environments.

---

## LLM Translation Speed

### Use GPU Acceleration

If you have a GPU, ensure Ollama uses it:

```bash
# Verify GPU is detected
ollama run qwen2.5:7b-instruct "hello" --verbose
# Look for "using GPU" in output
```

GPU translation is typically 10-20x faster than CPU.

### Choose the Right Model Size

| Model | VRAM Required | Speed | Quality |
|---|---|---|---|
| 3B models | ~2.5GB | Very fast | Acceptable |
| 7B models | ~5GB | Fast | Very good |
| 14B models | ~9GB | Medium | Excellent |
| 32B models | ~20GB | Slow | Best |

For most anime translation workflows, `qwen2.5:7b-instruct` is the best speed/quality trade-off.

### Increase Batch Size

```
SUBLARR_BATCH_SIZE=25
```

Risk: larger batches may hit context window limits. If you see truncated translations, reduce to 15.

### Translation Memory

Enable in **Settings → Translation → Translation Memory**. For series with consistent dialogue, this can reduce translation time by 30-60%.

---

## Provider Search Speed

### Disable Unused Providers

Go to **Settings → Providers** and toggle off providers you never use.

### Tune Dynamic Timeouts

```
SUBLARR_PROVIDER_DYNAMIC_TIMEOUT_ENABLED=true
SUBLARR_PROVIDER_DYNAMIC_TIMEOUT_MULTIPLIER=2.0
```

### Provider Cache

```
SUBLARR_PROVIDER_CACHE_TTL_MINUTES=10
```

---

## Wanted Scanner

Since v0.20.0, ffprobe results are cached with mtime-based invalidation — unchanged files are skipped entirely.

### Reduce Worker Count

```
SUBLARR_SCAN_METADATA_MAX_WORKERS=2
```

### Yield CPU Between Series (v0.20.0+)

```
SUBLARR_SCAN_YIELD_MS=5
```

### Increase Scan Interval

```
SUBLARR_WANTED_SCAN_INTERVAL_HOURS=12
```

### Adaptive Backoff

```
SUBLARR_WANTED_ADAPTIVE_BACKOFF_ENABLED=true
SUBLARR_WANTED_BACKOFF_BASE_HOURS=2.0
SUBLARR_WANTED_BACKOFF_CAP_HOURS=168
```

---

## Database

### SQLite Tuning (default)

WAL mode is enabled by default. For large libraries (1000+ series):

1. Run `VACUUM` periodically via Settings → System → Maintenance
2. Consider migrating to PostgreSQL

### PostgreSQL Migration

```
SUBLARR_DATABASE_URL=postgresql://sublarr:password@postgres:5432/sublarr
```

```bash
python3 backend/scripts/migrate_sqlite_to_postgres.py \
  --sqlite /config/sublarr.db \
  --postgres postgresql://sublarr:pass@postgres:5432/sublarr
```

---

## Translation Worker Pool (v0.20.0+)

```
SUBLARR_TRANSLATION_MAX_WORKERS=4
```

With Redis+RQ, scale via workers instead:

```bash
docker compose -f docker-compose.redis.yml up -d --scale rq-worker=3
```

---

## Redis (Advanced)

```
SUBLARR_REDIS_URL=redis://redis:6379/0
SUBLARR_REDIS_CACHE_ENABLED=true
SUBLARR_REDIS_QUEUE_ENABLED=true
```

Quick start: `docker compose -f docker-compose.redis.yml up -d`

---

## Docker Resource Limits

```yaml
deploy:
  resources:
    limits:
      cpus: '2.0'
      memory: 2G
    reservations:
      cpus: '0.5'
      memory: 512M
```

---

## Monitoring

```bash
curl http://localhost:5765/api/v1/health/detailed
```

Reports: provider health, translation backend health, media server connectivity, scheduler status, database health.
