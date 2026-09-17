# Spotlight Rebrand + Multi-Brand Kit System Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Rename the `brag` Agent Skill to `spotlight` throughout the repo, strip it down to internal-tool scope, add a plan-approval + progress-visibility checkpoint to every run, and add a multi-brand-kit system so users can ingest a brand guideline (PDF/HTML) and apply its colors/fonts/logo/tone/music to future videos, or keep today's default per-project extraction.

**Architecture:** No code, no build system — this is a markdown-driven Claude Code Agent Skill (`SKILL.md` + `references/*.md` + bundled assets), and every change here is either a text/JSON edit to that content or a new convention (a `brands/<slug>/` directory holding a `tokens.json` + generated `brand.md`). Four phases, each producing a working, testable state on its own: (A) mechanical rename, (B) repo cleanup, (C) workflow visibility, (D) brand-kit system built on top of the renamed baseline.

**Tech Stack:** Markdown, JSON. No package.json, no test framework — validation is manual, by invoking `/spotlight` against the `examples/` fixtures and inspecting output, per the spec's Testing section.

**Spec:** `specs/2026-09-16-spotlight-rebrand-design.md` — read it first for the *why* behind every decision below; this plan only covers the *how*.

---

## Phase A: Mechanical rename (`brag` → `spotlight`)

Do this phase first — every later phase edits files that live at the renamed paths.

### Task A1: Rename the skill directory and rewrite SKILL.md

**Files:**
- Rename: `skills/brag/` → `skills/spotlight/` (git mv, preserves history)
- Modify: `skills/spotlight/SKILL.md`

- [ ] **Step 1: Rename the directory**

```bash
git mv skills/brag skills/spotlight
```

- [ ] **Step 2: Edit `skills/spotlight/SKILL.md` — frontmatter and title**

Apply these exact replacements (each appears once; use a targeted Edit, not replace_all, for these):

| Old | New |
|---|---|
| `name: brag` | `name: spotlight` |
| `"/brag", "let's brag about this"` | `"/spotlight", "let's spotlight this"` |
| `# /brag` (the H1, line 6) | `# /spotlight` |
| `You built it. Now let's brag about it.` | `You built it. Now let's put it in the spotlight.` |

- [ ] **Step 3: Edit `skills/spotlight/SKILL.md` — mechanical substitutions (safe as `replace_all` within this file)**

Apply each as a global replace within the file:

| Old | New |
|---|---|
| `` `/brag` `` | `` `/spotlight` `` |
| `/brag` (bare, in the invocation-examples code block and prose) | `/spotlight` |
| `brag-output` | `spotlight-output` |
| `brag-plan.md` | `spotlight-plan.md` |
| `brag.mp4` | `spotlight.mp4` |
| `brag.jpg` | `spotlight.jpg` |

- [ ] **Step 4: Verify no `brag` remains**

```bash
grep -in "brag" skills/spotlight/SKILL.md
```
Expected: no output (empty).

- [ ] **Step 5: Commit**

```bash
git add skills/spotlight/SKILL.md
git commit -m "rename: skills/brag -> skills/spotlight, rewrite SKILL.md for Spotlight"
```

---

### Task A2: Rewrite `references/step-1-inspect.md`

**Files:** Modify `skills/spotlight/references/step-1-inspect.md`

- [ ] **Step 1: One prose rewrite (line 3)**

| Old | New |
|---|---|
| `Read the project directory to understand what you're bragging about.` | `Read the project directory to understand what you're spotlighting.` |

- [ ] **Step 2: Verify**

```bash
grep -in "brag" skills/spotlight/references/step-1-inspect.md
```
Expected: no output.

- [ ] **Step 3: Commit**

```bash
git add skills/spotlight/references/step-1-inspect.md
git commit -m "rename: step-1-inspect.md for Spotlight"
```

---

### Task A3: Rewrite `references/step-2-plan.md`

**Files:** Modify `skills/spotlight/references/step-2-plan.md`

- [ ] **Step 1: Apply these replacements**

| Old | New |
|---|---|
| `` Write `brag-output/brag-plan.md`. `` | `` Write `spotlight-output/spotlight-plan.md`. `` |
| `# Brag Plan: [App Name]` | `# Spotlight Plan: [App Name]` |

- [ ] **Step 2: Verify**

```bash
grep -in "brag" skills/spotlight/references/step-2-plan.md
```
Expected: no output.

- [ ] **Step 3: Commit**

```bash
git add skills/spotlight/references/step-2-plan.md
git commit -m "rename: step-2-plan.md for Spotlight"
```

---

### Task A4: Rewrite `references/step-3-compose.md`

**Files:** Modify `skills/spotlight/references/step-3-compose.md`

This file has the most occurrences (~20) but all are mechanical. Apply each as a global replace within the file, in this order:

| Old | New |
|---|---|
| `skills/brag/` | `skills/spotlight/` |
| `` `/brag` `` | `` `/spotlight` `` |
| `/brag is its own workflow` | `/spotlight is its own workflow` |
| `brag-output` | `spotlight-output` |
| `brag-plan.md` | `spotlight-plan.md` |
| `brag.mp4` | `spotlight.mp4` |
| `brag's single gate` | `spotlight's single gate` |
| `not a hardcoded ` + `` `/brag` `` + ` template` | `not a hardcoded ` + `` `/spotlight` `` + ` template` |

- [ ] **Step 1: Apply the table above**

- [ ] **Step 2: Verify**

```bash
grep -in "brag" skills/spotlight/references/step-3-compose.md
```
Expected: no output. If anything remains, it's a occurrence not covered by the table above — read it in context and fix by hand (e.g. any remaining bare `/brag` in prose not caught above).

- [ ] **Step 3: Commit**

```bash
git add skills/spotlight/references/step-3-compose.md
git commit -m "rename: step-3-compose.md for Spotlight"
```

---

### Task A5: Rewrite `references/step-4-deliver.md`

**Files:** Modify `skills/spotlight/references/step-4-deliver.md`

| Old | New |
|---|---|
| `` `npx hyperframes check`   # brag's single pre-render gate `` | `` `npx hyperframes check`   # spotlight's single pre-render gate `` |
| `` `check` is brag's single pre-render gate `` | `` `check` is spotlight's single pre-render gate `` |
| `brag-output` (every occurrence — directory paths, `../brag.mp4`, `brag.jpg`, `brag-output/composition`, etc.) | `spotlight-output` |
| `brag.mp4` | `spotlight.mp4` |
| `brag.jpg` | `spotlight.jpg` |
| `brag-plan.md` (in "Final output structure") | `spotlight-plan.md` |

Note: this file uses relative filenames like `../brag.mp4` and `brag.jpg` extensively (the ffmpeg commands and "Final output structure" tree) — a `replace_all` of the bare tokens `brag.mp4`, `brag.jpg`, and `brag-output` catches all of them since the file has no unrelated use of those exact strings.

- [ ] **Step 1: Apply the table above**

- [ ] **Step 2: Verify**

```bash
grep -in "brag" skills/spotlight/references/step-4-deliver.md
```
Expected: no output.

- [ ] **Step 3: Commit**

```bash
git add skills/spotlight/references/step-4-deliver.md
git commit -m "rename: step-4-deliver.md for Spotlight"
```

---

### Task A6: Rewrite `references/audio.md`

**Files:** Modify `skills/spotlight/references/audio.md`

| Old | New |
|---|---|
| `skills/brag/assets/` (asset path examples, appears twice — installed-skill path and repo-copy path) | `skills/spotlight/assets/` |
| `~/.claude/skills/brag/assets/` | `~/.claude/skills/spotlight/assets/` |
| `brag-output/composition/assets/` (all occurrences in the asset-copy examples and paths) | `spotlight-output/composition/assets/` |
| `` `/brag` `` (single occurrence, "Hyperframes implementation note") | `` `/spotlight` `` |

- [ ] **Step 1: Apply the table above**

- [ ] **Step 2: Verify**

```bash
grep -in "brag" skills/spotlight/references/audio.md
```
Expected: no output.

- [ ] **Step 3: Commit**

```bash
git add skills/spotlight/references/audio.md
git commit -m "rename: audio.md for Spotlight"
```

---

### Task A7: Confirm `references/tones.md` needs no changes

**Files:** none modified

- [ ] **Step 1: Verify it has zero references to the tool name**

```bash
grep -in "brag" skills/spotlight/references/tones.md
```
Expected: no output (confirmed during planning — this file only discusses tone presets, never self-references the tool). No edit needed; this step is a checkpoint, not a fix.

---

### Task A8: Rewrite bundled asset/script metadata

**Files:**
- Modify: `skills/spotlight/assets/music/README.md`
- Modify: `skills/spotlight/assets/sfx/sfx-analysis.json`
- Modify: `skills/spotlight/scripts/pyproject.toml`
- Modify: `skills/spotlight/scripts/analyze_music_cues.py`
- Modify: `skills/spotlight/scripts/uv.lock` (best-effort — see note)

- [ ] **Step 1: `assets/music/README.md`**

| Old | New |
|---|---|
| `` generated by `skills/brag/scripts/analyze_music_cues.py` and are intended for `/brag` planning `` | `` generated by `skills/spotlight/scripts/analyze_music_cues.py` and are intended for `/spotlight` planning `` |

- [ ] **Step 2: `assets/sfx/sfx-analysis.json`**

| Old | New |
|---|---|
| `"sourceRoot": "skills/brag/assets/sfx"` | `"sourceRoot": "skills/spotlight/assets/sfx"` |

- [ ] **Step 3: `scripts/pyproject.toml`**

| Old | New |
|---|---|
| `name = "brag"` | `name = "spotlight"` |
| `description = "Development tooling for the /brag skill."` | `description = "Development tooling for the /spotlight skill."` |

- [ ] **Step 4: `scripts/analyze_music_cues.py`**

| Old | New |
|---|---|
| `/brag` (3 occurrences, all in comments/docstrings/help text — lines ~5, ~7, ~283 as read during planning) | `/spotlight` |

- [ ] **Step 5: `scripts/uv.lock` — best effort**

Hand-edit the local package's own `name = "brag"` entry to `name = "spotlight"`. If `uv` is available, regenerate properly instead:

```bash
cd skills/spotlight/scripts && uv lock && cd -
```

This is dev-tooling metadata only (used solely if someone runs the optional cue-analysis script) — not required for the skill itself to function. If `uv` isn't installed, the hand-edit is sufficient; don't block on this step.

- [ ] **Step 6: Verify**

```bash
grep -rin "brag" skills/spotlight/assets/music/README.md skills/spotlight/assets/sfx/sfx-analysis.json skills/spotlight/scripts/pyproject.toml skills/spotlight/scripts/analyze_music_cues.py
```
Expected: no output. (Skip `uv.lock` in this grep — it may still contain incidental hash/metadata noise unrelated to the name field.)

- [ ] **Step 7: Commit**

```bash
git add skills/spotlight/assets/music/README.md skills/spotlight/assets/sfx/sfx-analysis.json skills/spotlight/scripts/pyproject.toml skills/spotlight/scripts/analyze_music_cues.py skills/spotlight/scripts/uv.lock
git commit -m "rename: bundled asset/script metadata for Spotlight"
```

---

### Task A9: Recreate the per-agent discovery symlinks

**Files:**
- Remove: `.claude/skills/brag`, `.agents/skills/brag`, `.opencode/skills/brag` (all tracked symlinks — confirmed via `git ls-files`)
- Create: `.claude/skills/spotlight`, `.agents/skills/spotlight`, `.opencode/skills/spotlight`
- Modify: `.gitattributes`

- [ ] **Step 1: Remove old symlinks and create new ones pointing at the renamed skill**

```bash
git rm .claude/skills/brag .agents/skills/brag .opencode/skills/brag
ln -s ../../skills/spotlight .claude/skills/spotlight
ln -s ../../skills/spotlight .agents/skills/spotlight
ln -s ../../skills/spotlight .opencode/skills/spotlight
git add .claude/skills/spotlight .agents/skills/spotlight .opencode/skills/spotlight
```

- [ ] **Step 2: Verify each symlink resolves correctly**

```bash
ls -la .claude/skills/ .agents/skills/ .opencode/skills/
readlink .claude/skills/spotlight .agents/skills/spotlight .opencode/skills/spotlight
```
Expected: each `readlink` prints `../../skills/spotlight`, and `skills/spotlight/SKILL.md` is reachable through each symlink (spot-check: `cat .claude/skills/spotlight/SKILL.md | head -3` should show the new frontmatter).

- [ ] **Step 3: Update `.gitattributes`**

Replace the full contents of `.gitattributes`:

```
# Symlinks — never subject to text/EOL normalization
.agents/skills/spotlight -text
.claude/skills/spotlight -text
.opencode/skills/spotlight -text
```

- [ ] **Step 4: Commit**

```bash
git add .gitattributes
git commit -m "rename: per-agent skill symlinks brag -> spotlight"
```

---

### Task A10: Update plugin manifests

**Files:**
- Modify: `.claude-plugin/plugin.json`
- Modify: `.claude-plugin/marketplace.json`

- [ ] **Step 1: Rewrite `.claude-plugin/plugin.json`**

Replace the full file contents. `homepage`/`repository` are dropped rather than pointed at a placeholder, since the new GitHub repo doesn't exist yet — add them back once it does. `author` is updated to reflect actual internal ownership (the original MIT copyright notice in `LICENSE` is untouched — see Task B2).

```json
{
  "name": "spotlight",
  "description": "Turn the current project into a short, polished, shareable launch video using HyperFrames.",
  "version": "0.1.0",
  "author": {
    "name": "Sana Riaz",
    "email": "sanariaz154@gmail.com"
  },
  "license": "MIT",
  "keywords": ["video", "launch", "hyperframes", "skill", "branding"]
}
```

- [ ] **Step 2: Rewrite `.claude-plugin/marketplace.json`**

Replace the full file contents:

```json
{
  "name": "spotlight",
  "description": "You built it. Now put it in the spotlight. Turn the project you just shipped into a short, shareable launch video with one command.",
  "owner": {
    "name": "Sana Riaz",
    "email": "sanariaz154@gmail.com"
  },
  "plugins": [
    {
      "name": "spotlight",
      "source": "./",
      "description": "Turn the current project website into a short, polished, shareable launch video using HyperFrames."
    }
  ]
}
```

- [ ] **Step 3: Verify both are valid JSON**

```bash
python3 -m json.tool .claude-plugin/plugin.json > /dev/null && python3 -m json.tool .claude-plugin/marketplace.json > /dev/null && echo "both valid"
```
Expected: `both valid`

- [ ] **Step 4: Commit**

```bash
git add .claude-plugin/plugin.json .claude-plugin/marketplace.json
git commit -m "rename: plugin manifests brag -> spotlight, update ownership metadata"
```

---

### Task A11: Update `.gitignore` and final repo-wide sweep

**Files:** Modify `.gitignore`

- [ ] **Step 1: Update the render-artifact ignore pattern**

| Old | New |
|---|---|
| `**/brag-output*/` | `**/spotlight-output*/` |

- [ ] **Step 2: Commit**

```bash
git add .gitignore
git commit -m "rename: gitignore pattern brag-output -> spotlight-output"
```

- [ ] **Step 3: Repo-wide verification sweep**

```bash
grep -ril "brag" skills/ .claude/ .agents/ .opencode/ .claude-plugin/ .gitignore .gitattributes 2>/dev/null
```
Expected: no output. (This intentionally excludes `examples/`, `LICENSE`, `README.md`, `PRODUCT.md`, `docs/`, and `specs/` — `examples/` was already confirmed to have zero brag references during planning; `LICENSE` keeps the original copyright notice on purpose; `README.md`/`PRODUCT.md`/`docs/` are handled in Phase B, which removes or rewrites them.)

If this command prints anything, stop and fix it before moving to Phase B — Phase D adds new content on top of these same files and a missed rename here will compound.

---

## Phase B: Repo cleanup

### Task B1: Remove the marketing site and its brief

**Files:**
- Delete: `docs/` (entire directory)
- Delete: `PRODUCT.md`
- Delete: `scripts/check-docs.mjs`

- [ ] **Step 1: Remove the three items**

```bash
git rm -r docs/
git rm PRODUCT.md
git rm scripts/check-docs.mjs
```

- [ ] **Step 2: Verify `scripts/` is now empty or removed if nothing else lives there**

```bash
ls scripts/ 2>/dev/null || echo "scripts/ removed"
```
If `scripts/` is now empty, remove the directory too (git doesn't track empty dirs, so it'll already be gone once the last file is removed).

- [ ] **Step 3: Commit**

```bash
git commit -m "cleanup: remove marketing site (docs/), PRODUCT.md, check-docs.mjs — internal tool scope only"
```

---

### Task B2: Rewrite README.md as a lean usage guide

**Files:** Modify `README.md`

- [ ] **Step 1: Replace the full contents of `README.md`**

```markdown
# /spotlight

**You built it. Now put it in the spotlight.**

`/spotlight` is a Claude Code skill that turns the project you created into a short, shareable launch video — music, motion, and share copy included. One command, powered by [Hyperframes](https://hyperframes.heygen.com/).

This is an internal fork of [`latent-spaces/brag`](https://github.com/latent-spaces/brag) (MIT), renamed and extended with a multi-brand-kit system. See `LICENSE` for the original copyright notice.

## Install

Copy the skill directly:

```bash
rsync -a --exclude '.DS_Store' skills/spotlight/ ~/.claude/skills/spotlight/
```

Restart Claude Code after copying.

### Also works with

This repo exposes the skill at every agent's standard discovery path via symlinks. No extra config needed.

| Agent | How it discovers |
|---|---|
| **opencode** | Auto-detects from `.opencode/skills/spotlight/` at project root |
| **Codex CLI** | Reads `.agents/skills/spotlight/`, walking up to repo root |
| **Claude Code** | Also reads `.claude/skills/spotlight/` |
| **Other agents** | Point custom instructions at `skills/spotlight/SKILL.md` |

> **Windows users:** Git requires `git config core.symlinks true` (or `git clone -c core.symlinks=true`) and Windows Developer Mode or Administrator privileges to create symlinks. If symlinks don't work on your system, copy `skills/spotlight/` to the agent's skill directory manually instead.

## Use it

From any project directory, ask your agent:

```text
let's /spotlight
```

Or steer the tone:

```text
/spotlight --tone "fake Series A launch from 2016"
```

Apply a saved brand kit instead of extracting colors/fonts from the project:

```text
/spotlight --brand acme
```

Add a new brand kit from a PDF or HTML brand guideline:

```text
/spotlight brand add ./acme-brand-guide.pdf --name "Acme"
```

List saved brand kits:

```text
/spotlight brand list
```

Voiceover is off by default. Enable it explicitly with:

```text
/spotlight --voice
```

Narration uses Kokoro through Hyperframes when enabled.

After the plan is written, Spotlight pauses and waits for you to approve, edit, or redirect it before composing anything — see "How it works" below.

You get a `spotlight-output/` folder with the plan, a composition brief, share copy, and the rendered `spotlight.mp4`.

## How it works

`/spotlight` owns the story — the product angle, tone, and which moments to show, plus (optionally) a saved brand kit's colors, fonts, logo, and pinned tone. It hands a focused brief to [Hyperframes](https://hyperframes.heygen.com/), which builds, times, and renders the video.

The pipeline is four steps — inspect, plan, compose, deliver — and pauses once, after the plan is written, for your review.

## Brand kits

See `skills/spotlight/references/brand-kits.md` for the full schema and ingestion flow. Short version: `brands/<slug>/tokens.json` holds a brand's colors, fonts, logo, pinned tone/music, and do's/don'ts; `/spotlight brand add` ingests one from a PDF or HTML guideline; `/spotlight --brand <slug>` applies one to a run. Omit `--brand` and Spotlight extracts visual identity from the project being spotlighted, same as it always has.

## Requirements

- An agent that supports Agent Skills — Claude Code, opencode, Codex CLI, or any agent with custom instructions (see "Also works with" above)
- Node.js 22+
- FFmpeg on `PATH`
- Hyperframes CLI — `npx hyperframes` (check it with `npx hyperframes doctor`)

## What's in this repo

- `skills/spotlight/` — the skill, references, and bundled music + SFX
- `brands/` — saved brand kits (colors, fonts, logo, pinned tone/music per brand)
- `examples/` — fake product sites used as a benchmark suite
- `.claude-plugin/` — plugin manifest + marketplace catalog
- `.claude/skills/spotlight/` — symlink → `skills/spotlight/` (Claude Code discovery)
- `.agents/skills/spotlight/` — symlink → `skills/spotlight/` (Codex CLI + opencode discovery)
- `.opencode/skills/spotlight/` — symlink → `skills/spotlight/` (opencode discovery)

## Credits

- Music — [ende.app](https://ende.app/en) "Happy Beats / Business Moves"
- Sound effects — [Kenney](https://kenney.nl/)
- Video generation — [Hyperframes](https://hyperframes.heygen.com/)
- Fake demo sites — built with [Impeccable](https://impeccable.style/)
- Originally built as [`brag`](https://github.com/latent-spaces/brag) by Shunit Haviv Hakimi
```

- [ ] **Step 2: Commit**

```bash
git add README.md
git commit -m "docs: condense README to internal usage guide, drop marketing copy"
```

---

## Phase C: Workflow visibility improvements

These apply regardless of brand-kit usage — general workflow behavior, edited into `SKILL.md`.

### Task C1: Add the plan-approval checkpoint

**Files:** Modify `skills/spotlight/SKILL.md`

- [ ] **Step 1: Find the "Step 2: Plan and storyboard" section and replace its Gate line**

Current (after Phase A rename):
```markdown
**Gate:** `<output-dir>/spotlight-plan.md` exists with a full storyboard. Scene durations sum to 15–25 seconds.
```

Replace with:
```markdown
**Gate:** `<output-dir>/spotlight-plan.md` exists with a full storyboard. Scene durations sum to 15–25 seconds. **Then stop and present the plan to the user — do not proceed to Step 3 until they respond.** They may approve as-is, request specific edits, or supply their own narrative direction outright. Revise and re-present until they explicitly approve. Only then continue.
```

- [ ] **Step 2: Verify the edit landed**

```bash
grep -A3 "Gate.*spotlight-plan.md.*exists with a full storyboard" skills/spotlight/SKILL.md
```
Expected: shows the new sentence about stopping and waiting for the user.

- [ ] **Step 3: Commit**

```bash
git add skills/spotlight/SKILL.md
git commit -m "feat: add plan-approval checkpoint after Step 2"
```

---

### Task C2: Add step-progress status lines, including the blocked/waiting distinction

**Files:** Modify `skills/spotlight/SKILL.md`

- [ ] **Step 1: Add a new section right after "## Output directory" and before "## Step 1: Inspect the project"**

```markdown
## Progress visibility

The full pipeline can take several minutes, dominated by Step 3 (compose) and Step 4 (render). Announce a short status line at the start of each step so the user always knows where a run stands:

- `Step 1/4 — Inspecting project...`
- `Step 2/4 — Planning and storyboarding...`
- `Step 3/4 — Composing with Hyperframes (usually the slowest step)...`
- `Step 4/4 — Rendering...`

**Step 2's status line is different in kind, not just wording, once the plan is written and the checkpoint from "Step 2: Plan and storyboard" is reached — it reads as blocked, not in-progress:**

```
Step 2/4 — Plan ready, waiting on your review. Approve, request edits, or add your own direction before I continue.
```

This is the only point in the pipeline that stops and waits on the user today. Never let a "waiting on you" state read like an in-progress status line — the user needs to be able to tell "still working" apart from "needs your input" at a glance.

During Step 4, relay the `npx hyperframes render` subprocess's own progress output live as it streams, rather than staying silent until it finishes.
```

- [ ] **Step 2: Verify**

```bash
grep -n "Progress visibility" skills/spotlight/SKILL.md
```
Expected: one match.

- [ ] **Step 3: Commit**

```bash
git add skills/spotlight/SKILL.md
git commit -m "feat: add step-progress status lines with blocked-vs-running distinction"
```

---

## Phase D: Multi-brand kit system

Builds on the renamed baseline from Phases A–C.

### Task D1: Scaffold the `brands/` directory

**Files:** Create `brands/README.md`

- [ ] **Step 1: Create `brands/README.md`**

```markdown
# Brand kits

Each subdirectory here is one saved brand kit — colors, fonts, logo, pinned tone/music, and do's/don'ts, ingested from a PDF or HTML brand guideline.

- Add one: `/spotlight brand add <path-to-pdf-or-html> --name "Acme"`
- List them: `/spotlight brand list`
- Use one: `/spotlight --brand <slug>`

Schema and ingestion details: `../skills/spotlight/references/brand-kits.md`.
```

- [ ] **Step 2: Commit**

```bash
git add brands/README.md
git commit -m "feat: scaffold brands/ directory"
```

---

### Task D2: Write `references/brand-kits.md`

**Files:** Create `skills/spotlight/references/brand-kits.md`

- [ ] **Step 1: Create the file**

```markdown
# Brand kits

A brand kit is a saved visual/creative identity — colors, fonts, logo, a pinned tone preset, a pinned music track, and do's/don'ts — that a `/spotlight` run can apply instead of extracting identity fresh from the project being spotlighted.

Default behavior (no `--brand` flag) is unchanged: extract colors and fonts from the inspected project, same as always. Brand kits are opt-in.

## Storage

```
brands/
  <slug>/
    tokens.json          # source of truth
    brand.md             # human-readable, generated from tokens.json — never hand-authored
    source/
      <original-upload>  # the PDF/HTML the brand was ingested from
    assets/
      logo/logo.<ext>
      fonts/<display-font-file>
      fonts/<body-font-file>
      music/<custom-track-file>   # only if tokens.json audio.musicTrack points here
```

## `tokens.json` schema

```json
{
  "name": "Acme",
  "slug": "acme",
  "colors": {
    "background": "#...",
    "text": "#...",
    "accent": "#...",
    "secondary": "#..."
  },
  "fonts": {
    "display": { "family": "...", "source": "bundled|google-fonts", "file": "assets/fonts/Display-Bold.woff2" },
    "body":    { "family": "...", "source": "bundled|google-fonts", "file": "assets/fonts/Body-Regular.woff2" }
  },
  "logo": { "file": "assets/logo/logo.svg", "placement": "outro" },
  "tone": { "preset": "polished", "voiceNotes": "..." },
  "audio": { "musicTrack": "assets/music/<file> | <bundled filename>" },
  "doNots": ["never stretch the logo", "never use pure black backgrounds"],
  "source": { "importedFrom": "brands/acme/source/<file>", "importedAt": "YYYY-MM-DD" }
}
```

`fonts.*.file` is present only when `source: "bundled"` (a real font file was supplied). When `source: "google-fonts"`, the composition references it via a Google Fonts `<link>` instead — same mechanism used for per-project font extraction today.

`logo.placement` is reserved for future values; only `"outro"` is implemented today — ingestion always writes `"outro"` and never prompts for this field.

`brand.md` is regenerated from `tokens.json` whenever the JSON is created or edited. It exists purely so a human can skim a brand kit without reading JSON — it is documentation, never the source of truth.

## Ingestion: `/spotlight brand add <path> --name "Acme"`

This is the one part of the Spotlight workflow that's expected to ask the user questions — a brand PDF/HTML is unstructured input, and this happens once per brand, not once per video.

1. Read the file. Use the pdf skill for a PDF; read HTML directly.
2. Extract what's extractable: colors, font family names, tone-of-voice language, an embedded logo image if present.
3. Ask the user to fill any gaps the source can't supply. Most brand PDFs won't embed a usable web font file — ask for a font file path, or confirm a Google Fonts fallback by family name. Same for a clean logo asset if none was embeddable.
4. Ask which tone preset (see `tones.md`) and, optionally, which music track (see `audio.md`) this brand should default to.
5. Write `brands/<slug>/tokens.json` and generate `brands/<slug>/brand.md` from it. Copy the source file into `brands/<slug>/source/` and any supplied assets (font files, logo, custom music) into `brands/<slug>/assets/`.

`/spotlight brand list` enumerates `brands/*/tokens.json` and prints each brand's `name` and `slug`.

## Selection and override

- New invocation flag: `--brand <slug>`.
- Omitted → unchanged default: extract colors/fonts from the inspected project, no logo, no pinned tone/music.
- When present, the brand's pinned tone/music are defaults for that run — **explicit per-run flags always win.** `/spotlight --brand acme --tone chaotic` uses Acme's palette/fonts/logo with `chaotic` pacing for just that run; the saved brand kit itself is never modified by a run.
- Invalid `--brand <slug>` (no matching `brands/<slug>/` directory): stop with an error and list available brands (run the same lookup as `brand list`). Do not silently fall back to default extraction — that would produce an un-branded video when the user asked for a specific brand.

## Frame-only styling scoping

When a brand is active, scoping applies to **both colors and fonts** — not colors alone. The point is that the video still reads as the specific product being spotlighted, which breaks just as badly from a silently swapped font as from a swapped color.

- **Frame** (backgrounds, headline/text treatment, transitions, outro, logo) uses the brand's colors and fonts.
- **Recreated product UI** — the app screens/components the video shows, *or* the recreated hero/UI element used as the strongest-visual fallback for landing-page-only projects (see `step-2-plan.md`, "Choosing what to show") — keeps the real product's own extracted colors and fonts, so it still reads as that specific product rather than being fully re-skinned. This applies whether or not the project has a real app flow: "product UI" means any recreated material sourced directly from the product itself, not just app screens.

## Tone resolution

If `--brand` is active and the user did not pass `--tone` explicitly, use the brand's pinned `tone.preset` instead of inferring one from the project (the normal Step 1 rubric, question 6). An explicit `--tone` flag always overrides the brand's pin.

## What reaches the composition brief

`composition-brief.md`'s Visual Identity section splits the same way the plan does — both colors and fonts, for both the brand's frame values and the product's own reference values — so Hyperframes actually builds from the full picture, not a brand-only block. The brief also instructs Hyperframes to `@font-face` the brand's font files (copied into `composition/assets/fonts/`) and composite the logo at the outro (`composition/assets/logo/`). Pinned music is copied in unless overridden. The brand kit's `doNots` and `tone.voiceNotes` are carried into the brief verbatim as creative-direction constraints, the same way `/spotlight` hands Hyperframes any other tone/handoff guidance — high-level direction, not implementation detail.

## Error handling

| Case | Behavior |
|---|---|
| `--brand <slug>` doesn't match any `brands/` entry | Stop with an error, list available brands. Do not fall back to default extraction. |
| Brand PDF/HTML yields no extractable colors/fonts | Ingestion asks the user directly for each missing field rather than guessing. |
| Brand kit has no pinned music track | Falls back to the tool's existing default music-selection logic for that run. |
| Brand kit has a font family name but no font file | Composition uses a Google Fonts `<link>` reference instead of a local `@font-face`. |
| Brand colors fail the WCAG contrast gate | Flagged the same as any other contrast failure — no special-casing for brand colors. |
```

- [ ] **Step 2: Verify the file is well-formed markdown and the JSON block is valid**

```bash
python3 -c "
import json, re
content = open('skills/spotlight/references/brand-kits.md').read()
m = re.search(r'\`\`\`json\n(.*?)\n\`\`\`', content, re.DOTALL)
json.loads(m.group(1))
print('tokens.json example is valid JSON')
"
```
Expected: `tokens.json example is valid JSON`

- [ ] **Step 3: Commit**

```bash
git add skills/spotlight/references/brand-kits.md
git commit -m "feat: add references/brand-kits.md — schema and ingestion flow"
```

---

### Task D3: Wire `--brand` into `SKILL.md`'s invocation dispatch and flag table

**Files:** Modify `skills/spotlight/SKILL.md`

- [ ] **Step 1: Add `--brand` to the options table**

Find:
```markdown
| `--voice` | flag | narration off |
```
Insert a new row immediately after it:
```markdown
| `--brand` | saved brand slug | none (extract from project) |
```

- [ ] **Step 2: Add brand-subcommand dispatch, right after the existing "Invocation dispatch (must happen first)" section**

Insert a new section after that one, before "## Output directory":

```markdown
## Brand subcommands

If the invocation is `/spotlight brand add <path> --name "<Name>"` or `/spotlight brand list`, this is not a video-generation run — read [references/brand-kits.md](references/brand-kits.md) and follow its ingestion or listing flow instead of Step 1–4 below.

Otherwise, if `--brand <slug>` is present on a normal invocation, read [references/brand-kits.md](references/brand-kits.md) before Step 1 — it changes how Step 1 sources colors/fonts, how Step 2 fills Visual Identity, and how Step 3 builds the composition brief.
```

- [ ] **Step 3: Verify**

```bash
grep -n "\-\-brand" skills/spotlight/SKILL.md
grep -n "Brand subcommands" skills/spotlight/SKILL.md
```
Expected: both produce matches.

- [ ] **Step 4: Commit**

```bash
git add skills/spotlight/SKILL.md
git commit -m "feat: wire --brand flag and brand subcommands into SKILL.md dispatch"
```

---

### Task D4: Add brand-aware branch to Step 1 (inspect)

**Files:** Modify `skills/spotlight/references/step-1-inspect.md`

- [ ] **Step 1: Add a new subsection right after "## Color extraction" and before "## Font extraction"**

```markdown
## Brand-aware sourcing (when `--brand` is active)

If a brand kit is active, do not use the project's own colors/fonts for the frame — load them from `brands/<slug>/tokens.json` instead (see `brand-kits.md`). Still extract the project's own real UI colors *and* fonts as described above and below — they're needed separately, for the frame-only scoping rule that keeps recreated product UI looking like the actual product. Content, copy, and flow extraction (questions 1–5, 7–9) are unchanged either way.

Tone resolution also changes: if `--brand` is active and the user did not pass `--tone` explicitly, use the brand's pinned `tone.preset` for question 6 instead of inferring one. An explicit `--tone` flag still overrides the brand's pin.
```

- [ ] **Step 2: Verify**

```bash
grep -n "Brand-aware sourcing" skills/spotlight/references/step-1-inspect.md
```
Expected: one match.

- [ ] **Step 3: Commit**

```bash
git add skills/spotlight/references/step-1-inspect.md
git commit -m "feat: add brand-aware sourcing branch to Step 1"
```

---

### Task D5: Split "Visual identity" in Step 2's plan template

**Files:** Modify `skills/spotlight/references/step-2-plan.md`

- [ ] **Step 1: Replace the "Visual identity" section of the `spotlight-plan.md` template**

Find:
```markdown
## Visual identity (from the project)
- Background: [exact color value]
- Accent: [exact color value]
- Text: [exact color value]
- Display font: [name]
- Body font: [name]
- Strongest visual element: [what from the site to reference]
```

Replace with:
```markdown
## Visual identity

If no brand kit is active, fill only "Project identity" below and skip "Brand identity (frame)".

### Brand identity (frame) — only when `--brand` is active
- Background: [exact color value from the brand kit]
- Accent: [exact color value from the brand kit]
- Text: [exact color value from the brand kit]
- Display font: [from the brand kit]
- Body font: [from the brand kit]
- Logo: [brand kit logo file, composited at outro]

### Project identity (for recreated product UI, and for the frame when no brand is active)
- Background: [exact color value from the project]
- Accent: [exact color value from the project]
- Text: [exact color value from the project]
- Display font: [name, from the project]
- Body font: [name, from the project]
- Strongest visual element: [what from the site to reference]
```

- [ ] **Step 2: Verify**

```bash
grep -n "Brand identity (frame)" skills/spotlight/references/step-2-plan.md
```
Expected: one match.

- [ ] **Step 3: Commit**

```bash
git add skills/spotlight/references/step-2-plan.md
git commit -m "feat: split Visual identity into brand/project sections in plan template"
```

---

### Task D6: Wire brand values into Step 3's composition brief template

**Files:** Modify `skills/spotlight/references/step-3-compose.md`

- [ ] **Step 1: Replace the "## Visual Identity" section of the `composition-brief.md` template**

Find:
```markdown
## Visual Identity
- Background: [exact value from project]
- Text: [exact value from project]
- Accent: [exact value from project]
- Display font: [font or fallback decision]
- Body font: [font or fallback decision]
- Visual references from the project: [short list]
```

Replace with:
```markdown
## Visual Identity

Splits the same way `spotlight-plan.md` does — carry both colors and fonts for each side into this brief. If no brand kit is active, fill only "Project identity".

### Brand identity (frame) — only when `--brand` is active
- Background / Text / Accent: [exact values from the brand kit]
- Display font / Body font: [from the brand kit — reference the actual font file path under `composition/assets/fonts/` if `source: "bundled"`, or note the Google Fonts family if `source: "google-fonts"`]
- Logo: [path under `composition/assets/logo/`, composited at the outro]
- Brand constraints: [the brand kit's `doNots`, verbatim]
- Brand voice notes: [the brand kit's `tone.voiceNotes`, verbatim]

### Project identity (for recreated product UI, and for the frame when no brand is active)
- Background: [exact value from project]
- Text: [exact value from project]
- Accent: [exact value from project]
- Display font: [font or fallback decision]
- Body font: [font or fallback decision]
- Visual references from the project: [short list]
```

- [ ] **Step 2: Add asset-copying instructions for brand fonts/logo, right after the existing "## Audio asset preparation" section**

Insert a new section after it:

```markdown
---

## Brand asset preparation (when `--brand` is active)

Copy the brand kit's font files and logo into the composition before building it:

```bash
mkdir -p <output-dir>/composition/assets/fonts <output-dir>/composition/assets/logo
cp brands/<slug>/assets/fonts/*.woff2 <output-dir>/composition/assets/fonts/
cp brands/<slug>/assets/logo/logo.* <output-dir>/composition/assets/logo/
```

If the brand kit pins a music track under `brands/<slug>/assets/music/`, copy it the same way the bundled tracks are copied (see "Audio asset preparation" above) unless the user's invocation overrides it.
```

- [ ] **Step 3: Verify**

```bash
grep -n "Brand identity (frame)" skills/spotlight/references/step-3-compose.md
grep -n "Brand asset preparation" skills/spotlight/references/step-3-compose.md
```
Expected: both produce matches.

- [ ] **Step 4: Commit**

```bash
git add skills/spotlight/references/step-3-compose.md
git commit -m "feat: wire brand identity and asset copying into composition brief template"
```

---

### Task D7: End-to-end validation with a sample brand kit

**Files:**
- Create (temporary, for the ingestion test): `/tmp` scratch HTML fixture (not committed)
- Create: `brands/example/` (tokens.json, brand.md, source/, assets/) — committed as a worked example

- [ ] **Step 1: Create a minimal test brand HTML page to ingest against**

Write to a scratch path (e.g. the session's scratchpad directory, not inside the repo):

```html
<!DOCTYPE html>
<html>
<head>
<style>
  :root {
    --background: #0b1220;
    --text: #f5f7fa;
    --accent: #3ea6ff;
    --secondary: #1f2a44;
    font-family: 'Space Grotesk', sans-serif;
  }
  body { font-family: 'Inter', sans-serif; }
</style>
</head>
<body>
  <h1>Exampleco brand guidelines</h1>
  <p>Voice: confident, plain-spoken, never salesy.</p>
  <p>Never stretch the logo. Never place it on a busy background.</p>
</body>
</html>
```

- [ ] **Step 2: Follow `references/brand-kits.md`'s ingestion flow by hand against this fixture**

Simulate `/spotlight brand add <fixture-path> --name "Exampleco"`: extract `background: #0b1220`, `text: #f5f7fa`, `accent: #3ea6ff`, `secondary: #1f2a44`, display font `Space Grotesk` (source: google-fonts, no file supplied), body font `Inter` (source: google-fonts), `doNots: ["never stretch the logo", "never place it on a busy background"]`. No logo was embeddable — note that gap rather than inventing one; leave `logo.file` unset for this example kit and drop the `logo` key entirely from `tokens.json`, since D6's brief-writing step already treats brand identity as optional. Pick a tone preset (`polished` fits the extracted voice) and skip pinning a music track (falls back to default selection, per the error-handling table).

- [ ] **Step 3: Write `brands/example/tokens.json`, `brands/example/brand.md`, and `brands/example/source/brand-guidelines.html`** (the fixture from Step 1, copied in)

Follow the schema in `references/brand-kits.md` exactly. `brand.md` should be a short human-readable summary generated from the JSON — name, colors as a swatch-style list, fonts, tone, do's/don'ts.

- [ ] **Step 4: Run `/spotlight --brand example` against one `examples/` fixture (e.g. `examples/horse-tinder/`) up through the Step 2 plan-approval checkpoint**

Confirm by inspection:
- `spotlight-plan.md`'s Visual Identity section has both a "Brand identity (frame)" block (Exampleco's colors/fonts) and a "Project identity" block (Horse Tinder's own colors/fonts, per `examples/horse-tinder/styles.css`).
- The run actually stops and presents the plan — it does not proceed to Step 3 on its own.
- The Step 2/4 status line reads as "waiting on your review," distinct from the Step 1/3/4 "in progress" lines.

You don't need to run this all the way through Hyperframes composition/render for this validation — confirming the plan-stage output is correct is sufficient evidence the brand-kit wiring works; Steps 3–4 were already covered by the existing `/brag` pipeline before this rename and aren't being changed in behavior beyond what Tasks D3/D6 add.

- [ ] **Step 5: Confirm the two documented error paths behave as specified**

- `/spotlight --brand does-not-exist` against the same fixture: confirm it stops with an error and lists available brands (including `example`), rather than silently falling back to project-only extraction.
- Confirm the "font family but no font file" path: `example`'s fonts are both `source: "google-fonts"` with no `file` — confirm Step 3's brief-writing instructions (Task D6) correctly produce a Google Fonts reference rather than a broken `@font-face` for this case, by inspection of what `composition-brief.md` would contain.

- [ ] **Step 6: Commit**

```bash
git add brands/example/
git commit -m "test: add brands/example/ as a worked brand-kit fixture, validate end-to-end wiring"
```

---

## Final check across all phases

- [ ] **Run the full repo-wide sweep one more time now that Phase D has added new content**

```bash
grep -ril "brag" skills/ .claude/ .agents/ .opencode/ .claude-plugin/ brands/ .gitignore .gitattributes README.md 2>/dev/null
```
Expected: no output.

```bash
git log --oneline -25
git status
```
Expected: a clean sequence of the commits from this plan, working tree clean.
