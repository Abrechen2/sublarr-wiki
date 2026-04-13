---
title: Aktivität
description: Übersetzungsjobs, Download-Verlauf und Hintergrundaufgaben-Überwachung
published: true
date: 2026-03-14
---

# Aktivität

Der Bereich Aktivität zeigt alle Hintergrundoperationen: Untertitel-Downloads, Übersetzungsjobs, Webhook-Events und geplante Scanner-Durchläufe.

## Übersetzungsjobs

Listet alle aktiven und abgeschlossenen Übersetzungsjobs mit:
- Quell- und Zielsprache
- Fortschritt (übersetzte Zeilen / Gesamtzahl)
- Verwendetes Modell (z. B. `anime-translator-v6`)
- Status: `queued`, `running`, `done`, `failed`

## Aufgaben

Die Tasks-Seite (`/tasks` in der UI) bietet Einblick in alle geplanten Hintergrundaufgaben.

**Angezeigte Informationen:**
- Alle geplanten Aufgaben mit Name, Beschreibung, Intervall, letztem Lauf und nächstem geplanten Lauf
- Aktueller Status: aktiviert/deaktiviert, Anzeige ob gerade aktiv
- Fortschrittsbalken pro Aufgabe bei lang laufenden Operationen

**Manuelle Steuerung:**
- **Abbrechen** — eine aktuell laufende Aufgabe stoppen
- **Auslösen** — eine Aufgabe sofort außerhalb des Zeitplans ausführen

Verfügbare Aufgaben umfassen den Wanted-Scanner, die Wanted-Suche, den Upgrade-Scanner, Cache-Bereinigung und den Backup-Scheduler.
