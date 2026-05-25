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

---

## Part of the Libre Open-Source Stack for Claude Code

This repository is part of a growing family of open-source toolkits for Claude Code.

### Libre suite — comprehensive plugin bundles

- [LibreUIUX-Claude-Code](https://github.com/HermeticOrmus/LibreUIUX-Claude-Code) — UI/UX development (152 agents, 70 plugins, 76 commands, 74 skills)
- [LibreArch-Claude-Code](https://github.com/HermeticOrmus/LibreArch-Claude-Code) — Software architecture and system design
- [LibreCopy-Claude-Code](https://github.com/HermeticOrmus/LibreCopy-Claude-Code) — Technical writing and documentation engineering
- [LibreDevOps-Claude-Code](https://github.com/HermeticOrmus/LibreDevOps-Claude-Code) — DevOps engineering and infrastructure automation
- [LibreEmbed-Claude-Code](https://github.com/HermeticOrmus/LibreEmbed-Claude-Code) — Embedded systems, firmware, and IoT development
- [LibreFinTech-Claude-Code](https://github.com/HermeticOrmus/LibreFinTech-Claude-Code) — Financial technology development
- [LibreGEO-Claude-Code](https://github.com/HermeticOrmus/LibreGEO-Claude-Code) — AI-search optimization (ChatGPT, Perplexity, Gemini, Google AI Overviews)
- [LibreGameDev-Claude-Code](https://github.com/HermeticOrmus/LibreGameDev-Claude-Code) — Game development across Godot, Unity, Unreal
- [LibreMLOps-Claude-Code](https://github.com/HermeticOrmus/LibreMLOps-Claude-Code) — ML engineering and AI operations
- [LibreMobileDev-Claude-Code](https://github.com/HermeticOrmus/LibreMobileDev-Claude-Code) — Mobile app development (Flutter, React Native, native iOS, native Android)
- [LibreSecOps-Claude-Code](https://github.com/HermeticOrmus/LibreSecOps-Claude-Code) — Security operations
- [LibreSessionFlow-Claude-Code](https://github.com/HermeticOrmus/LibreSessionFlow-Claude-Code) — Session lifecycle: handoff, pickup, absorb, explore, close

### Skills mini-repos — single CLAUDE.md drop-ins

- [vibe-engineer-skills](https://github.com/HermeticOrmus/vibe-engineer-skills) — Direct AI codegen well: hypothesis before help, scoped prompts, validate before accepting
- [markdown-discipline-skills](https://github.com/HermeticOrmus/markdown-discipline-skills) — Strip AI-slop from markdown (no em dashes, no marketing fluff)
- [shell-safety-skills](https://github.com/HermeticOrmus/shell-safety-skills) — `set -euo pipefail` discipline plus 15 failure-mode examples
- [commit-standard-skills](https://github.com/HermeticOrmus/commit-standard-skills) — Ormus Commit Standard v1.0 plus commit-msg hook and commitlint
- [unwoke-skills](https://github.com/HermeticOrmus/unwoke-skills) — Strip AI theater (ten sins to eliminate, symmetric engagement)
- [python-conventions-skills](https://github.com/HermeticOrmus/python-conventions-skills) — Modern Python 3.11+ (types, pathlib, async, ruff, mypy, uv)
- [typescript-conventions-skills](https://github.com/HermeticOrmus/typescript-conventions-skills) — TypeScript strict mode, discriminated unions, Result types
- [hermetic-laws-skills](https://github.com/HermeticOrmus/hermetic-laws-skills) — Seven Hermetic Principles applied to engineering
- [riper-workflow-skills](https://github.com/HermeticOrmus/riper-workflow-skills) — Research / Innovate / Plan / Execute / Review systematic dev
- [six-day-cycle-skills](https://github.com/HermeticOrmus/six-day-cycle-skills) — Sustainable shipping cadence with mandatory rest
- [token-optimization-skills](https://github.com/HermeticOrmus/token-optimization-skills) — Claude Code token and context optimization
- [osint-skills](https://github.com/HermeticOrmus/osint-skills) — OSINT research methodology (multi-wave investigative spiral)
- [calcinate-skills](https://github.com/HermeticOrmus/calcinate-skills) — Stage 1 of the Magnum Opus (burn project bloat)
- [claude-md-overhaul-skills](https://github.com/HermeticOrmus/claude-md-overhaul-skills) — Audit CLAUDE.md and MEMORY.md against caps
- [session-handoff-skills](https://github.com/HermeticOrmus/session-handoff-skills) — Session handoff and pickup discipline
- [naming-skills](https://github.com/HermeticOrmus/naming-skills) — Product naming methodology (mine the brand's vocabulary)
- [magnum-opus-skills](https://github.com/HermeticOrmus/magnum-opus-skills) — Seven-stage alchemy applied to project transformation
- [mem-search-skills](https://github.com/HermeticOrmus/mem-search-skills) — Search claude-mem cross-session memory: search, filter, fetch
- [vibe-proof-skills](https://github.com/HermeticOrmus/vibe-proof-skills) — Security hardening for vibe-coded full-stack apps
- [tdd-skills](https://github.com/HermeticOrmus/tdd-skills) — Test-driven development (Red-Green-Refactor) for JS/TS and Python
- [mars-skills](https://github.com/HermeticOrmus/mars-skills) — Production-readiness audit: the five mortal sins of vibe-coded MVPs
- [git-workflow-skills](https://github.com/HermeticOrmus/git-workflow-skills) — Clean git workflow: branch, atomic commits, reviewable PRs
- [code-review-skills](https://github.com/HermeticOrmus/code-review-skills) — Domain-aware code review: classify the code, then focus
- [explore-code-skills](https://github.com/HermeticOrmus/explore-code-skills) — Understand an unfamiliar codebase fast
- [dx-audit-skills](https://github.com/HermeticOrmus/dx-audit-skills) — Audit developer experience: docs, onboarding, tooling friction
- [setup-env-skills](https://github.com/HermeticOrmus/setup-env-skills) — Set up a project's development environment
- [automate-skills](https://github.com/HermeticOrmus/automate-skills) — Turn repetitive tasks into reliable automation scripts
- [quick-fix-skills](https://github.com/HermeticOrmus/quick-fix-skills) — Fast troubleshooting for common issues
- [prime-context-skills](https://github.com/HermeticOrmus/prime-context-skills) — Prime project context at the start of a session
- [auto-docs-skills](https://github.com/HermeticOrmus/auto-docs-skills) — Generate and maintain project documentation
- [learning-skills](https://github.com/HermeticOrmus/learning-skills) — Learn any technology: roadmaps, explanations, practice, cheatsheets, comparisons
- [linux-sysadmin-skills](https://github.com/HermeticOrmus/linux-sysadmin-skills) — Linux system administration: security, performance, diagnostics, monitoring, maintenance

### Template source

- [andrej-karpathy-skills](https://github.com/HermeticOrmus/andrej-karpathy-skills) — the canonical single-file CLAUDE.md pattern (fork of jiayuan_jy's original)

Star the family, not just one — that's how the suite stays coherent.
