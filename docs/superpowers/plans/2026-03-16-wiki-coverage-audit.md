# Wiki Coverage Audit — Implementation Plan

> **For agentic workers:** REQUIRED: Use superpowers:subagent-driven-development (if subagents available) or superpowers:executing-plans to implement this plan. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Produce a structured gap report showing which Sublarr features and settings fields are undocumented or outdated in the SublarrWiki, then create a reusable skill to re-run the audit in future sessions.

**Architecture:** Three sequential phases: (1) Settings-model→wiki mapping via Python script, (2) Changelog+git feature→wiki mapping via Python script, (3) one combined Markdown gap report + a Claude skill for future reuse. All scripts are standalone, output Markdown, and run from Windows with `python`.

**Tech Stack:** Python 3 (stdlib only), `re`, `ast`, `pathlib`, `subprocess` (for git); Bash for running scripts; Markdown for output; Claude Code skill YAML for the skill.

---

## Chunk 1: Approach 3 — Settings/Config → Wiki Audit Script

### Task 1: Write `wiki_audit_settings.py`

**Files:**
- Create: `Z:/CC/wiki_audit_settings.py`

This script:
1. Parses `Sublarr/backend/config.py` — extracts every `field_name: type = default` line from the `Settings` class via regex.
2. Groups fields by their comment-section headers (e.g. `# Ollama`, `# Translation`, `# Subtitle Providers`).
3. Loads all `SublarrWiki/user-guide/settings/*.md` and `SublarrWiki/en/user-guide/settings/*.md` — reads their full text.
4. For each field, checks whether the field name (or its `SUBLARR_` env var equivalent) appears in any wiki file.
5. Outputs a Markdown table: `| field | section | env_var | found_in_wiki | wiki_file |`

- [ ] **Step 1: Create the script**

```python
#!/usr/bin/env python3
"""Approach 3: Map Settings fields → SublarrWiki coverage."""

import re
from pathlib import Path

REPO_ROOT = Path(__file__).parent
CONFIG_PY = REPO_ROOT / "Sublarr/backend/config.py"
WIKI_SETTINGS_DIRS = [
    REPO_ROOT / "SublarrWiki/user-guide/settings",
    REPO_ROOT / "SublarrWiki/en/user-guide/settings",
]

# ── Parse Settings fields ─────────────────────────────────────────────────────
field_pattern = re.compile(r"^    ([a-z_]+):\s+\S.*?(?:#(.*))?$")
section_pattern = re.compile(r"^    # (.+)$")

fields = []  # list of (section, field_name, comment)
current_section = "General"

with open(CONFIG_PY) as f:
    in_class = False
    for line in f:
        if line.strip().startswith("class Settings"):
            in_class = True
            continue
        if not in_class:
            continue
        if line.startswith("class ") and not line.startswith("    "):
            break  # left the class

        sec_m = section_pattern.match(line.rstrip())
        if sec_m:
            current_section = sec_m.group(1)
            continue

        fld_m = field_pattern.match(line.rstrip())
        if fld_m:
            name = fld_m.group(1)
            comment = (fld_m.group(2) or "").strip()
            fields.append((current_section, name, comment))

# ── Load wiki files ────────────────────────────────────────────────────────────
wiki_texts: dict[str, str] = {}
for wiki_dir in WIKI_SETTINGS_DIRS:
    if not wiki_dir.exists():
        continue
    for md in wiki_dir.glob("*.md"):
        key = md.relative_to(REPO_ROOT).as_posix()
        wiki_texts[key] = md.read_text(encoding="utf-8").lower()

# ── Cross-reference ────────────────────────────────────────────────────────────
print("# Settings Coverage Report\n")
print("| Section | Field | Env Var | In Wiki | Wiki File |")
print("|---------|-------|---------|---------|-----------|")

missing = []
for section, name, comment in fields:
    env_var = f"SUBLARR_{name.upper()}"
    found_file = None
    for wiki_path, text in wiki_texts.items():
        if name.lower() in text or env_var.lower() in text:
            found_file = wiki_path
            break
    status = "✅" if found_file else "❌"
    wiki_ref = f"`{found_file}`" if found_file else "—"
    print(f"| {section} | `{name}` | `{env_var}` | {status} | {wiki_ref} |")
    if not found_file:
        missing.append((section, name, env_var))

print(f"\n## Summary\n- Total fields: **{len(fields)}**")
print(f"- Documented: **{len(fields) - len(missing)}**")
print(f"- **Missing: {len(missing)}**\n")

if missing:
    print("## Missing Fields\n")
    print("| Section | Field | Env Var |")
    print("|---------|-------|---------|")
    for section, name, env_var in missing:
        print(f"| {section} | `{name}` | `{env_var}` |")
```

- [ ] **Step 2: Run the script and capture output**

```bash
cd Z:/CC
python wiki_audit_settings.py > /tmp/settings_audit.md 2>&1
cat /tmp/settings_audit.md | head -60
```

Expected: Markdown table with ✅/❌ per field, summary at bottom.

- [ ] **Step 3: Review the summary numbers**

Check how many of the ~143 config fields are missing from wiki. Note all `❌` rows — these are documentation gaps.

---

## Chunk 2: Approach 4+1 — Changelog + Git Feature Audit Script

### Task 2: Write `wiki_audit_features.py`

**Files:**
- Create: `Z:/CC/wiki_audit_features.py`

This script:
1. Parses `Sublarr/CHANGELOG.md` — extracts all `### Added` / `### Changed` / `### Fixed` bullet items with their version and feature name (the bold `**Feature Name**` prefix).
2. Runs `git log --oneline --grep="feat:" --since="2025-01-01"` in `Sublarr/` to catch feat-commits not in CHANGELOG.
3. Loads ALL wiki `.md` files (not just settings) and checks whether the feature name appears.
4. Outputs a Markdown table: `| version | feature | type | in_wiki | wiki_file |`

- [ ] **Step 1: Create the script**

```python
#!/usr/bin/env python3
"""Approach 4+1: Map CHANGELOG features + git feat: commits → SublarrWiki coverage."""

import re
import subprocess
from pathlib import Path

REPO_ROOT = Path(__file__).parent
CHANGELOG = REPO_ROOT / "Sublarr/CHANGELOG.md"
SUBLARR_GIT = REPO_ROOT / "Sublarr"
WIKI_ROOT = REPO_ROOT / "SublarrWiki"

# ── Parse CHANGELOG ────────────────────────────────────────────────────────────
version_pattern = re.compile(r"^## \[(.+?)\]")
type_pattern = re.compile(r"^### (Added|Changed|Fixed|Removed)")
feature_pattern = re.compile(r"^\s*[-*]\s+\*\*(.+?)\*\*")

features = []  # list of (version, change_type, feature_name, full_line)
current_version = "unknown"
current_type = "Added"

with open(CHANGELOG, encoding="utf-8") as f:
    for line in f:
        v_m = version_pattern.match(line)
        if v_m:
            current_version = v_m.group(1)
            continue
        t_m = type_pattern.match(line)
        if t_m:
            current_type = t_m.group(1)
            continue
        f_m = feature_pattern.match(line)
        if f_m:
            raw = f_m.group(1)
            # Feature name is the part before " — " (the description separator)
            feature_name = raw.split(" — ")[0].split(": ")[0].strip()
            features.append((current_version, current_type, feature_name, line.strip()))

# ── Parse git feat: commits ────────────────────────────────────────────────────
git_features = []
try:
    result = subprocess.run(
        ["git", "log", "--oneline", "--grep=feat:", "--since=2025-01-01"],
        capture_output=True, text=True, cwd=SUBLARR_GIT
    )
    for line in result.stdout.strip().splitlines():
        sha, *msg_parts = line.split(" ", 1)
        msg = msg_parts[0] if msg_parts else ""
        # Strip "feat: " prefix
        clean = re.sub(r"^feat[(!]?[^:]*:\s*", "", msg, flags=re.IGNORECASE).strip()
        git_features.append((sha, clean))
except Exception as e:
    print(f"<!-- git log failed: {e} -->")

# ── Load wiki files ────────────────────────────────────────────────────────────
wiki_texts: dict[str, str] = {}
for md in WIKI_ROOT.rglob("*.md"):
    # Skip specs/plans (internal docs)
    if "superpowers" in md.parts:
        continue
    key = md.relative_to(REPO_ROOT).as_posix()
    wiki_texts[key] = md.read_text(encoding="utf-8").lower()

def find_in_wiki(name: str) -> str | None:
    """Return first wiki file path containing the name (case-insensitive)."""
    needle = name.lower()
    # Try progressively shorter tokens
    tokens = [needle] + needle.split(" — ")[0].split()[:3]
    for tok in tokens:
        if len(tok) < 4:
            continue
        for path, text in wiki_texts.items():
            if tok in text:
                return path
    return None

# ── Output CHANGELOG coverage ──────────────────────────────────────────────────
print("# Feature Coverage Report\n")
print("## CHANGELOG Features\n")
print("| Version | Type | Feature | In Wiki | Wiki File |")
print("|---------|------|---------|---------|-----------|")

missing_features = []
for version, ftype, name, full in features:
    wiki_file = find_in_wiki(name)
    status = "✅" if wiki_file else "❌"
    wiki_ref = f"`{wiki_file}`" if wiki_file else "—"
    short_name = name[:60] + "…" if len(name) > 60 else name
    print(f"| `{version}` | {ftype} | {short_name} | {status} | {wiki_ref} |")
    if not wiki_file:
        missing_features.append((version, ftype, name))

print(f"\n**CHANGELOG entries scanned:** {len(features)}")
print(f"**Missing from wiki:** {len(missing_features)}\n")

# ── Output git feat: coverage ──────────────────────────────────────────────────
print("## Git `feat:` Commits\n")
print("| SHA | Commit | In Wiki |")
print("|-----|--------|---------|")
for sha, msg in git_features:
    wiki_file = find_in_wiki(msg)
    status = "✅" if wiki_file else "❌"
    print(f"| `{sha}` | {msg[:70]} | {status} |")

# ── Combined missing list ──────────────────────────────────────────────────────
if missing_features:
    print("\n## Missing Features (CHANGELOG only)\n")
    print("| Version | Type | Feature |")
    print("|---------|------|---------|")
    for version, ftype, name in missing_features:
        print(f"| `{version}` | {ftype} | {name} |")
```

- [ ] **Step 2: Run the script and capture output**

```bash
cd Z:/CC
python wiki_audit_features.py > /tmp/features_audit.md 2>&1
cat /tmp/features_audit.md | head -80
```

Expected: Markdown table of all CHANGELOG entries and git feat: commits with ✅/❌.

- [ ] **Step 3: Review results**

Check which recent features (v0.25–v0.30) are missing wiki coverage. This is the actionable gap list.

---

## Chunk 3: Combined Gap Report

### Task 3: Merge into a single report file

**Files:**
- Create: `Z:/CC/SublarrWiki/docs/wiki-gap-report-YYYY-MM-DD.md`

- [ ] **Step 1: Run both scripts and combine**

```bash
cd Z:/CC
python wiki_audit_settings.py > /tmp/settings_audit.md
python wiki_audit_features.py > /tmp/features_audit.md
echo "---" > /tmp/combined.md
cat /tmp/settings_audit.md >> /tmp/combined.md
echo "" >> /tmp/combined.md
echo "---" >> /tmp/combined.md
cat /tmp/features_audit.md >> /tmp/combined.md
cp /tmp/combined.md "SublarrWiki/docs/wiki-gap-report-$(date +%Y-%m-%d).md"
```

- [ ] **Step 2: Read and summarize the combined report**

Read `SublarrWiki/docs/wiki-gap-report-*.md` and produce a human-readable summary:
- How many settings fields undocumented (out of ~143 total)
- Which recent versions (v0.25+) have zero wiki coverage
- Top 5 priority pages to write/update

---

## Chunk 4: Create Reusable Skill

### Task 4: Write the `sublarr-wiki-audit` skill

**Files:**
- Create: `C:/Users/Dennis Wittke/.claude/skills/sublarr-wiki-audit.md`

The skill should:
- Be triggered by "audit wiki", "check wiki coverage", or "wiki gap report"
- Instruct Claude to run `wiki_audit_settings.py` and `wiki_audit_features.py` from `Z:/CC/`
- Read the output and summarize gaps
- Output a prioritized to-do list for wiki updates

- [ ] **Step 1: Write the skill file**

```markdown
---
name: sublarr-wiki-audit
description: Run a coverage audit of the SublarrWiki against the Sublarr codebase. Produces a gap report showing undocumented settings fields and changelog features. Trigger when the user says "audit wiki", "wiki coverage", "wiki gaps", or "check wiki documentation".
---

# Sublarr Wiki Coverage Audit

## When to Use
Trigger when:
- User asks "is X documented in the wiki?"
- User asks "what's missing from the wiki?"
- After a batch of new features are merged
- Periodically to keep wiki in sync with codebase

## Steps

### 1. Run the Settings Audit (Approach 3)

```bash
cd Z:/CC
python wiki_audit_settings.py
```

This script parses `Sublarr/backend/config.py` and checks every Pydantic `Settings` field against wiki pages in `SublarrWiki/user-guide/settings/` and `SublarrWiki/en/user-guide/settings/`. Output is a Markdown table with ✅/❌.

If the script doesn't exist yet: it's at `Z:/CC/wiki_audit_settings.py`. If missing, recreate it from `SublarrWiki/docs/superpowers/plans/2026-03-16-wiki-coverage-audit.md` Task 1.

### 2. Run the Feature Audit (Approaches 4+1)

```bash
cd Z:/CC
python wiki_audit_features.py
```

Parses `Sublarr/CHANGELOG.md` + `git log --grep="feat:"` and cross-references every feature against all wiki pages. Output is a Markdown table.

If the script doesn't exist yet: it's at `Z:/CC/wiki_audit_features.py`. Recreate from plan Task 2 if needed.

### 3. Save Combined Report

```bash
cd Z:/CC
python wiki_audit_settings.py > /tmp/s.md && python wiki_audit_features.py > /tmp/f.md
cat /tmp/s.md /tmp/f.md > "SublarrWiki/docs/wiki-gap-report-$(date +%Y-%m-%d).md"
```

### 4. Summarize and Prioritize

Read the combined report and output:

1. **Settings gaps** — list of undocumented config fields grouped by section
2. **Feature gaps** — list of CHANGELOG entries with no wiki page, grouped by version
3. **Priority queue** — top 5 pages to write/update sorted by:
   - User-facing impact (settings > internal)
   - Recency (newer versions first)
   - Complexity (new feature pages > small field additions)

## Expected Output Format

```
## Wiki Audit Results — YYYY-MM-DD

### Settings Coverage: X/143 fields documented

Missing sections:
- **Standalone** (5 fields): standalone_skip_extras, ...
- **Web Player** (2 fields): streaming_enabled, ...

### Feature Coverage: X/Y changelog entries documented

Undocumented features (v0.25+):
- [0.30.0] Standalone NFO metadata integration
- [0.29.0] Web Player streaming endpoint
- ...

### Priority Queue
1. [ ] settings/media-sources.md — add standalone_skip_extras toggle
2. [ ] user-guide/web-player.md — document streaming_enabled + PlayerModal
3. ...
```
```

- [ ] **Step 2: Register skill in MEMORY.md**

Add a reference entry: "Sublarr wiki coverage audit skill is at `~/.claude/skills/sublarr-wiki-audit.md`; run scripts at `Z:/CC/wiki_audit_settings.py` and `Z:/CC/wiki_audit_features.py`."

- [ ] **Step 3: Commit the scripts and plan**

```bash
cd Z:/CC
git add wiki_audit_settings.py wiki_audit_features.py
git add SublarrWiki/docs/superpowers/plans/2026-03-16-wiki-coverage-audit.md
git commit -m "chore: add wiki coverage audit scripts (settings + features)"
```

---

## Execution Order

```
Task 1 → Task 2 → Task 3 → Task 4
(sequential — each builds on prior output)
```

No parallel execution needed — tasks are fast and sequential.
