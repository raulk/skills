@AGENTS.md

## Claude Code / Opus 4.8

The base guidance above is model-agnostic. These notes correct for Opus 4.8's
defaults and the Claude Code harness; they apply on top of the shared register,
not instead of it.

- **Effort.** This is curation and writing work, so favor depth: run substantive
  edits at `xhigh` (or `high` minimum). Opus respects low effort strictly and
  will under-think card derivations at `low`/`medium`.
- **Literalism.** Opus follows instructions literally and will not generalize
  across items. When a change should touch every card or every section, say so
  explicitly rather than relying on it to infer the pattern.
- **Tool use.** Opus favors reasoning over tool calls. For derivation work,
  actually read the source in `sources/` and verify claims against it rather than
  reconstructing a card from memory.
- **Design defaults.** If any skill here produces frontend or slide output,
  remember Opus's persistent cream-serif house style and override it with a
  concrete spec or a propose-options step (see `cards/claude-opus-4-8.md`).
