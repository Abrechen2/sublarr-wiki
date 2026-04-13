---
title: Bibliothek
description: Medienbibliothek in Sublarr durchsuchen und verwalten
published: true
date: 2026-03-14
---

# Bibliothek

Die Bibliotheksansicht zeigt alle Serien und Filme, die Sublarr kennt — bezogen aus den verbundenen Jellyfin-, Emby- oder *arr-Integrationen.

## Serienliste

Jede Zeile zeigt:
- **Titel** — Serienname mit Jahr
- **Untertitelstatus** — wie viele Episoden Untertitel haben im Verhältnis zur Gesamtzahl
- **Sprache** — aktives Sprachprofil
- **Zuletzt genutzter Provider** — welcher Provider den letzten Untertitel gefunden hat

Auf eine Serie klicken, um die **Seriendetailansicht** mit Untertitelstatus pro Episode zu öffnen.

## Seriendetail

- Listet alle Staffeln und Episoden auf
- Zeigt Untertitel-Dateipfad, Score, Provider und Sprache für jede Episode
- **Suchen**-Schaltfläche löst eine manuelle Provider-Suche für die Episode aus
- **Übersetzen**-Schaltfläche schickt den Untertitel durch die LLM-Übersetzungs-Pipeline
- **Löschen** entfernt die Untertiteldatei (wird in den Papierkorb verschoben)

## Filter

Mit der Filterleiste die Anzeige eingrenzen nach:
- Untertitelstatus: `Alle`, `Untertitelt`, `Fehlend`, `Wanted`
- Sprachprofil
- Provider

## Massenaktionen

Mehrere Serien über die Checkbox-Spalte auswählen, dann:
- **Batch-Suche** — durchsucht alle ausgewählten Serien nach fehlenden Untertiteln
- **Batch-Übersetzung** — reiht alle ausgewählten zur Übersetzung ein
