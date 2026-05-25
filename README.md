<p align="center">
  <img src="https://ormus.solutions/mascot/pixellab_liquid_to_eye.gif" alt="Hypothesis Debugging Skills" width="128" style="image-rendering: pixelated;" />
</p>

<h1 align="center">Hypothesis Debugging Skills</h1>

<p align="center">
  <em>A Claude Code skill for hypothesis-driven debugging — reproduce, isolate, hypothesize, test, fix, document. Find the root cause, not a workaround.</em>
</p>

<p align="center">
  <a href="https://github.com/HermeticOrmus/hypothesis-debugging-skills/stargazers"><img src="https://img.shields.io/github/stars/HermeticOrmus/hypothesis-debugging-skills?style=flat-square&color=aa8142" alt="Stars" /></a>
  <a href="https://github.com/HermeticOrmus/hypothesis-debugging-skills/blob/main/LICENSE"><img src="https://img.shields.io/github/license/HermeticOrmus/hypothesis-debugging-skills?style=flat-square&color=aa8142" alt="License" /></a>
  <a href="https://github.com/HermeticOrmus/hypothesis-debugging-skills/commits"><img src="https://img.shields.io/github/last-commit/HermeticOrmus/hypothesis-debugging-skills?style=flat-square&color=aa8142" alt="Last Commit" /></a>
  <img src="https://img.shields.io/badge/Claude_Code-aa8142?style=flat-square&logo=anthropic&logoColor=white" alt="Claude Code" />
</p>

---

This is the companion to [vibe-engineer-skills](https://github.com/HermeticOrmus/vibe-engineer-skills). That repo states the principle: hypothesis before help. This repo is the procedure that enacts it when you actually sit down to debug.

## The problem

"Fix this" is the most expensive prompt in software. Handed a vague symptom, an AI guesses at the most plausible cause, writes a change that compiles and passes a test, and hands back a green build. The build is green because the symptom is gone, not because the bug is gone.

The result is a workaround that hides the real failure:

- A try/catch that swallows the error instead of preventing it.
- A defensive null check that masks an upstream contract violation.
- A renamed variable that still holds the wrong state.
- A mocked dependency that was the thing actually broken.

Each one passes tests. Each one propagates the bug to the next caller of the same broken code. The fix that hides a bug is worse than no fix, because now the bug is invisible.

The cure is to refuse to write a fix until the cause is known. That is what this protocol enforces.

## The six-phase protocol

| Phase | Does | Output |
|---|---|---|
| **1. Reproduce** | Make the failure happen on demand | A repeatable trigger and the exact error |
| **2. Isolate** | Narrow scope via recent diffs and a minimal case | "This input to this function fails" |
| **3. Hypothesize** | Write a ranked hypothesis table before touching code | Ranked causes, each with a test |
| **4. Test** | Work the table top-down, confirm or refute each row | A confirmed root cause |
| **5. Fix** | Smallest change that addresses the cause, plus a regression test | A verified fix, not a workaround |
| **6. Document** | Record root cause, fix, and prevention | A lesson, not just a closed ticket |

The load-bearing step is Phase 3. The ranked hypothesis table is what turns an AI's job from inventing a cause into testing yours. Full content: [`CLAUDE.md`](CLAUDE.md). Worked walkthroughs: [`EXAMPLES.md`](EXAMPLES.md).

## Install

### As a project CLAUDE.md

Drop [`CLAUDE.md`](CLAUDE.md) at the root of your repository. Claude Code picks it up automatically. Merge with existing project instructions if any.

```bash
curl -o CLAUDE.md https://raw.githubusercontent.com/HermeticOrmus/hypothesis-debugging-skills/main/CLAUDE.md
```

### As a Claude Code skill

The same content is packaged as a skill under [`skills/hypothesis-debugging/`](skills/hypothesis-debugging/) for `~/.claude/skills/`. See the `SKILL.md` inside for installation.

### As a `/debug` slash command

Save the protocol as a slash command so you can invoke it on demand with the symptom as an argument.

```bash
curl -o ~/.claude/commands/debug.md https://raw.githubusercontent.com/HermeticOrmus/hypothesis-debugging-skills/main/CLAUDE.md
```

Then run `/debug <error-or-symptom>` in any Claude Code session to start the loop against a specific failure.

### In Cursor

See [`CURSOR.md`](CURSOR.md) for the Cursor-rule equivalent at [`.cursor/rules/hypothesis-debugging.mdc`](.cursor/rules/hypothesis-debugging.mdc). The rule is situational (`alwaysApply: false`) so it activates when you are debugging rather than on every edit.

### In other AI coding tools

If your tool reads a single instruction file at the project root, copy `CLAUDE.md` to whatever name your tool expects (`AGENTS.md`, `INSTRUCTIONS.md`, etc.).

## See also

- [`vibe-engineer-skills`](https://github.com/HermeticOrmus/vibe-engineer-skills): the principle this repo enacts, hypothesis before help, plus the four other Vibe Engineer principles for directing AI codegen.
- [`andrej-karpathy-skills`](https://github.com/HermeticOrmus/andrej-karpathy-skills): how the AI should behave while you run this loop, think before coding, simplicity first, surgical changes, goal-driven execution.

## Contributing

PRs welcome, especially for additional worked walkthroughs in [`EXAMPLES.md`](EXAMPLES.md), translations of the README, and adaptations of `CURSOR.md` for other AI coding tools (Windsurf, Cline, Aider, Continue, etc.).

## License

MIT. Use it, fork it, merge it into your own CLAUDE.md.
