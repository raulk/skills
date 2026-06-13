# skills

Raul's personal agent tooling: [Claude Code](https://docs.claude.com/en/docs/claude-code)
skills, the model prompting cards and learnings used to write and tune them, and
the agent guidance that governs the repo itself. The skills ship as an installable
Claude plugin, and the repo doubles as its own single-plugin marketplace.

## Contents

- [Install](#install)
- [What's inside](#whats-inside)
- [License](#license)

## Install

With the `skills` CLI (works across agents):

```
npx skills add raulk/skills
```

As a Claude Code plugin:

```
/plugin marketplace add raulk/skills
/plugin install skills@skills
```

To try the plugin from a local checkout before it is published, add it by path:

```
/plugin marketplace add /path/to/this/repo
/plugin install skills@skills
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
