---
title: Settings — Profiles
description: Quality profiles, language profiles, and scoring configuration
published: true
date: 2026-03-14
---

# Settings — Profiles

## Language Profiles

Language Profiles define the subtitle search strategy per series. See the dedicated [Language Profiles](/user-guide/language-profiles) page for full documentation.

## Quality / Score Thresholds

Each profile sets a minimum score (0–100) a subtitle must reach before it is downloaded.

| Score range | Meaning |
|-------------|----------|
| 80–100 | High confidence match — exact episode hash or release name match |
| 60–79 | Good match — title + season/episode match |
| 40–59 | Weak match — title only |
| < 40 | Rejected — too uncertain |

See [Settings → Providers → Scoring](/user-guide/settings/providers#scoring) for how scores are calculated.

## Delay Profiles

Delay profiles add a wait time before searching, allowing better subtitle releases to appear. Useful for newly aired episodes where only machine-translated subtitles are available initially.

Configure per language profile: **Delay (hours)** — default `0`.
