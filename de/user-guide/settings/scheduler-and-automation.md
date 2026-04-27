---
title: Settings — Scheduler & Automation
description: APScheduler-Profil, Wanted-Search-Pacing und die persistente Subtitle-Automation-Queue
published: true
date: 2026-04-27
---

# Settings — Scheduler & Automation

Diese Seite dokumentiert die Stellschrauben dafür, **wann** Sublarr Arbeit erledigt — das Scheduler-Profil, das die Intervalle aller wiederkehrenden Jobs setzt, das Wanted-Search-Pacing, das Premium-Serien gegenüber Backlog im Vorrang hält, und die persistente Subtitle-Automation-Queue, die ausstehende Embedded-Track-Extraktionen im Hintergrund abarbeitet.

> [!TIP]
> Die UI liegt unter **Settings → System → Scheduler** (Job-Liste, Run-Now, Historie) und **Settings → Wanted → Scheduling** (Pacing). Die unten dokumentierten Felder sind die zugrundeliegenden Konfig-Werte — beide UI-Seiten schreiben auf dieselben `SUBLARR_*`-Werte.

## Scheduler-Profil *(v0.55.0-beta)*

Ein einzelnes Preset, das auf einen vollständigen Satz von Job-Intervallen abgebildet wird (Wanted-Scan, Wanted-Search, Cleanup, AniDB-Sync, Scheduler-History-Cleanup). Für 95 % der Installationen reicht das Preset; auf `custom` umstellen nur dann, wenn ein konkreter Grund existiert, von einem Preset-Intervall abzuweichen.

| Einstellung | Default | Umgebungsvariable | Beschreibung |
|-------------|---------|-------------------|--------------|
| Scheduler-Profil | `balanced` | `SUBLARR_SCHEDULER_PROFILE` | Eines von: `light`, `balanced`, `aggressive`, `custom`. `light` tickt einmal pro Stunde; `balanced` ist der Default; `aggressive` tickt alle 15 Min. für Premium-lastige Libraries; `custom` erlaubt das individuelle Editieren jedes Job-Intervalls unter **Settings → System → Scheduler**. |
| Scheduler-Historie (Tage) | `30` | `SUBLARR_SCHEDULER_HISTORY_RETENTION_DAYS` | Wie viele Tage `scheduler_job_runs`-Zeilen aufbewahrt werden, bevor der Job `scheduler_history_cleanup` sie löscht. Werte unter 7 verbergen den Großteil des Diagnose-Kontexts; Werte über 90 lassen die Tabelle spürbar wachsen. |

## Wanted-Search-Pacing *(V1-Budget-Scheduler — v0.55.0-beta)*

Steuert, welche Items der Wanted-Search-Tick versuchen darf. Die Defaults geben eine faire Rotation, die selbst bei vollem Backlog stets Kapazität für Premium- und Standard-Items reserviert.

| Einstellung | Default | Umgebungsvariable | Beschreibung |
|-------------|---------|-------------------|--------------|
| Suchreihenfolge | `fair` | `SUBLARR_WANTED_SEARCH_ORDER` | Item-Reihenfolge innerhalb eines Ticks: `fair` rotiert ältest-gesucht zuerst; `newest_first` bedient frisch hinzugefügte Items sofort; `weighted` mischt beides, gewichtet nach Sprachprofil-Priorität. |
| Priorisierungsgewichtung aktiv | `true` | `SUBLARR_WANTED_SCHEDULER_PRIORITY_WEIGHTING_ENABLED` | Bei `true` stellt die Such-`ORDER BY` einen Prioritätsrang voran (premium=0, standard=1, backlog=2), damit Premium-Items immer das erste Stück des Per-Tick-Budgets bekommen. Nur deaktivieren, wenn reines FIFO-Scheduling gewünscht ist. |
| Backlog-Reserve (%) | `50` | `SUBLARR_WANTED_SCHEDULER_BACKLOG_RESERVE_PCT` | Tagesbudget-Verbrauch in Prozent, ab dem Backlog-Priorität-Items in den nächsten Tick verschoben werden. Stellt sicher, dass Premium + Standard immer einen fairen Anteil des Tages-Quotums bekommen. |

> [!NOTE]
> Kombinationswirkung: In einem typischen Tick laufen Premium-Items zuerst; Backlog-Items laufen nur, solange das Tagesbudget unter `backlog_reserve_pct` liegt. Sobald die Schwelle überschritten ist, werden bis zum nächsten UTC-Tageswechsel nur noch Premium- und Standard-Items gesucht.

## Subtitle-Automation-Queue *(v0.71.0-beta)*

Persistente Queue für die Post-Scan-Automation-Pipeline (Embedded-Track-Extraktion, SDH-Penalty, Fremdsprachenspuren-Cleanup). Items landen in der Queue, sobald der Wanted-Scanner einen Embedded-Untertitel erkennt, der das Sprachprofil erfüllt; ein Drain-Worker arbeitet sie im Hintergrund ab, damit der Scan selbst schnell bleibt.

| Einstellung | Default | Umgebungsvariable | Beschreibung |
|-------------|---------|-------------------|--------------|
| Subtitle-Automation aktiv | `false` | `SUBLARR_SUBTITLE_AUTOMATION_ENABLED` | Hauptschalter. Bei `false` werden Embedded-Untertitel wie zuvor synchron im Wanted-Scanner-Tick verarbeitet. Aktivieren, um sie über die persistente Queue + Drain-Worker-Pipeline laufen zu lassen. |
| Queue aktiv | `true` | `SUBLARR_SUBTITLE_AUTOMATION_QUEUE_ENABLED` | Drain-Worker-Schalter. Nur relevant, wenn die Automation aktiv ist. Bei `false` werden Items in die Queue geschrieben, aber nie abgearbeitet — nützlich für Diagnose oder Wartungsfenster. |
| Drain-Intervall (Minuten) | `2` | `SUBLARR_SUBTITLE_AUTOMATION_DRAIN_INTERVAL_MINUTES` | Scheduler-Takt für den Drain-Worker. Jeder Tick verarbeitet einen Batch ausstehender Einträge; Fehlschläge werden mit exponentiellem Backoff erneut versucht. Niedrigere Werte beschleunigen die Verarbeitung neuer Items, kosten aber mehr Wakeups. |

> [!TIP]
> Das Queue-Dashboard liegt unter **Settings → Subtitle Automation**. Es zeigt Pending- / Running- / Done- / Failed-Zähler sowie den letzten Drain-Zeitstempel; fehlgeschlagene Einträge können dort retried oder verworfen werden.
