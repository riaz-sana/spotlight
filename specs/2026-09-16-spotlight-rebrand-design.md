# Spotlight: internal rebrand + multi-brand kit system — design

**Status:** Approved by user via conversation, pending written spec review
**Date:** 2026-09-16

## Background

This repo is currently `brag`, an MIT-licensed Claude Code Agent Skill (upstream:
`latent-spaces/brag`) that turns a project's own source code into a short (15-25s)
shareable launch video, via the external Hyperframes framework. It has no build
system, no `package.json`, no rendering code of its own — it is a markdown-driven
agent workflow (`SKILL.md` + `references/*.md`) plus bundled music/SFX assets.

The repo also contains an unrelated static marketing website (`docs/`) that
promotes `brag` itself, and today has no persistent branding system: colors and
fonts are extracted fresh from whatever project is being "bragged about" on every
run, with nothing saved or reusable between runs.

This document specs three changes, to be shipped together since they touch
overlapping files:

1. Rename/rebrand the tool as an internal asset: **Spotlight**.
2. Add a multi-brand-kit system: users can ingest a brand guideline (PDF/HTML),
   save it, and select it on future runs — or keep today's default behavior of
   extracting visual identity from the inspected project.
3. Add a plan-approval and progress-visibility checkpoint, and strip the repo
   down to internal-tool scope (remove the marketing site, keep a lean usage
   README).

## Goals

- Users can pick a saved brand kit or fall back to today's per-project extraction.
- A brand kit carries colors, fonts (incl. actual font files), a logo, a pinned
  tone preset, and a pinned music track — the "full profile" scope, not just
  color/font tokens.
- When a brand is active, the brand skins the **frame** (backgrounds, text,
  transitions, outro, logo); recreated product UI keeps the real product's own
  colors *and fonts* so the video still reads as that product.
- Every run pauses once, after the plan is written, for explicit user approval —
  the user can approve, edit, or supply their own narrative direction before
  anything is composed or rendered.
- The user can always see roughly where a run stands — which of the 4 steps is
  active, and live progress during the slow render step — rather than the
  agent going quiet for minutes at a time.
- The repo works as a clean internal tool: no marketing site, no upstream-facing
  copy, lean README aimed at users of the tool, not visitors to a launch page.
- No new dependencies, no build step — stays consistent with the existing
  markdown/JSON-convention style of the repo.

## Non-goals

- No AI-generated music — needle-drop only (bundled or user-supplied tracks),
  unchanged from today.
- No change to the underlying Hyperframes composition/render pipeline itself —
  Spotlight still owns only planning/briefing, same division of responsibility
  as `brag` does today.
- No automated test suite / CI — this repo has none today and none is being
  added; validation stays manual (see Testing below).
- `examples/` (the 5 fake product test-fixture sites) stays as-is, unrenamed
  internally — confirmed with user as useful regardless of the marketing-site
  cleanup.

## Naming and rename scope

New name: **Spotlight**. Invocation becomes `/spotlight` (natural-language
triggers like "spotlight this," "make a launch video" also still work, same
pattern as today's `SKILL.md` description field).

Renames required:

| Old | New |
|---|---|
| `skills/brag/` | `skills/spotlight/` |
| `.claude/skills/brag` (symlink) | `.claude/skills/spotlight` |
| `.agents/skills/brag` (symlink) | `.agents/skills/spotlight` |
| `.opencode/skills/brag` (symlink) | `.opencode/skills/spotlight` |
| `SKILL.md` frontmatter `name: brag` | `name: spotlight` (description/trigger phrases updated to match) |
| `.claude-plugin/plugin.json`, `marketplace.json` | id/name updated to `spotlight` |
| `brag-output/` (default output dir) | `spotlight-output/` |
| `brag-plan.md` | `spotlight-plan.md` |
| `brag.mp4` / `brag.jpg` | `spotlight.mp4` / `spotlight.jpg` |
| `--tone`/flag table, all `/brag ...` examples in docs | `/spotlight ...` |

`composition-brief.md` and `share-copy.txt` keep their names (generic, not
brand-specific).

The table above covers concrete artifacts (paths, filenames, flags). The
rename also includes a full prose find-and-replace across every file under
`skills/spotlight/**`: the reference docs refer to the tool by name
throughout ("brag's single gate," "the brag video," "/brag owns the product
angle," etc.) and all such self-references become "Spotlight"/"/spotlight."

**Repo/folder detachment (confirmed in scope, executed at implementation time,
not during spec-writing):**

`origin` currently points at the *upstream* open-source repo
(`https://github.com/latent-spaces/brag.git`, MIT-licensed, not owned by this
user) — not a fork. As part of implementation:
- Create a new **private** GitHub repo under the user's personal account
  (exact name to be confirmed at execution time — expected `spotlight`) and
  repoint `origin` to it, detaching from `latent-spaces/brag`.
- Rename the on-disk project folder (`side-projects/brag` →
  `side-projects/spotlight`) — done last, after other changes are committed,
  since it changes the working directory path mid-session.

Both are git-remote/filesystem actions with real blast radius (a new GitHub
repo is a shared, externally-visible artifact; a folder rename affects the
running session's own working directory) — re-confirm immediately before
executing each, do not run them silently as part of a larger batch of file
edits.

**License/attribution**: `LICENSE` (MIT, copyright Shunit Haviv Hakimi) stays
unchanged and intact — renaming and modifying the tool does not remove the
obligation to retain the original copyright/permission notice. README gets a
brief acknowledgment that Spotlight is an internal fork of `latent-spaces/brag`.

## Repo cleanup

Remove (marketing-site-only, confirmed to have zero shared code with the skill):
- `docs/` (entire GitHub Pages site)
- `PRODUCT.md` (design brief for that site)
- `scripts/check-docs.mjs` (only checks `docs/`)

Condense `README.md` to a pure usage guide: requirements, install, invocation
and flags (including the new `--brand` flag and `spotlight brand add`/`spotlight
brand list` subcommands), one short example. Drop gallery/credits/marketing
copy.

Keep unchanged: `LICENSE`, `examples/`, `skills/spotlight/` (renamed from
`skills/brag/`), `.claude-plugin/`, per-agent symlinks.

## Workflow visibility improvements

Two general changes, both apply on every run regardless of brand kit usage —
neither is brand-specific.

### Plan-approval checkpoint

Today, Step 2 ("Plan and storyboard") gate is just "the plan file exists with a
full storyboard summing to 15-25s" — the workflow proceeds straight to Step 3
(Hyperframes handoff) with no human checkpoint.

New behavior: after `spotlight-plan.md` is written, the agent **presents it and
stops**. It does not proceed to Step 3 until the user responds. The user can:
- Approve as-is.
- Request specific edits ("change the hook," "make scene 3 about X").
- Supply their own narrative direction outright.

The agent revises and re-presents until the user explicitly approves.

### Progress visibility

The full pipeline (inspect → plan → compose → render) can take several
minutes, dominated by Step 3 (Hyperframes composition) and Step 4 (render).
Today the agent can go quiet for long stretches with no indication of where a
run stands.

New behavior: the agent announces a short status line at the start of each of
the 4 steps (e.g. "Step 1/4 — Inspecting project...", "Step 3/4 — Composing
with Hyperframes (usually the slowest step)...", "Step 4/4 — Rendering..."),
and relays whatever progress output the underlying `npx hyperframes render`
subprocess streams to its own terminal output live, rather than waiting
silently for it to finish.

**Status lines distinguish "working" from "blocked on you."** The only point
in the pipeline that stops and waits on the user is the plan-approval
checkpoint above (end of Step 2). Its status line is visually/textually
distinct from the other three — e.g. "Step 2/4 — Plan ready, **waiting on
your review** — approve, edit, or add your own direction before I continue"
— rather than reading like an in-progress step. This is the only blocking
point the pipeline has today; if a future change adds another one, it follows
the same convention (steps are either "running" or "blocked, needs you," never
ambiguous between the two).

This is narration/UX only — no new numeric aggregate-percentage system is
invented, since accurate cross-tool percentage isn't available (Hyperframes is
an external CLI Spotlight has no visibility into beyond its stdout). Step
labels plus live subprocess output give the user a concrete sense of where the
run stands without overclaiming precision.

## Multi-brand kit system

### Storage

```
brands/
  <slug>/
    tokens.json          # source of truth
    brand.md             # human-readable, generated from tokens.json
    source/
      <original-upload>  # the PDF/HTML the brand was ingested from
    assets/
      logo/logo.<ext>
      fonts/<display-font-file>
      fonts/<body-font-file>
      music/<custom-track-file>   # only if tokens.json audio.musicTrack points here
```

### `tokens.json` schema

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

`fonts.*.file` is present only when `source: "bundled"` (a real font file was
supplied); when `source: "google-fonts"`, the composition references it via a
Google Fonts `<link>`, same mechanism the tool already uses for per-project
font extraction today.

`logo.placement` is reserved for future values; only `"outro"` is implemented
in this phase — ingestion always writes `"outro"` and never prompts the user
for this field.

`brand.md` is regenerated from `tokens.json` whenever the JSON is created or
edited — it is documentation, never hand-authored as the source of truth.

### Ingestion: `/spotlight brand add <path> --name "Acme"`

The one part of this workflow allowed to be interactive/ask questions, since a
brand PDF/HTML is unstructured input and this happens once per brand, not once
per video:

1. Read the file — PDF via the pdf skill, HTML via direct read.
2. Extract what's extractable: colors, font family names, tone-of-voice
   language, an embedded logo image if present.
3. Ask the user to fill gaps the source can't supply — most brand PDFs won't
   embed usable web font files, so the agent asks for a font file path or
   confirms a Google Fonts fallback; same for a clean logo asset if none was
   embeddable.
4. Ask which tone preset and (optionally) which music track this brand
   defaults to.
5. Write `tokens.json` + `brand.md`, copy the source file and any supplied
   assets into place under `brands/<slug>/`.

`/spotlight brand list` enumerates `brands/*/tokens.json` by name/slug.

### Selection and override

- New invocation flag: `--brand <slug>`.
- Omitted → unchanged default behavior: extract colors/fonts fresh from the
  inspected project, no logo, no pinned tone/music.
- When present, the brand's pinned tone/music are defaults for that run —
  **explicit per-run flags always win.** `/spotlight --brand acme --tone
  chaotic` uses Acme's palette/fonts/logo with `chaotic` pacing for just that
  run; the saved brand kit is not modified.
- Invalid `--brand <slug>` (no matching directory): stop with an error and
  list available brands (do not silently fall back to default extraction —
  that would silently produce an un-branded video when the user expected a
  specific brand).

### Frame-only styling scoping

When a brand is active, scoping applies to **both colors and fonts** — not
colors alone. The purpose is that the video still reads as the specific
product being bragged about, which breaks just as badly from a silently
swapped font as from a swapped color.

- **Frame** (backgrounds, headline/text treatment, transitions, outro, logo)
  uses the brand's colors and fonts.
- **Recreated product UI** (the app screens/components the video shows, *or*
  the recreated hero/UI element used as the strongest-visual fallback for
  landing-page-only projects per `step-2-plan.md`'s "Choosing what to show")
  keeps the real product's own extracted colors and fonts, so it still reads
  as that specific product rather than being fully re-skinned. This applies
  whether or not the project has a real app flow — "product UI" means any
  recreated material sourced directly from the product itself, not just app
  screens.

Step 1 extracts the project's own colors *and* fonts for this purpose whenever
a brand is active, the same two-value extraction it already does by default
today — it just also loads the brand kit's frame values alongside it instead
of using the project's values for the frame.

### Workflow doc changes

- **Step 1 (inspect)**: if `--brand` is set, skip using the project's CSS
  colors/fonts for the frame; load frame colors/fonts from `tokens.json`
  instead. Still separately extract the project's own real UI colors *and
  fonts* (needed for the frame-only rule above, applied to both). Content/copy/
  flow extraction is unchanged. Tone resolution also changes: today's rubric
  (`step-1-inspect.md` Q6 — use `--tone` if given, else infer from the project)
  gets a brand-aware branch — if `--brand` is active and `--tone` was not
  explicitly passed, use the brand's pinned `tone.preset` instead of
  inferring. An explicit `--tone` flag still overrides the brand's pin, per
  "Selection and override" below.
- **Step 2 (plan)**: `spotlight-plan.md`'s "Visual identity" section splits in
  two when a brand is active: **Brand identity (frame)** — colors and fonts
  from the brand kit — and **Product reference (recreation only)** — colors
  and fonts from the project's own extraction, used only where the video
  recreates real product UI.
- **Step 3 (compose)**: `composition-brief.md`'s Visual Identity section splits
  the same way as the plan's, carrying both colors and fonts for each side —
  brand frame values, and the product's own reference values for recreated
  UI — into the brief Hyperframes actually builds from, not just a single
  brand-only block. The brief additionally instructs Hyperframes to
  `@font-face` the brand's font files (copied into
  `composition/assets/fonts/`, same pattern already used for music/SFX today)
  and composite the logo at the outro (`composition/assets/logo/`). Pinned
  music track is copied in unless overridden by an explicit flag. The brief
  also carries the brand kit's
  `doNots` and `tone.voiceNotes` verbatim as creative-direction constraints, the
  same way `/brag` today hands Hyperframes tone/handoff guidance rather than
  low-level implementation details — so captured brand guidance actually
  reaches the composition step instead of being captured at ingestion and then
  never read again.
- **Step 4 (deliver)**: unchanged. The existing WCAG-contrast gate
  (`npx hyperframes check`) still runs regardless of which palette is active —
  brand colors are not exempt from it.

New reference doc: `references/brand-kits.md`, documenting the schema and
ingestion flow, same role as the existing `references/audio.md`.

## Error handling

| Case | Behavior |
|---|---|
| `--brand <slug>` doesn't match any `brands/` entry | Stop with an error, list available brands via the same logic as `spotlight brand list`. Do not fall back to default extraction. |
| Brand PDF/HTML yields no extractable colors/fonts | Ingestion asks the user directly for each missing field rather than guessing. |
| Brand kit has no pinned music track | Falls back to the tool's existing default music-selection logic for that run. |
| Brand kit has a font family name but no font file | Composition uses a Google Fonts `<link>` reference instead of a local `@font-face`, same as today's per-project font handling. |
| Brand colors fail the WCAG contrast gate | Flagged the same as any other contrast failure today — no special-casing for brand colors. |

## Testing / validation

No automated test framework exists in this repo and none is being added.
Validation is manual:
- Run `/spotlight` with no `--brand` against an `examples/` fixture and confirm
  output is unchanged in kind from today's `brag` behavior (per-project
  extraction still works after the rename/refactor).
- Ingest one sample brand kit end-to-end (a short test HTML "brand page" is
  enough) and run `/spotlight --brand <slug>` against an `examples/` fixture;
  confirm: tokens.json/brand.md generated correctly, frame uses brand
  colors/fonts, recreated UI keeps the product's own colors *and fonts*, logo
  appears at outro, plan-approval checkpoint stops and waits before Step 3,
  step-progress status lines appear at each of the 4 steps and the Step 2
  checkpoint's status line reads as distinctly "blocked, waiting on you"
  rather than "in progress," the `npx hyperframes render` subprocess's own
  output is actually relayed live during Step 4 (not just the step-start
  label), and `npx hyperframes check` still passes.
- Confirm a landing-page-only fixture (no app flow) still keeps its own
  recreated hero/UI element in the product's own colors/fonts when a brand is
  active, per the frame-only scoping rule's landing-page clause.
- Confirm the two documented error paths actually behave as specified: an
  invalid `--brand <slug>` stops with an error and lists available brands
  (does not silently fall back to default extraction), and a brand with a
  font family name but no font file falls back to a Google Fonts `<link>`
  rather than a broken `@font-face`.
