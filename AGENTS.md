# dot-agents

Raul's skills and global agent guidance. This repo holds reusable agent
material: model prompting cards, durable learnings, and skills. Treat it as a
curated knowledge base, not application code.

## Repo map

- `cards/` — one prompting card per model (facts, tendencies, steering levers),
  used when optimizing skills and prompts. Schema and conventions live in
  `cards/README.md`. Read it before adding or editing a card.
- `sources/` — verbatim vendor guidance the cards are derived from. Re-fetchable;
  most doc sites serve markdown at a `.md` suffix on the page URL.
- `learnings/` — durable findings worth keeping across sessions. One file per
  topic, cross-linked.
- `skills/` — Claude Code skills, one directory per skill (`<name>/SKILL.md`).
- `.claude-plugin/` — packages this repo as the `skills` marketplace shipping the
  `skills` plugin. The plugin ships everything under `skills/`, so adding a skill
  directory there ships it; no manifest edit needed.

## How to work here

- **Cards are traceable.** Every claim in a card should be findable in its linked
  source. Prefer the source's own framing and numbers; mark anything inferred
  with `(inferred)`. Keep copy-paste snippets verbatim so they paste without
  re-derivation. When a model updates, re-fetch its source and re-derive the card
  rather than patching from memory.
- **Learnings cite their basis.** Link to the cards, sources, or other learnings
  a finding rests on, so a reader can audit it.
- **Keep this file self-contained.** Other agents (Codex, Cursor) read `AGENTS.md`
  directly and do not expand `@imports`, so do not add import directives here.

## Writing register

Write guidance, cards, and learnings outcome-first: state what good looks like,
the constraints that matter, and the evidence available, then stop. This register
serves every model we target.

- Prefer decision rules over blanket absolutes. Reserve ALWAYS/NEVER/must/only
  for true invariants.
- State scope explicitly when an instruction should apply broadly.
- Plain prose by default. Use headers, bold, and lists only when they aid
  comparison or scanning.
- Sentence case headings. No em dashes (use parentheses, commas, colons, or
  separate sentences).
