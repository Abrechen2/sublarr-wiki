---
title: Settings — Profile
description: Qualitätsprofile, Sprachprofile und Scoring-Konfiguration
published: true
date: 2026-03-14
---

# Settings — Profile

## Sprachprofile

Sprachprofile definieren die Untertitel-Suchstrategie pro Serie. Siehe die separate Seite [Sprachprofile](/user-guide/language-profiles) für die vollständige Dokumentation.

## Qualität / Score-Schwellenwerte

Jedes Profil legt einen Mindest-Score (0–100) fest, den ein Untertitel erreichen muss, bevor er heruntergeladen wird.

| Score-Bereich | Bedeutung |
|---------------|-----------|
| 80–100 | Hohe Konfidenz — exakter Episoden-Hash oder Release-Name-Match |
| 60–79 | Guter Match — Titel + Staffel-/Episodennummer stimmen |
| 40–59 | Schwacher Match — nur Titel |
| < 40 | Abgelehnt — zu unsicher |

Siehe [Settings → Provider → Scoring](/user-guide/settings/providers#scoring) für die Berechnung der Scores.

## Verzögerungsprofile

Verzögerungsprofile fügen eine Wartezeit vor der Suche hinzu, damit bessere Untertitel-Releases erscheinen können. Nützlich für frisch ausgestrahlte Episoden, bei denen zunächst nur maschinell übersetzte Untertitel verfügbar sind.

Konfiguration pro Sprachprofil: **Delay (hours)** — Standard `0`.
