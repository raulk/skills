# Can one SKILL.md / AGENTS.md serve multiple models?

Question: is it feasible to use a single `SKILL.md` or `AGENTS.md` across
both Claude Opus 4.8 and GPT-5.5? Derived from the two prompting cards in
[`../cards`](../cards).

## Verdict

Yes, feasible, with one load-bearing caveat: **keep parameters out of the
prose.** The body of an `AGENTS.md`/`SKILL.md` is model-agnostic instruction
text. The biggest apparent differences between these two models are not prose
at all, they are API settings (`effort` vs `reasoning effort`, `thinking`,
`text.verbosity`, `phase`) that live in harness config and are set
per-deployment regardless. Once those move out, the prose differences are
smaller than the cards make them look.

## Most divergences collapse to a common register

Write to the stricter/intersection discipline, which both models reward:

| Axis | Opus 4.8 wants | GPT-5.5 wants | Shared register that serves both |
| --- | --- | --- | --- |
| Instruction style | literal, state scope | drop blanket ALWAYS/NEVER | decision rules + explicit scope |
| Process vs outcome | follows steps literally | outcome-first, no process bloat | outcome + success criteria + scope |
| Formatting | concision examples | plain prose default | "formatting serves comprehension" |
| Voice | direct baseline, add warmth explicitly | direct baseline, add warmth explicitly | same (both need explicit voice) |
| Validation | (not in guide) | run concrete checks | concrete validation block (harmless to Opus) |

Outcome-first + explicit scope + decision-rules + plain formatting is a single
style that is near-optimal for both. That is the convergent "good prompt" shape
both guides independently push toward.

## What genuinely needs a per-model nudge

Only three, and they are short:

1. **Tool-use direction is opposite.** Opus under-calls tools (nudge up);
   GPT-5.5 over-loops (needs stop conditions). A combined block with both a
   "when to use tool X" policy and a "stop when you can answer" condition covers
   both, since each model reads the half it needs.
2. **Design defaults differ in specifics.** Opus fights its cream-serif house
   style; GPT-5.5 fights generic-hero / nested-card / gradient defaults. The
   meta-instruction ("specify a concrete direction or propose options, avoid
   generic AI aesthetics") is shared; the concrete override lists differ.
3. **Preamble emphasis.** GPT-5.5 wants one; Opus already does it and dislikes
   rigid cadence scaffolding. A single soft "open with a one-line preamble
   before tool-heavy work" is fine for both. The thing Opus dislikes is the
   heavy "summarize every N calls" cadence, not an opening line.

## Recommended pattern: base + thin overlay

Not one flat file, and not two full forks:

- **Shared `AGENTS.md`/`SKILL.md` body** written in the common register above.
- **A short per-model block** (front-matter-selected, or a `## Model notes`
  appendix) for the three nudges plus the design override list.
- **Parameters in harness config**, never in the markdown.

This is the same progressive-disclosure shape skills already use.

## What you trade away

You cannot maximally exploit each model's superpowers from a shared file
(Opus's design instincts and bug-finding, GPT-5.5's `phase` channel), and the
two opposite nudges land as a mild compromise rather than a tuned setting. For
most skills that loss is negligible. For a design-heavy or review-heavy skill,
that is where a fork earns its keep.
