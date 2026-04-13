---
title: Circuit Breaker
description: Wie Sublarr vor fehlschlagenden Untertitel-Providern schützt
published: true
date: 2026-04-03
---

# Circuit Breaker

Sublarr umschließt jeden Aufruf an einen Untertitel-Provider mit einem **Circuit Breaker**.
Wenn ein Provider wiederholt fehlschlägt, öffnet sich der Circuit Breaker und
verhindert weitere Anfragen, bis der Provider Zeit zur Erholung hatte. Das
stoppt kaskadierende Timeouts, die die gesamte Download-Queue blockieren könnten.

## Zustandsautomat

```
CLOSED ──(5 aufeinanderfolgende Fehler)──► OPEN
  ▲                                          │
  │                                    (60 s Cooldown)
  │                                          ▼
  └────(Probe erfolgreich)────── HALF_OPEN
                Probe fehlgeschlagen ──► OPEN
```

| Zustand | Bedeutung | Anfragen erlaubt |
|---|---|---|
| **CLOSED** | Normalbetrieb, Fehler werden gezählt | Ja |
| **OPEN** | Provider als ausgefallen angenommen, Aufrufe werden sofort abgelehnt | Nein |
| **HALF_OPEN** | Cooldown abgelaufen — eine Probe-Anfrage wird durchgelassen | Eine Probe |

### Übergänge

| Von | Nach | Auslöser |
|---|---|---|
| CLOSED | OPEN | 5 aufeinanderfolgende Fehler |
| OPEN | HALF_OPEN | 60 Sekunden seit dem letzten Fehler vergangen |
| HALF_OPEN | CLOSED | Probe-Aufruf erfolgreich |
| HALF_OPEN | OPEN | Probe-Aufruf fehlgeschlagen |
| Beliebig | CLOSED | Manuelles Zurücksetzen via API |

## Konfiguration

Circuit-Breaker-Schwellenwerte sind globale Standardwerte. Zukünftige Releases
werden Pro-Provider-Überschreibungen bereitstellen.

| Parameter | Standard | Beschreibung |
|---|---|---|
| `failure_threshold` | `5` | Aufeinanderfolgende Fehler bis zur Öffnung |
| `cooldown_seconds` | `60` | Sekunden Wartezeit bis zur Probe |

## Persistenz

Der Circuit-Breaker-Zustand (offen/geschlossen, Fehlerzähler, Zeitpunkt des letzten Fehlers)
wird in der Datenbanktabelle `circuit_breaker_state` persistiert. Nach einem Neustart
stellt der Breaker seinen vorherigen Zustand wieder her. War der Breaker OPEN und ist
der Cooldown zum Neustartzeitpunkt bereits abgelaufen, wechselt er sofort nach
HALF_OPEN, sodass die erste echte Anfrage als Probe dient — keine zusätzliche Wartezeit.

## Überwachung via Prometheus

Der Circuit Breaker jedes Providers stellt Metriken unter `GET /api/v1/metrics` bereit:

| Metrik | Beschreibung |
|---|---|
| `sublarr_circuit_breaker_state` | Aktueller Zustand als Label (`closed`, `open`, `half_open`) |
| `sublarr_circuit_breaker_failure_count` | Aktuelle Anzahl aufeinanderfolgender Fehler |

Diese Metriken in Grafana oder Prometheus-Alerts verwenden, um Provider zu erkennen,
die wiederholt ihren Circuit Breaker auslösen.

## Provider manuell zurücksetzen

Falls der Circuit Breaker eines Providers OPEN ist und ein sofortiger Retry
ohne Warten auf den Cooldown gewünscht ist:

1. Zu **Settings → Providers** navigieren
2. Den Provider mit dem Badge „Circuit Open" finden
3. **Reset** (oder **Re-enable**, je nach UI-Version) klicken

Dies ruft die interne `reset()`-Methode auf, die den Breaker sofort auf CLOSED
setzt und den neuen Zustand persistiert.

Alternativ via API:

```bash
curl -X POST http://localhost:5765/api/v1/providers/PROVIDER_NAME/reset \
  -H "X-Api-Key: EIGENER_API_KEY"
```

## Warum Provider deaktiviert werden

Provider werden automatisch deaktiviert (Circuit geöffnet), wenn:
- Sie wiederholt HTTP-Fehler zurückgeben (4xx/5xx)
- Sie wiederholt Timeouts verursachen
- Ihre Antwort nicht geparst werden kann

Ein einzelner Fehler öffnet den Circuit nicht. Fünf aufeinanderfolgende Fehler
sind erforderlich (Standard-Schwellenwert). Ein einzelner Erfolg setzt den
Fehlerzähler zurück.
