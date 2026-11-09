# cortana-companion

A companion repo for building your own "AI assistant with a persona" setup on top of [Jared Rhod's](https://github.com/jaredrhod) open-source agent stack:

- [`fullstack-agent`](https://github.com/jaredrhod/fullstack-agent) — the installer wizard that assembles an AI agent with memory, voice, face, and optional hand-tracking.
- [`ai-memory-vault`](https://github.com/jaredrhod/ai-memory-vault) — turns an Obsidian vault into persistent working memory for an AI, using plain markdown instead of a vector database.
- [`backtalk`](https://github.com/jaredrhod/backtalk) — lets you talk aloud to a Claude Code agent and get a spoken reply back.

This repo does **not** vendor or copy any of that code. It only links to those projects and adds the pieces they don't cover: a fill-in persona template, a worked example of the Claude Code subagent pattern, and two reusable skills written up from real build experience. Everything here is template/example content — no real personal names, business names, credentials, or account IDs are included anywhere in this repo.

## Demo video

https://github.com/user-attachments/assets/afe3a368-ba12-4000-8309-9929b4330bf3

A walkthrough of an assistant built on this pattern — memory, dashboard, calendar/bid tracking, chat/email integration, and the tool stack used to make the video itself. (Also available as a direct download: [demo/cortana-capabilities-demo.mp4](demo/cortana-capabilities-demo.mp4).)

## What's included

```
.gitignore
LICENSE
README.md
agents/README.md
agents/example-specialist.md
demo/cortana-capabilities-demo.mp4
persona/CORTANA.md
skills/avatar-video-automation/SKILL.md
skills/discord-bot-provisioning/SKILL.md
```

- **`persona/CORTANA.md`** — a fill-in-the-blanks identity template for giving your assistant a name, personality, and voice/avatar configuration, with bracketed markers in place of real values and a worked (fictional) example.
- **`agents/README.md`** and **`agents/example-specialist.md`** — an explanation of the Claude Code subagent (specialist) pattern, plus one complete, runnable example specialist you can copy and adapt.
- **`skills/avatar-video-automation/SKILL.md`** — lessons learned building a custom HeyGen avatar/voice and wiring it into an unattended, scheduled video pipeline.
- **`skills/discord-bot-provisioning/SKILL.md`** — lessons learned writing an idempotent discord.py script that provisions a server's categories, channels, roles, and permissions.

## How to use it

1. **Set up the base stack.** Follow `fullstack-agent`'s own installer to get memory (`ai-memory-vault`), voice (`backtalk`), and the rest of the base agent running. This repo assumes that part is already working.
2. **Give your agent a persona.** Copy `persona/CORTANA.md` into your own identity/instruction file (e.g. your `CLAUDE.md` or whatever file your agent loads at startup), and fill in every bracketed `[FILL IN: ...]` marker with your own values — assistant name, avatar/voice IDs, personality, channels. Do not commit real IDs or credentials to a public repo.
3. **Add subagents.** Read `agents/README.md` for how Claude Code discovers and routes to subagents, then copy `agents/example-specialist.md` into your own `.claude/agents/` folder (or `~/.claude/agents/` to make it available everywhere) as a starting point for your own specialists.
4. **Add the skills.** Copy the `skills/avatar-video-automation/` and `skills/discord-bot-provisioning/` folders into your own `.claude/skills/` folder if either applies to what you're building.

## Not included yet

A guided installer wizard (something closer to `fullstack-agent`'s own setup flow, but for the persona/subagent/skills layer in this repo) is planned as a future addition. For now, the steps above are manual.

## License

AGPL-3.0-or-later. See [`LICENSE`](./LICENSE).
