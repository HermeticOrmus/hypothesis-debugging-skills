# Using this repo with Cursor

This project includes a Cursor project rule so the hypothesis-driven debugging protocol applies when you are debugging here.

## In this repository

1. Open the folder in Cursor.
2. The rule [`.cursor/rules/hypothesis-debugging.mdc`](.cursor/rules/hypothesis-debugging.mdc) is committed with `alwaysApply: false`, so it is situational. Cursor pulls it in when the work looks like debugging rather than firing on every edit.
3. In Cursor, confirm under **Settings, Rules**, where `hypothesis-debugging` should appear.

To force the rule into context for a specific session, reference it explicitly in chat (for example, "follow the hypothesis-debugging rule") when you start a debugging task.

## Use the same protocol in another project

**Cursor (recommended)**: Copy `.cursor/rules/hypothesis-debugging.mdc` into that project's `.cursor/rules/` directory (create the folders if needed). Merge with existing rules as you like.

**Other AI coding tools**: If a stack only supports a root instruction file, copy [`CLAUDE.md`](CLAUDE.md) into that project instead (or merge its contents into your existing instructions). Most modern AI coding tools (Claude Code, Continue, Cline, Windsurf, Aider) read a root-level instruction file.

## Optional: personal Agent Skills

If you want the same content as a reusable skill under `~/.cursor/skills`, use [`skills/hypothesis-debugging/SKILL.md`](skills/hypothesis-debugging/SKILL.md). Copy or symlink it into your personal skills directory.
