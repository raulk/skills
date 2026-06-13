---
model: gpt-5.5
display_name: GPT-5.5
provider: OpenAI
family: GPT-5.x
context_window: not stated in source
source: ../sources/gpt-5.5-prompting.md
source_url: https://developers.openai.com/api/docs/guides/prompt-guidance
fetched: 2026-06-13
---

# GPT-5.5 — prompting card

Best when prompts define the outcome (success criteria, constraints, available
evidence, required final content) and leave the model to choose the path. Shorter,
outcome-oriented prompts beat process-heavy legacy stacks. Migration can be
automated with the OpenAI Docs Skill (`$openai-docs migrate this project to gpt-5.5`).

## At a glance

- **Outcome-first beats process-first.** Describe the destination and success
  criteria; don't enumerate every step.
- **More efficient reasoning** — re-evaluate `low`/`medium` effort before escalating.
- **Default style is efficient, direct, task-oriented.** Good for production;
  define personality explicitly for conversational/customer-facing products.
- **Highly steerable on format and verbosity** (`text.verbosity`, default `medium`).
- **Avoid blanket `ALWAYS`/`NEVER`/`must`/`only`** — reserve absolutes for true
  invariants; use decision rules for judgment calls.
- **Retrieval/citation behavior must be prompted**: explicit search budgets and
  missing-evidence rules.
- **Preamble matters** for time-to-first-token in streaming/tool-heavy tasks.

## Default tendencies

- **Default style: efficient, direct, task-oriented**, minimal conversational
  padding. _Implication:_ great for production; add a personality + collaboration
  block for conversational products.
- **Reasons more efficiently than GPT-5.4.** _Implication:_ retest `low`/`medium`
  effort before reaching for higher.
- **Strongest with outcome-first prompts.** _Implication:_ over-specified process
  steps add noise, narrow the search space, and produce mechanical answers.
- **Highly steerable on output format/structure** and verbosity. _Implication:_
  set the shape you want; default to plain prose unless structure aids comprehension.
- **May spend time reasoning/planning before first visible token** in streaming.
  _Implication:_ request a short preamble to improve perceived responsiveness.
- **Treats absolutes literally.** _Implication:_ blanket `ALWAYS`/`NEVER` on
  judgment calls over-constrains; convert to decision rules.
- **Does not invent citations or stopping rules.** _Implication:_ retrieval
  budgets, citation requirements, and missing-evidence behavior must be in the prompt.

## Control levers

| Lever | Controls | Default | When to adjust | How |
| --- | --- | --- | --- | --- |
| `reasoning effort` | depth vs. cost/latency | (re-evaluate from low) | escalate only after `low`/`medium` underperform | param |
| `text.verbosity` | response length | `medium` | want shorter | param `low` (or describe shape) |
| Personality block | tone, warmth, directness, formality, humor | efficient/direct | conversational/customer-facing UX | prompt `# Personality` |
| Collaboration style | when to ask vs. assume, proactivity, when to check work | — | shape task behavior | prompt (separate from personality) |
| Preamble | time-to-first-visible-token | none | streaming / multi-step / tool-heavy | prompt (one-two sentence update) |
| Outcome spec | path-choice freedom | path-free when given criteria | always for complex tasks | prompt: goal + success criteria + constraints |
| Stopping conditions | when to stop looping/searching | — | agentic/tool loops | prompt: post-result "can I answer now?" check |
| Retrieval budget | when to search again | — | grounded/RAG answers | prompt: one broad search, re-search only on listed triggers |
| `phase` values | distinguish updates from final answer | preserved via `previous_response_id` | manual assistant-item replay | API field: `commentary` / `final_answer` |

## Parameters / knobs

- **`reasoning effort`**: efficiency improved vs. 5.4; start lower and escalate
  only if `low`/`medium` underperform.
- **`text.verbosity`**: API default `medium`; use `low` for shorter, more concise output.
- **`phase`** (assistant items, since GPT-5.4): `commentary` for intermediate
  user-visible updates, `final_answer` for the completed answer. With
  `previous_response_id` the API preserves state; if you manually replay assistant
  items, pass each original `phase` back unchanged and never add `phase` to user messages.

## Tool use & agentic behavior

- **Stopping conditions are explicit, not assumed.** Add a per-result self-check:
  "can I answer the core request now with useful evidence and citations?" If yes, answer.
- **Minimize loops without sacrificing correctness** — loop minimization must not
  outrank correctness, fallback evidence, calculations, or required citations.
- **Validation when tools allow it.** Ask coding agents to run targeted tests,
  type/lint/build checks, or a smoke test; explain when validation can't run.
- **Visual artifacts**: render then inspect for layout/clipping/spacing/missing
  content; revise until the render matches requirements.
- **Implementation plans** should be traceable (requirements → where addressed,
  named resources, state/data flow, validation commands, failure behavior,
  privacy/security, open questions).

## Writing, tone & formatting

- Default to plain paragraphs for conversation, explanations, reports, docs, and
  technical writeups. Use headers/bold/bullets/numbered lists sparingly — for
  user requests, comparisons/rankings, or hard-to-scan info.
- Respect explicit user formatting preferences (terse, no bullets, no headers).
- Add audience + length guidance when it matters (e.g. senior business audience,
  under 400 words, conclusion-first).
- For editing/rewriting/summaries, state what to preserve (artifact, length,
  structure, genre) before asking for improvement, so polish doesn't become expansion.

## Domain notes

### Personality vs. collaboration

Two distinct dials, both kept short, neither a substitute for goals/success
criteria/tool rules/stop conditions:

- **Personality** = how it sounds: tone, warmth, directness, formality, humor, empathy, polish.
- **Collaboration style** = how it works: when to ask vs. assume, proactivity,
  how much context, when to check work, how to handle uncertainty/risk.

### Grounding, citations, retrieval

Citation behavior is part of the prompt: define what needs support, what counts
as enough evidence, and missing-evidence behavior (absence of evidence is not an
automatic "no"). Use a retrieval budget as a stopping rule for search.

### Creative drafting guardrails

For slides, launch copy, customer summaries, talk tracks, leadership blurbs,
narrative framing: separate source-backed facts (cite them) from creative wording.
Don't invent names, first-party data, metrics, roadmap status, customer outcomes,
or capabilities; if support is thin, write a generic draft with placeholders or
labeled assumptions.

### Frontend engineering

Steer via OpenAI's frontend example instructions: product/user context,
design-system alignment, first-screen usability, familiar controls, expected
states, responsive behavior. Avoid generated-UI defaults: generic heroes, nested
cards, decorative gradients, visible instructional text, broken layouts.

## Ready-to-use snippets

Steady, task-focused personality:

```text
# Personality
You are a capable collaborator: approachable, steady, and direct. Assume the user is competent and acting in good faith, and respond with patience, respect, and practical helpfulness.

Prefer making progress over stopping for clarification when the request is already clear enough to attempt. Use context and reasonable assumptions to move forward. Ask for clarification only when the missing information would materially change the answer or create meaningful risk, and keep any question narrow.

Stay concise without becoming curt. Give enough context for the user to understand and trust the answer, then stop. Use examples, comparisons, or simple analogies when they make the point easier to grasp. When correcting the user or disagreeing, be candid but constructive. When an error is pointed out, acknowledge it plainly and focus on fixing it.

Match the user's tone within professional bounds. Avoid emojis and profanity by default, unless the user explicitly asks for that style or has clearly established it as appropriate for the conversation.
```

Preamble (general):

```text
Before any tool calls for a multi-step task, send a short user-visible update that acknowledges the request and states the first step. Keep it to one or two sentences.
```

Preamble (phase-aware coding agents):

```text
You must always start with an intermediary update before any content in the analysis channel if the task will require calling tools. The user update should acknowledge the request and explain your first step.
```

Outcome-first task spec:

```text
Resolve the customer's issue end to end.

Success means:
- the eligibility decision is made from the available policy and account data
- any allowed action is completed before responding
- the final answer includes completed_actions, customer_message, and blockers
- if evidence is missing, ask for the smallest missing field
```

Stopping condition:

```text
Resolve the user query in the fewest useful tool loops, but do not let loop minimization outrank correctness, accessible fallback evidence, calculations, or required citation tags for factual claims.

After each result, ask: "Can I answer the user's core request now with useful evidence and citations for the factual claims?" If yes, answer.
```

Plain-formatting policy:

```text
Let formatting serve comprehension. Use plain paragraphs as the default format for normal conversation, explanations, reports, documentation, and technical writeups. Keep the presentation clean and readable without making the structure feel heavier than the content.

Use headers, bold text, bullets, and numbered lists sparingly. Reach for them when the user requests them, when the answer needs clear comparison or ranking, or when the information would be harder to scan as prose. Otherwise, favor short paragraphs and natural transitions.

Respect formatting preferences from the user. If they ask for a terse answer, minimal formatting, no bullets, no headers, or a specific structure, follow that preference unless there is a strong reason not to.
```

Preserve-before-improve (editing):

```text
Preserve the requested artifact, length, structure, and genre first. Quietly improve clarity, flow, and correctness. Do not add new claims, extra sections, or a more promotional tone unless explicitly requested.
```

Retrieval budget:

```text
For ordinary Q&A, start with one broad search using short, discriminative keywords. If the top results contain enough citable support for the core request, answer from those results instead of searching again.

Make another retrieval call only when:
- The top results do not answer the core question.
- A required fact, parameter, owner, date, ID, or source is missing.
- The user asked for exhaustive coverage, a comparison, or a comprehensive list.
- A specific document, URL, email, meeting, record, or code artifact must be read.
- The answer would otherwise contain an important unsupported factual claim.

Do not search again to improve phrasing, add examples, cite nonessential details, or support wording that can safely be made more generic.
```

Coding-agent validation:

```text
After making changes, run the most relevant validation available:
- targeted unit tests for changed behavior
- type checks or lint checks when applicable
- build checks for affected packages
- a minimal smoke test when full validation is too expensive

If validation cannot be run, explain why and describe the next best check.
```

## Anti-patterns & legacy cleanup

- **Don't carry over process-heavy legacy prompts** — they add noise and narrow
  the search space. Re-derive from the outcome.
- **Don't use blanket `ALWAYS`/`NEVER`/`must`/`only`** for judgment calls; reserve
  for true invariants (safety, required fields, forbidden actions).
- **Don't escalate effort reflexively** — retest `low`/`medium` first.
- **Don't assume default stopping/citation behavior** — prompt budgets and rules.
- **Don't let "minimize tool loops" outrank correctness** — say so explicitly.
- **Don't over-format** — plain prose is the default; structure must earn its place.

## Recommended prompt skeleton

Keep each section short; add detail only where it changes behavior.

```text
Role: [1-2 sentences defining the model's function, context, and job]

# Personality
[tone, demeanor, and collaboration style]

# Goal
[user-visible outcome]

# Success criteria
[what must be true before the final answer]

# Constraints
[policy, safety, business, evidence, and side-effect limits]

# Output
[sections, length, and tone]

# Stop rules
[when to retry, fallback, abstain, ask, or stop]
```

## Skill-author checklist

- [ ] Write the prompt outcome-first: goal + success criteria + constraints, not step lists.
- [ ] Start at `low`/`medium` reasoning effort; escalate only on measured underperformance.
- [ ] Set `text.verbosity` (`low` for concise) and a plain-formatting policy.
- [ ] Add a `# Personality` + collaboration block only for conversational/customer-facing skills.
- [ ] Add a preamble instruction for streaming or tool-heavy flows.
- [ ] Convert blanket absolutes to decision rules; keep absolutes for true invariants.
- [ ] Add explicit stopping conditions and (for RAG) a retrieval budget + citation rules.
- [ ] For drafting skills, separate source-backed facts from creative wording.
- [ ] For coding skills, specify concrete validation commands and traceable-plan fields.
- [ ] If manually replaying assistant items, preserve `phase` values unchanged.
