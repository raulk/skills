# AGENTS.md vs CLAUDE.md: one source of truth across harnesses

How to share instruction/memory files between Claude Code (which reads
`CLAUDE.md`) and other agents (Codex, Cursor, etc., which read `AGENTS.md`).
This is the file-mechanism half of the question in [`skill-reuse.md`](skill-reuse.md),
which covers the prose-portability half.

## Ground truth (Claude Code)

- **Claude Code reads only `CLAUDE.md`.** It never reads `AGENTS.md`. If both
  exist in a directory, `AGENTS.md` is ignored entirely. No setting changes this.
- **Discovery is concatenation up the tree**, broadest to most specific:
  managed policy `CLAUDE.md` → `~/.claude/CLAUDE.md` → ancestor dir `CLAUDE.md`
  files → project `./CLAUDE.md` (or `./.claude/CLAUDE.md`) → `./CLAUDE.local.md`
  → subdirectory `CLAUDE.md` (loaded on demand when Claude reads files there).
  All discovered files are concatenated, not merged or overridden. Later text
  wins by recency.
- **Import syntax**: `@path/to/file`, anywhere in the markdown. Resolves
  relative to the importing file, absolute paths allowed, recursive to depth 4.
  First external import shows an approval dialog. Imports expand fully at session
  start, so splitting is an organization win, not a context-cost win.
- **Official single-source pattern**: `@AGENTS.md` at the top of `CLAUDE.md`,
  Claude-specific content below. Symlink `CLAUDE.md -> AGENTS.md` also works on
  Unix (Windows needs Admin/Developer Mode, so import is preferred). `/init`
  auto-detects an existing `AGENTS.md` and folds it into a generated `CLAUDE.md`.

## The sharp constraint

Two facts collide:

1. **`@import` is a Claude Code feature.** Other `AGENTS.md` readers do not
   expand `@core.md`; they read it as literal text. So **`AGENTS.md` must be
   self-contained** (no imports) to work cross-tool.
2. **`AGENTS.md` cannot branch by model.** Whatever tool reads it gets the whole
   file regardless of which model runs. There is no per-model conditional in a
   static file.

Consequence: the model-portability question and the file-convention question are
not independent. A Claude/Opus overlay has a home (`CLAUDE.md`); a GPT-5.5
overlay does not (there is no `GPT.md`), so GPT-leaning nudges must live inline
in `AGENTS.md`.

## Recommended structure

`AGENTS.md` is the single self-contained source of truth; `CLAUDE.md` is a thin
Claude overlay that imports it:

```
AGENTS.md     # self-contained: shared common register + the few GPT-5.5-leaning
              # nudges inline. Read by Codex, Cursor, etc. running GPT-5.5.

CLAUDE.md     # @AGENTS.md
              # ## Claude Code / Opus 4.8
              #   - corrects the 2-3 opposite nudges (favor reasoning -> when to
              #     call tools MORE; cream-serif design override; ignore the
              #     stop-looping emphasis)
              #   - Claude Code harness specifics
```

Why it works: Claude Code reads `CLAUDE.md`, expands `@AGENTS.md`, then applies
the Opus corrections last (concatenation order means later text wins). GPT-5.5
tools read `AGENTS.md` directly. One real source file, one short overlay. This is
the "base + thin overlay" pattern from [`skill-reuse.md`](skill-reuse.md), with
the import as its file-level mechanism.

This is feasible only because the genuine conflicts are small (see
`skill-reuse.md`): the overlay only has to correct those few. If they were large,
you would be forced into two full forks and the `AGENTS.md` / `CLAUDE.md` sharing
would stop paying off.
