---
title: Circuit Breaker
description: How Sublarr protects against failing subtitle providers
published: true
date: 2026-04-13
---

# Circuit Breaker

Sublarr wraps every subtitle provider call in a **circuit breaker**. When a
provider starts failing repeatedly, the circuit breaker opens and prevents
further requests until the provider has had time to recover. This stops
cascade timeouts from blocking your entire download queue.

## State Machine

```
CLOSED ──(5 consecutive failures)──► OPEN
  ▲                                    │
  │                              (300 s cooldown)
  │                                    ▼
  └────(probe succeeds)────── HALF_OPEN
                probe fails ──► OPEN
```

| State | Meaning | Requests allowed |
|---|---|---|
| **CLOSED** | Normal operation, failures are counted | Yes |
| **OPEN** | Provider assumed down, calls are rejected immediately | No |
| **HALF_OPEN** | Cooldown elapsed — one probe request allowed through | One probe |

### Transitions

| From | To | Trigger |
|---|---|---|
| CLOSED | OPEN | 5 consecutive failures |
| OPEN | HALF_OPEN | 300 seconds have elapsed since last failure |
| HALF_OPEN | CLOSED | Probe call succeeded |
| HALF_OPEN | OPEN | Probe call failed |
| Any | CLOSED | Manual reset via API |

## Configuration

Circuit breaker thresholds are global defaults. Future releases will expose
per-provider overrides.

| Parameter | Default | Description |
|---|---|---|
| `failure_threshold` | `5` | Consecutive failures before opening |
| `cooldown_seconds` | `300` | Seconds to wait before allowing a probe |

> [!NOTE]
> The cooldown was increased from 60 to 300 seconds in v0.51.3-beta to reduce probe frequency against providers that are genuinely down, lowering the risk of IP bans from repeated retries.

## Persistence

Circuit breaker state (open/closed, failure count, last failure time) is
persisted in the `circuit_breaker_state` database table. After a restart,
the breaker restores its previous state. If the breaker was OPEN and the
cooldown has already elapsed at restart time, it immediately transitions to
HALF_OPEN so the first real request acts as the probe — no extra wait.

## Monitoring via Prometheus

Each provider's circuit breaker exposes metrics at `GET /api/v1/metrics`:

| Metric | Description |
|---|---|
| `sublarr_circuit_breaker_state` | Current state as a label (`closed`, `open`, `half_open`) |
| `sublarr_circuit_breaker_failure_count` | Current consecutive failure count |

Use these metrics in Grafana or Prometheus alerts to detect providers that
are repeatedly tripping their circuit breakers.

## Manually Resetting a Provider

If a provider's circuit breaker is OPEN and you want to force an immediate
retry without waiting for the cooldown:

1. Go to **Settings → Providers**
2. Find the provider with the "Circuit Open" badge
3. Click **Reset** (or **Re-enable** depending on UI version)

This calls the internal `reset()` method, which immediately transitions the
breaker to CLOSED and persists the new state.

Alternatively, via API:

```bash
curl -X POST http://localhost:5765/api/v1/providers/PROVIDER_NAME/reset \
  -H "X-Api-Key: YOUR_API_KEY"
```

## Why Providers Get Disabled

Providers are auto-disabled (circuit opened) when:
- They return repeated HTTP errors (4xx/5xx)
- They time out repeatedly
- Their response cannot be parsed
- Authentication errors (401/403) are returned repeatedly

> [!NOTE]
> **v0.51.3 change:** Authentication errors (HTTP 401, 403) now propagate to the circuit breaker. Previously, auth errors were silently retried without counting toward the failure threshold. If a provider's API key expires or is revoked, the circuit will now open after 5 consecutive auth failures, preventing pointless retries. Fix the credentials and reset the provider to resume.

A single failure does not open the circuit. Five consecutive failures are
required (default threshold). A single success resets the failure counter.
