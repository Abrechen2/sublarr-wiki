---
title: General Troubleshooting
description: Common Sublarr issues and how to fix them
published: true
date: 2026-03-14
---

# Troubleshooting Guide

## Subtitles Not Downloading

Common causes:

1. **No matching provider found** — check which providers are enabled and have API keys
2. **Provider circuit breaker OPEN** — a provider failed too many times; wait for cooldown or reset in Settings
3. **Item not in Wanted queue** — check the Wanted page to see if the file was scanned
4. **Path mapping wrong** — if Sonarr and Sublarr use different mount paths, set `SUBLARR_PATH_MAPPING`
5. **Language profile not set** — the series needs a language profile assigned in Library

Check **Queue** and **Activity** pages for error details.

## Translation Not Running

1. Verify Ollama is reachable: `curl http://ollama-host:11434/api/tags`
2. Check the model is pulled: `ollama list`
3. Check the Queue page for failed translation jobs
4. Look at container logs: `docker logs sublarr`

## Container Logs

```bash
docker logs sublarr --tail 100 --follow
```

Enable debug logging:

```
SUBLARR_LOG_LEVEL=DEBUG
```

## Health Check

```bash
curl http://localhost:5765/api/v1/health/detailed
```

Returns status for all 11 subsystems: providers, translation backends, media servers, scheduler, database.

## Pre-commit Hooks (Development)

```bash
# Install pre-commit
pip install pre-commit
pre-commit install
```

## CI Pipeline Issues

- Check GitHub Actions logs for specific errors
- Verify `.env.example` contains all required variables
- Run tests locally: `pytest` or `npm test`

## Further Help

1. Open an [issue on GitHub](https://github.com/Abrechen2/sublarr/issues) with:
   - Error message
   - Steps to reproduce
   - System info (OS, Docker version)
2. Check logs: `backend/logs/` or frontend console
