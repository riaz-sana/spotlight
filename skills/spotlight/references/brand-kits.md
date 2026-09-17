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
