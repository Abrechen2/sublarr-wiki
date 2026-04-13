---
title: Performance-Optimierung
description: Sublarr für große Bibliotheken und hohen Übersetzungsdurchsatz optimieren
published: true
date: 2026-03-14
---

# Performance-Optimierung

Sublarr läuft standardmäßig gut, bietet aber verschiedene Stellschrauben für große Bibliotheken oder ressourcenbeschränkte Umgebungen.

---

## LLM-Übersetzungsgeschwindigkeit

### GPU-Beschleunigung nutzen

Falls eine GPU vorhanden ist, sicherstellen, dass Ollama sie nutzt:

```bash
# GPU-Erkennung prüfen
ollama run qwen2.5:7b-instruct "hello" --verbose
# In der Ausgabe nach "using GPU" suchen
```

GPU-Übersetzung ist typischerweise 10–20× schneller als CPU.

### Richtige Modellgröße wählen

| Modell | Benötigter VRAM | Geschwindigkeit | Qualität |
|---|---|---|---|
| 3B-Modelle | ~2,5 GB | Sehr schnell | Akzeptabel |
| 7B-Modelle | ~5 GB | Schnell | Sehr gut |
| 14B-Modelle | ~9 GB | Mittel | Hervorragend |
| 32B-Modelle | ~20 GB | Langsam | Bestmöglich |

Für die meisten Anime-Übersetzungs-Workflows bietet `qwen2.5:7b-instruct` den besten Kompromiss aus Geschwindigkeit und Qualität.

### Batch-Größe erhöhen

```
SUBLARR_BATCH_SIZE=25
```

Risiko: Größere Batches können Kontextfenster-Limits erreichen. Bei abgeschnittenen Übersetzungen auf 15 reduzieren.

### Translation Memory

Unter **Settings → Translation → Translation Memory** aktivieren. Bei Serien mit wiederkehrenden Dialogmustern kann die Übersetzungszeit um 30–60 % reduziert werden.

---

## Provider-Suchgeschwindigkeit

### Ungenutzte Provider deaktivieren

Unter **Settings → Providers** nicht verwendete Provider abschalten.

### Dynamische Timeouts anpassen

```
SUBLARR_PROVIDER_DYNAMIC_TIMEOUT_ENABLED=true
SUBLARR_PROVIDER_DYNAMIC_TIMEOUT_MULTIPLIER=2.0
```

### Provider-Cache

```
SUBLARR_PROVIDER_CACHE_TTL_MINUTES=10
```

---

## Wanted-Scanner

Seit v0.20.0 werden ffprobe-Ergebnisse mit mtime-basierter Invalidierung gecacht — unveränderte Dateien werden komplett übersprungen.

### Worker-Anzahl reduzieren

```
SUBLARR_SCAN_METADATA_MAX_WORKERS=2
```

### CPU zwischen Serien freigeben (v0.20.0+)

```
SUBLARR_SCAN_YIELD_MS=5
```

### Scan-Intervall erhöhen

```
SUBLARR_WANTED_SCAN_INTERVAL_HOURS=12
```

### Adaptives Backoff

```
SUBLARR_WANTED_ADAPTIVE_BACKOFF_ENABLED=true
SUBLARR_WANTED_BACKOFF_BASE_HOURS=2.0
SUBLARR_WANTED_BACKOFF_CAP_HOURS=168
```

---

## Datenbank

### SQLite-Tuning (Standard)

WAL-Modus ist standardmäßig aktiviert. Für große Bibliotheken (1000+ Serien):

1. `VACUUM` regelmäßig über Settings → System → Maintenance ausführen
2. Migration auf PostgreSQL in Betracht ziehen

### PostgreSQL-Migration

```
SUBLARR_DATABASE_URL=postgresql://sublarr:passwort@postgres:5432/sublarr
```

```bash
python3 backend/scripts/migrate_sqlite_to_postgres.py \
  --sqlite /config/sublarr.db \
  --postgres postgresql://sublarr:passwort@postgres:5432/sublarr
```

---

## Übersetzungs-Worker-Pool (v0.20.0+)

```
SUBLARR_TRANSLATION_MAX_WORKERS=4
```

Mit Redis+RQ stattdessen über Worker skalieren:

```bash
docker compose -f docker-compose.redis.yml up -d --scale rq-worker=3
```

---

## Redis (Erweitert)

```
SUBLARR_REDIS_URL=redis://redis:6379/0
SUBLARR_REDIS_CACHE_ENABLED=true
SUBLARR_REDIS_QUEUE_ENABLED=true
```

Schnellstart: `docker compose -f docker-compose.redis.yml up -d`

---

## Docker-Ressourcenlimits

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

Berichtet: Provider-Health, Übersetzungs-Backend-Health, Medienserver-Konnektivität, Scheduler-Status, Datenbank-Health.
