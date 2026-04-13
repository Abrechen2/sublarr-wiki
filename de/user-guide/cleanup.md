---
title: Cleanup
description: Automatische Bereinigungsoperationen für Untertitel-Sidecar-Dateien und Datenbankeinträge
published: true
date: 2026-04-10
---

# Cleanup

Die Cleanup-Seite (**Settings → Cleanup**) bietet fünf feste automatische Operationen, die unnötige Untertiteldateien und Datenbankeinträge aus der Bibliothek entfernen.

## Operationen

### Sprachfilter
Löscht Untertitel-Sidecar-Dateien in Sprachen, die nicht auf der Behalten-Liste stehen. Konfiguriert wird, welche Sprachen beibehalten werden — alle anderen werden entfernt.

**Konfiguration:** Kommaseparierte Liste der zu behaltenden Sprachcodes (z. B. `de,en`)

### Format-Upgrade
Löscht SRT-Sidecar-Dateien, wenn eine ASS-Version desselben Untertitels für dieselbe Episode bereits existiert. Hält die Bibliothek frei von qualitativ niedrigeren Duplikaten.

### Verwaiste Dateien
Löscht Untertitel-Sidecar-Dateien, zu denen keine passende Videodatei im selben Ordner existiert. Erfasst übrig gebliebene Sidecars nach dem Verschieben oder Löschen von Medien.

### Verwaiste DB-Einträge
Entfernt Datenbankeinträge, deren Untertiteldatei nicht mehr auf der Festplatte existiert. Hält die Datenbank konsistent mit dem tatsächlichen Dateisystem-Zustand.

### Alte Backups
Löscht Remux-Backup-Dateien (`.mkv.bak`) nach Ablauf der konfigurierten Aufbewahrungsfrist. Gesteuert durch die Einstellung `remux_backup_retention_days`.

## Verwendung der Cleanup-Operationen

Jede Operation ist eine aufklappbare Karte mit:
- **Schalter** — die Operation aktivieren oder deaktivieren
- **Konfiguration** — operationsspezifische Einstellungen (z. B. Sprachliste)
- **Zeitplan** — manuell / täglich / wöchentlich / nach Scan
- **Vorschau** — Probelauf mit bis zu 20 Beispieldateien, die betroffen wären, mit Begründung
- **Jetzt ausführen** — sofort ausführen

## Dry-Run-Vorschau

Vor der Ausführung einer Operation **Vorschau** nutzen, um zu sehen, welche Dateien betroffen wären. Die Vorschau zeigt:
- Dateipfad
- Dateigröße
- Löschgrund (z. B. `lang:ja`, `ersetzt durch Episode.S01E01.de.ass`, `kein Video im Ordner`)

Während der Vorschau werden keine Dateien gelöscht.

## Deduplizierung

Der Bereich **Deduplizierung** unterhalb der fünf Operationen nutzt SHA-256-Hashing, um identische Untertiteldateien in der gesamten Bibliothek zu finden. Einen Scan starten, dann für jede Duplikat-Gruppe auswählen, welche Kopie behalten werden soll.

## Cleanup-Verlauf

Vergangene Cleanup-Durchläufe erscheinen in der **Verlauf**-Tabelle am Ende der Seite und zeigen verarbeitete Dateien, gelöschte Dateien und freigegebene Bytes pro Durchlauf.
