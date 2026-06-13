---
model: claude-opus-4-8
display_name: Claude Opus 4.8
provider: Anthropic
family: Claude 4.x (Opus)
context_window: 1M tokens default (200k on Microsoft Foundry)
source: ../sources/claude-opus-4-8-prompting.md
source_url: https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-opus-4-8
fetched: 2026-06-13
---

# Claude Opus 4.8 — prompting card

Strengths: long-horizon agentic work, knowledge work, vision, memory. Performs
well out of the box on existing Opus 4.7 prompts; the items below are the
behaviors that most often need tuning on upgrade.

## At a glance

- **Calibrates verbosity to perceived task complexity** instead of a fixed
  length. Short on lookups, long on open-ended analysis.
- **Effort is the master lever** and matters more than on any prior Opus. Start
  `xhigh` for coding/agentic, minimum `high` for intelligence-sensitive work.
- **More literal instruction following.** Won't generalize an instruction across
  items or infer requests you didn't make. State scope explicitly.
- **Favors reasoning over tool calls**, spawns fewer subagents, and gives better
  unprompted progress updates. Steer up with effort or explicit prompting.
- **Strong, persistent design house style** (warm cream + serif). Great for
  editorial, wrong for dashboards/fintech/enterprise unless you override hard.
- **Better at finding bugs**, but follows "be conservative" review instructions
  more faithfully, which can look like a recall drop.

## Default tendencies

- **Verbosity tracks complexity** — not a fixed default. _Implication:_ if your
  product needs stable length/style, tune the prompt; positive concision
  examples beat negative "don't" instructions.
- **Respects effort levels strictly, especially at the low end.** At `low`/`medium`
  it scopes to exactly what was asked. _Implication:_ raise effort to fix shallow
  reasoning rather than prompting around it; moderately complex work at `low`
  risks under-thinking.
- **Thinking is off** unless `thinking: {type: "adaptive"}` is set; adaptive
  triggering is steerable and can over-fire with large/complex system prompts.
  _Implication:_ add guidance to suppress or to encourage thinking as needed.
- **Prefers reasoning over tool calls.** _Implication:_ usually better results,
  but if you need more tool use, raise effort or describe when/how/why to call.
- **Spawns fewer subagents by default.** _Implication:_ give explicit "when to
  fan out" guidance if you want parallelism.
- **Interprets prompts literally and explicitly**, particularly at lower effort.
  _Implication:_ precision and less thrash, but you must state scope ("every
  section, not just the first").
- **Direct, opinionated prose** with minimal validation-forward phrasing and
  sparing emoji. _Implication:_ re-evaluate voice prompts against this baseline.
- **More tokens in interactive (multi-turn) coding** than in autonomous
  single-turn, because it reasons more after user turns. _Implication:_ front-load
  spec and reduce required user turns to maximize autonomy + efficiency.

## Control levers

| Lever | Controls | Default | When to adjust | How |
| --- | --- | --- | --- | --- |
| `effort` | intelligence vs. token spend; also tool-use and subagent frequency | (see migration guide) | shallow reasoning, too few tool calls, latency/cost tuning | param: `low`/`medium`/`high`/`xhigh`/`max` |
| `thinking` | adaptive reasoning on/off + triggering | off | want explicit reasoning, or it over-fires | param `{type: "adaptive"}` + prompt steer |
| max output tokens | room to think/act across tools and subagents | — | running `max`/`xhigh` effort | param: start 64k, tune |
| Verbosity | response length/style | complexity-calibrated | product needs fixed style | prompt (positive concision examples) |
| Tool-use frequency | how often it calls tools | reasoning-favored | want more search/tool use | effort up, or describe when/how/why to call |
| Subagent spawning | parallel fan-out | sparse | want more/less parallelism | explicit "when to spawn" prompt |
| Progress updates | interim user-facing messages | well-calibrated, unprompted | want a specific shape | describe + example; remove old forcing scaffolds |
| Design palette | frontend/deck aesthetics | cream + serif house style | non-editorial brief | specify a concrete alternative, or have it propose options first |

## Parameters / knobs

Effort levels (the headline knob, more important here than on prior Opus):

- **`max`** — gains on some intelligence-demanding tasks, but diminishing returns
  and can overthink. Test, don't default.
- **`xhigh`** — best for most coding and agentic use cases. Default here.
- **`high`** — balances tokens and intelligence; minimum for intelligence-sensitive work.
- **`medium`** — cost-sensitive; trades intelligence for fewer tokens.
- **`low`** — short, scoped, latency-sensitive, non-intelligence-sensitive work only.

Other:

- **`thinking`**: off unless `{type: "adaptive"}`. Steerable via prompt.
- **Max output tokens**: start 64k when at `max`/`xhigh` so subagents/tool calls have room.

## Tool use & agentic behavior

- Reasoning is favored over tool calls by default; this is usually a win.
  `high`/`xhigh` show substantially more tool use in agentic search and coding.
- Subagents are sparse by default but steerable; specify when fan-out is wanted
  (e.g. across items or multi-file reads) and when it isn't (work doable in one response).
- Progress updates during long traces are more regular and higher quality; remove
  old "summarize every N tool calls" scaffolding and only re-add a shape if needed.
- Interactive coding burns more tokens (post-user-turn reasoning) but improves
  long-horizon coherence. Maximize autonomy: `xhigh`/`high` effort, add an auto
  mode, and specify task/intent/constraints fully in the first turn.

## Writing, tone & formatting

- Baseline prose: direct, opinionated, low validation-language, sparing emoji.
- For a warmer voice, add it explicitly (see snippet).
- For concision, give positive examples of the desired level rather than lists of
  what to avoid.

## Domain notes

### Frontend & design

Persistent house style: warm cream/off-white backgrounds (~`#F4F1EA`), serif
display type (Georgia, Fraunces, Playfair), italic word-accents, terracotta/amber
accent. Reads well for editorial/hospitality/portfolio; wrong for dashboards, dev
tools, fintech, healthcare, enterprise. Appears in slide decks too.

Generic negative instructions ("don't use cream", "make it clean") just shift it
to a different fixed palette. Two reliable overrides: (1) specify a concrete
alternative spec, which it follows precisely; (2) have it propose 4 directions
first and let the user pick. Approach (2) replaces the old `temperature`-for-variety
trick. Opus 4.8 needs less anti-"AI-slop" prompting than earlier models.

### Code review

Higher recall and precision internally, but it follows "only high-severity",
"be conservative", "don't nitpick" more faithfully, so a harness tuned for an
older model can show lower measured recall (a harness effect, not a regression):
same investigation depth, fewer findings reported. Fix by separating finding from
filtering (tell it the finding stage is coverage, not filtering), or if
self-filtering in one pass, set a concrete bar instead of "important". Iterate
against eval/test subsets to confirm recall/F1 gains.

### Computer use

Works across resolutions up to 2576px / 3.75MP. 1080p is the recommended
performance/cost balance; 720p or 1366×768 are lower-cost options with strong
performance. Tune effort here too.

## Ready-to-use snippets

Decrease verbosity:

```text
Provide concise, focused responses. Skip non-essential context, and keep examples minimal.
```

Encourage deeper reasoning when pinned to low effort:

```text
This task involves multi-step reasoning. Think carefully through the problem before responding.
```

Suppress over-eager adaptive thinking:

```text
Thinking adds latency and should only be used when it will meaningfully improve answer
quality — typically for problems that require multi-step reasoning. When in doubt,
respond directly.
```

Warmer voice:

```text
Use a warm, collaborative tone. Acknowledge the user's framing before answering.
```

Subagent guidance (coding):

```text
Do not spawn a subagent for work you can complete directly in a single response (e.g.
refactoring a function you can already see).

Spawn multiple subagents in the same turn when fanning out across items or reading multiple files.
```

Break the design default with options:

```text
Before building, propose 4 distinct visual directions tailored to this brief (each as:
bg hex / accent hex / typeface — one-line rationale). Ask the user to pick one, then
implement only that direction.
```

Anti-"AI-slop" frontend block (works with the above):

```text
<frontend_aesthetics>
NEVER use generic AI-generated aesthetics like overused font families (Inter, Roboto,
Arial, system fonts), cliched color schemes (particularly purple gradients on white or
dark backgrounds), predictable layouts and component patterns, and cookie-cutter design
that lacks context-specific character. Use unique fonts, cohesive colors and themes, and
animations for effects and micro-interactions.
</frontend_aesthetics>
```

Code-review coverage (move filtering downstream):

```text
Report every issue you find, including ones you are uncertain about or consider
low-severity. Do not filter for importance or confidence at this stage - a separate
verification step will do that. Your goal here is coverage: it is better to surface a
finding that later gets filtered out than to silently drop a real bug. For each finding,
include your confidence level and an estimated severity so a downstream filter can rank
them.
```

## Anti-patterns & legacy cleanup

- **Don't prompt around shallow reasoning** — raise `effort` instead.
- **Don't keep "summarize progress every N tool calls" scaffolding** — updates
  are good unprompted; only specify a shape if the default misfits.
- **Don't rely on negative design instructions** ("don't use cream") — they just
  swap one fixed palette for another. Specify or let it propose.
- **Don't assume scope generalizes** — literal following means "apply to every X"
  must be said.
- **Don't keep conservative review language** ("only high-severity") if you want
  coverage; it now obeys it.
- **Don't use `temperature` for design variety** — use the propose-options pattern.

## Recommended prompt skeleton

The source gives no single skeleton; instead it prescribes per-axis tuning. The
practical default for coding/agentic skills: set `effort: xhigh`, large max output
tokens (≥64k), full task spec in the first turn, explicit scope on any
broad-application instruction, and explicit subagent/tool-use policy if you want
behavior different from the (reasoning-favored, sparse-fan-out) default.

## Skill-author checklist

- [ ] Pick an `effort` default for the skill's task class (`xhigh` coding/agentic, `high`+ intelligence-sensitive).
- [ ] Set max output tokens (≥64k) if running `xhigh`/`max`.
- [ ] Decide thinking policy and steer it if the system prompt is large/complex.
- [ ] State scope explicitly on any "apply broadly" instruction.
- [ ] Remove legacy progress-update scaffolding.
- [ ] If you want tool use or fan-out, say when/how/why (or raise effort).
- [ ] For frontend skills, override the cream-serif default with a concrete spec or propose-options step.
- [ ] For review skills, separate coverage (finding) from filtering, or set a concrete severity bar.
- [ ] Front-load the full spec; minimize required user turns for autonomous flows.
- [ ] Re-check voice prompts against the direct/opinionated baseline.
