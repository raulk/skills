<img width="1672" height="941" alt="fable5 optimized-256" src="https://github.com/user-attachments/assets/1f0cbcc4-baaa-4237-8c63-5fc7a409ce81" />

# 0xff's skills

[0xff.ai](https://0xff.ai)'s Fable 5 Emulator skill for
[Claude Code](https://docs.claude.com/en/docs/claude-code). It gives an Opus
session a Fable-like style and judgment overlay: concise, plainspoken,
mechanism-first, and calibrated to the user's actual uncertainty. The skill
installs through the `skills` CLI with `npx`.

```
npx skills add 0xff-ai/skills
```

## What's inside

- `skills/fable-5-emulation/` — the Fable 5 Emulator skill.
- `cards/` — one prompting card per model (facts, tendencies, steering levers),
  used when tuning the emulator and related prompts. Schema in `cards/README.md`.
- `sources/` — verbatim vendor prompting guidance the cards are derived from.
- `learnings/` — durable findings about prompting and skill portability across
  models and harnesses.
- `AGENTS.md` / `CLAUDE.md` — agent guidance for working in this repo. `AGENTS.md`
  is the self-contained base; `CLAUDE.md` imports it and adds a thin Claude overlay.

## License

MIT
