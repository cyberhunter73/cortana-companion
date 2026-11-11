# cortana-companion

A companion layer for an AI agent built with [Jared Rhod's](https://github.com/jaredrhod) open-source agent stack:

- [`fullstack-agent`](https://github.com/jaredrhod/fullstack-agent) is the guided installer that builds the agent.
- [`ai-memory-vault`](https://github.com/jaredrhod/ai-memory-vault) gives it a memory made of plain notes in an Obsidian vault.
- [`backtalk`](https://github.com/jaredrhod/backtalk) lets you talk to it out loud and hear it answer.
- [`ai-visualizer`](https://github.com/jaredrhod/ai-visualizer) gives it a live face on screen.

This repo copies none of that code. It links to those projects and adds a layer on top: a guided setup, a persona template, an example helper agent, three skills, free phone sync for Android, and two guides to voice and face options. Everything here is template and example content. There are no real names, business names, credentials, or account IDs anywhere in it.

## Demo video

https://github.com/user-attachments/assets/afe3a368-ba12-4000-8309-9929b4330bf3

A walkthrough of an assistant built on this pattern: memory, dashboard, calendar/bid tracking, chat/email integration, and the tool stack used to make the video itself. (Also available as a direct download: [demo/cortana-capabilities-demo.mp4](demo/cortana-capabilities-demo.mp4).)

## What's included

```
.gitignore
CLAUDE.md
LICENSE
README.md
agents/README.md
agents/example-specialist.md
demo/cortana-capabilities-demo.mp4
guides/face-options.md
guides/voice-options.md
persona/CORTANA.md
setup/cortana-setup.md
skills/avatar-video-automation/SKILL.md
skills/discord-bot-provisioning/SKILL.md
skills/free-vault-sync/SKILL.md
```

- **`setup/cortana-setup.md`** is the guided setup. Claude Code reads it and walks you through each part, asking before every change.
- **`CLAUDE.md`** tells Claude Code to run that setup when you say "set me up" in this folder.
- **`persona/CORTANA.md`** is a fill-in template for extras you may want in your agent's boot file, with bracketed markers in place of real values and a made-up example.
- **`agents/README.md`** and **`agents/example-specialist.md`** explain how Claude Code helper agents work, with one complete example you can copy and adapt.
- **`skills/avatar-video-automation/SKILL.md`** holds lessons from building a custom HeyGen avatar and voice and running it as an unattended, scheduled video pipeline.
- **`skills/discord-bot-provisioning/SKILL.md`** holds lessons from writing a script that sets up a Discord server's categories, channels, roles, and permissions.
- **`guides/voice-options.md`** and **`guides/face-options.md`** compare your choices for a voice and a face, with links to the official pages and a last checked date.
- **`skills/free-vault-sync/SKILL.md`** walks your own Claude Code through syncing your Obsidian vault between a Windows computer and an Android phone with Syncthing, for free and with no account. It covers ignore rules that keep a git-backed vault clean, pairing, versioning, testing, and what to do with conflict files.

## How to use it

There are two steps. The first builds your agent. The second adds this repo's layer on top.

### Step 1: build the base agent

Follow the Install section of [`fullstack-agent`](https://github.com/jaredrhod/fullstack-agent). One paste starts a guided setup that gives you the agent, its memory vault, Obsidian, and the optional voice and face. Say yes to the memory piece, because everything here builds on it. Use the command on that page, since it changes over time.

### Step 2: add the companion layer

Open a new terminal after Step 1 finishes, then pick one of these.

**One paste.** This puts the repo inside your agent's folder and starts the guided setup. If your agent lives somewhere other than `my-agent` in your home folder, change that part first.

Use the block that matches your computer. The Windows one will not run in a Mac or Linux terminal, and the other way around.

**Windows (PowerShell)**

```
$d="$env:USERPROFILE\.local\bin"; if (Test-Path "$d\claude.exe") { $env:Path="$d;$env:Path" }; cd $HOME\my-agent; if (-not (Test-Path cortana-companion)) { git clone https://github.com/cyberhunter73/cortana-companion }; cd cortana-companion; claude "set me up"
```

The first part of that line helps Windows find Claude Code if it was installed a few minutes ago.

**Mac or Linux (Terminal)** (not yet tested, tell us how it goes)

```
cd ~/my-agent && { [ -d cortana-companion ] || git clone https://github.com/cyberhunter73/cortana-companion; } && cd cortana-companion && claude "set me up"
```

**Or by hand.** Clone this repo into your agent's folder, open the `cortana-companion` folder in Claude Code, and say "set me up".

Either way, Claude Code checks that Step 1 worked, then offers each part one at a time: persona extras, a helper agent, the skills that fit what you are building, and free phone sync for Android. Every part is optional. It shows you each change before making it, and it never deletes, overwrites, or moves anything you already have.

Windows with an Android phone is the tested path. Mac and Linux are untested until someone tries them.

The two guides in `guides/` carry a last checked date. Recheck their links every few months.

## What this does not do

- It does not build the agent. That is Step 1.
- It does not install a voice or a face. Step 1 offers those, and the two guides explain the choices.
- Phone sync covers Android only. There is no official Syncthing app for iPhone.
- Mac and Linux are untested.
- It never signs in to, reads, or changes any of your accounts.

## License

AGPL-3.0-or-later. See [`LICENSE`](./LICENSE).
