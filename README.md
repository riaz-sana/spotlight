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

`/spotlight` owns the story — the product angle, tone, and which moments to show, plus (optionally) a saved brand kit's colors, fonts, logo, and pinned tone/music. It hands a focused brief to [Hyperframes](https://hyperframes.heygen.com/), which builds, times, and renders the video.

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
