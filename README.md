# skills

Raul's personal collection of [Claude Code](https://docs.claude.com/en/docs/claude-code)
skills, plus the model guidance used to write and tune them. The repo doubles as
an installable Claude plugin and its own single-plugin marketplace.

## Contents

- [Install](#install)
- [What's inside](#whats-inside)
- [License](#license)

## Install

```
/plugin marketplace add raulk/dotagents
/plugin install raulk-skills@raulk-skills
```

To try it from a local checkout before it is published, add it by path:

```
/plugin marketplace add /path/to/this/repo
/plugin install raulk-skills@raulk-skills
```

The plugin ships everything under `skills/`. Adding a new `skills/<name>/SKILL.md`
ships it on the next release; no manifest edit is needed.

## What's inside

- `skills/` — the skills themselves, one directory per skill (`<name>/SKILL.md`).
  Currently ships `fable-5-emulation`, a session-wide style and judgment overlay
  that makes an Opus session approximate Claude Fable 5's conversational character.
- `cards/` — one prompting card per model (facts, tendencies, steering levers),
  used when optimizing skills and prompts. Schema in `cards/README.md`.
- `sources/` — verbatim vendor prompting guidance the cards are derived from.
- `learnings/` — durable findings about prompting and skill portability across
  models and harnesses.
- `AGENTS.md` / `CLAUDE.md` — agent guidance for working in this repo. `AGENTS.md`
  is the self-contained base; `CLAUDE.md` imports it and adds a thin Claude overlay.

## License

MIT
