# Model prompting cards

One card per model, capturing the facts, tendencies, and steering levers we need
when optimizing skills and agent prompts for that model. Cards are distilled from
the vendor guidance archived in [`../sources`](../sources). When a model is
updated, re-fetch its source and re-derive the card.

## Why these exist

Skills and agent prompts are model-specific in practice. The same instruction
that fixes one model's behavior is noise on another. These cards let a
skill-author (human or agent) answer three questions quickly:

1. What does this model do by default? (so I know which default I'm leveraging or fighting)
2. Which lever changes that behavior? (param vs. prompt, and the exact snippet)
3. What should I delete from a legacy prompt? (scaffolding that's now counterproductive)

## Canonical card structure

Every card follows the same section order so two models can be diffed
section-by-section. Omit a section only if the source says nothing about it; note
the omission rather than silently dropping it.

| Section | Holds | Form |
| --- | --- | --- |
| Frontmatter | model id, provider, family, context window, source pointer, fetch date | YAML |
| **At a glance** | the 3-6 headline behavioral shifts you must account for | bullets |
| **Default tendencies** | the facts: out-of-the-box behavior + the prompting implication of each | `**Tendency** — behavior. _Implication:_ ...` |
| **Control levers** | how to steer each tendency | table: lever / controls / default / when to adjust / how |
| **Parameters / knobs** | API-level settings and recommended values per use case | table or list |
| **Tool use & agentic behavior** | subagents, tool triggering, autonomy, progress updates | prose + bullets |
| **Writing, tone & formatting** | prose style, verbosity, formatting defaults | prose + bullets |
| **Domain notes** | frontend/design, code review, retrieval, computer use, etc. | subsections |
| **Ready-to-use snippets** | labeled, copy-paste prompt blocks | fenced `text` blocks |
| **Anti-patterns & legacy cleanup** | what to remove or avoid; counterproductive scaffolding | bullets |
| **Recommended prompt skeleton** | the vendor's suggested prompt scaffold, if any | fenced block |
| **Skill-author checklist** | actionable items when writing/optimizing a skill for this model | checkboxes |

### Conventions

- **Fidelity over paraphrase.** Prefer the source's own framing and numbers.
  Mark anything we infer (not stated in the source) with `(inferred)`.
- **Snippets are verbatim** from the source where possible, so they can be
  pasted into a prompt without re-deriving them. Keep them in `text` fences.
- **Provider-neutral vocabulary.** A "lever" is anything that changes behavior:
  an API parameter or a prompt instruction. The "how" column says which.
- **Every claim is traceable.** If a card states a behavior, it should be
  findable in the linked source. Don't add cross-model folklore.
- Follow the repo's prose rules: sentence case headings, no em dashes.

## Cards

- [`claude-opus-4-8.md`](claude-opus-4-8.md) — Anthropic Claude Opus 4.8
- [`gpt-5.5.md`](gpt-5.5.md) — OpenAI GPT-5.5
