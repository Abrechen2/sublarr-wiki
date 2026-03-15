---
title: AI Glossary
description: Per-series term dictionary that guides the LLM translation pipeline to use the correct names and phrases.
published: true
date: 2026-03-15
tags: user-guide
editor: markdown
dateCreated: 2026-03-15
---

# AI Glossary

![AI Glossary](/img/screen-ai-glossary.png)

Define series-specific terms that the LLM translation pipeline must use consistently — character names, locations, attack names, and other proper nouns.

## Overview

When Sublarr translates subtitles using an LLM (local Ollama or a cloud provider), it has no inherent knowledge of which romanisation a fandom has settled on for character names, how a series-specific technique should be rendered, or what a fictional location is called in an established translation. Without guidance, the model will guess — and it may guess differently from line to line.

The AI Glossary solves this by injecting a structured term dictionary into the translation prompt. Any entry you add is presented to the model as a hard override: "always translate `<source_term>` as `<target_term>`." This produces consistent, predictable output for terms that matter.

## Creating a Glossary

1. Navigate to **Library** and open a series.
2. Click the **⋮ (three-dot)** menu on the series header row.
3. Select **Edit Glossary**.
4. Enter your terms in YAML format (see below) in the editor.
5. Click **Save**.

The glossary is stored per-series and applies to all episodes in that series. It does not apply automatically to other series, even if some terms overlap.

## Glossary Format

The glossary is a YAML mapping of source terms (in the subtitle's source language) to their target translations.

```yaml
# Character names
Naruto Uzumaki: Naruto Uzumaki
Sasuke Uchiha: Sasuke Uchiha
Kakashi-sensei: Kakashi-Sensei

# Locations
Konoha: Konoha
Land of Fire: Land of Fire

# Techniques and attacks
Rasengan: Rasengan
Chidori: Chidori
Shadow Clone Jutsu: Shadow Clone Jutsu

# Series-specific terms
chakra: Chakra
shinobi: Shinobi
```

Keys are the source-language terms (e.g. Japanese romanisation, or German source terms for a DE→EN translation). Values are the exact strings the LLM should output in the target language.

Term matching is **case-insensitive** on the source side but **case-preserving** on the target side — the value you write is the exact string injected into the prompt instruction.

## How It Works

Before each translation request, Sublarr reads the series glossary and constructs an instruction block that is prepended to the LLM's system prompt:

```
The following term overrides MUST be applied exactly as listed.
Do not translate, paraphrase, or alter these terms:

- "Rasengan" → "Rasengan"
- "chakra" → "Chakra"
- "Konoha" → "Konoha"
```

This block is injected before the general translation instruction and before the subtitle lines. Most instruction-following models honour these overrides reliably for single-word and short-phrase entries.

## Limitations

- **Complex compound phrases.** LLMs may occasionally ignore glossary entries when the source term is a long compound phrase or appears in the middle of a grammatically complex sentence. Keep entries as short as possible.
- **Inflected forms.** The glossary matches exact strings. If your source subtitle contains an inflected form (e.g. `Chakras` instead of `Chakra`), it will not be caught by a `chakra` entry. Add inflected forms as separate entries if needed.
- **Model-dependent reliability.** Smaller local models (e.g. 7B parameter Ollama models) are less reliable at following override instructions than larger cloud models. If overrides are being ignored, switch to a larger model or a cloud provider.
- **No regex or wildcard matching.** Each entry must be a literal string.

## Tips

> [!TIP]
> Keep glossary entries focused on **proper nouns and series-specific terms** — character names, place names, technique names, and fandom-established spellings. Do not add common vocabulary; the LLM handles that better without constraints.

- One entry per line. YAML does not require quoting unless the value contains special characters (`:`, `#`, `{`, etc.).
- If a character name should remain identical in the target language (e.g. a Japanese name kept in romanisation for an EN target), add it anyway — this prevents the model from attempting to "translate" it into a phonetically similar but different spelling.
- Revisit the glossary after reviewing the first few translated episodes of a new series. Early translation results will reveal which terms the model is getting wrong.
