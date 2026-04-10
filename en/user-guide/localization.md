---
title: UI Localization
description: Switching the Sublarr interface language between German and English
published: true
date: 2026-04-10
---

# UI Localization

As of v0.43.0-beta, Sublarr's interface is fully localized and can be switched between **German** (default) and **English** at any time.

## Changing the Interface Language

1. Open **Settings → General**
2. Find the **Language** setting
3. Select **Deutsch** or **English**
4. The interface updates immediately — no restart required

## What is Localized

All visible UI strings are localized, including:
- Page titles and headings
- Table column headers
- Button labels
- Status messages and toasts
- Settings field labels and hints
- Filter labels and dropdown options

## Subtitle Languages

The language selector for subtitle searches and language profiles supports **74 languages** as of v0.47.0-beta (up from 20 in earlier versions).

This covers all major subtitle provider languages and allows fine-grained per-profile configuration.

## Default Language

The application defaults to **German (Deutsch)**. This can be changed per user in Settings.

Note: Subtitle file content is not translated by the localization system — only the UI. For subtitle translation, see [Translation](/user-guide/settings/translation).
