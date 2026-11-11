# cortana-companion: setup

You are Claude Code, and the person in front of you wants the companion layer added to their agent. This file is the script. Read all of it first, then follow it in order.

This is Step 2 of two. Step 1 is the base agent installer from [fullstack-agent](https://github.com/jaredrhod/fullstack-agent) by Jared Rhod, which sets up the agent, its memory vault, Obsidian, and the optional voice and face. This file never repeats or replaces Step 1. It only adds to what Step 1 left behind.

## Ground rules

These hold for the whole run.

- Plain English. Assume the person installed Claude Code this week. Explain each thing in one line before you name it.
- One question at a time. Wait for each answer.
- Every part below is optional. Say what it is, say what it will change, and wait for a clear yes before you change anything.
- Never delete, overwrite, or move anything the person already has. If a file with the same name is already there, stop and ask. Offer a different name or skip it.
- Only look in the agent's home folder, the one vault its boot file points at, and this repo. Ask before looking anywhere else.
- Never use a connected account during this setup. That covers HeyGen, ElevenLabs, email, calendar, and any other connector or signed-in service this session can reach. Do not list, read, look up, or change anything in the person's accounts, even to be helpful, and even if they describe what they want. Do not offer to. This setup only works with files in the agent's home folder, its vault, and this repo.
- Never state a price or a free plan limit for any service. Say "check their site" and give the link.
- Write paths with forward slashes on Windows too. They work, and backslashes get lost on the way through the shell.
- Keep a running list of what you added and what the person skipped. You read it back at the end.

## Part 1: check that Step 1 is done

Do these checks yourself. Do not quiz the person about things you can look up.

1. Find the agent's home. It is the folder that contains this repo. It should also contain a folder named `fullstack-agent` and a file named `CLAUDE.md`. If both are there, that is home.

   If they are not there, ask: "Where does your agent live? It is the folder you open when you chat with it." If they are not sure, offer to find it from the shortcut or icon they use to open their agent. Read that one shortcut only, and only after they say yes. Check the folder they name, or the one the shortcut points to, the same way. If it is not an agent's home, say so and ask again. Leave this repo where it is. Do not move it.

   If they have no agent yet, say so kindly and stop: "This setup adds to an agent that already exists, and I can't find one yet. The base installer builds it. It is free and takes one paste." Send them to the Install section of https://github.com/jaredrhod/fullstack-agent and tell them to come back and say "set me up" again when it finishes. Do not paste his install command from memory. It changes, and his page always has the current one.

2. Read the boot file, `CLAUDE.md` in the home folder, start to finish. Take from it the agent's name, how it talks, and the vault path. Then say what you found: "Found [name]. I will keep the name and personality exactly as they are." Do not ask for the name or personality again.

3. Check the vault. Look for the vault path in the boot file.

   If the boot file has no vault path, the memory piece was skipped in Step 1. Stop here: "Your agent has no memory vault yet, and everything in this setup builds on one. Run the base installer again and say yes to the memory piece. It only adds what is missing." Link the same Install section and stop.

   If there is a path, confirm the folder exists and has a file named `VAULT-INDEX.md` at its top level. If the folder is missing, the vault was probably moved. Say so, point them at `ai-memory-vault/TROUBLESHOOTING.md` in their home folder, and stop.

4. Check that Obsidian is installed. On Windows look for `Obsidian.exe` under `%LOCALAPPDATA%/Programs/Obsidian`, or run `winget list --id Obsidian.Obsidian`. On Mac look for `Obsidian.app` in `/Applications` and `~/Applications`. On Linux try `which obsidian`, `flatpak list`, and `snap list`. If it is missing, stop and send them back to Step 1, the same way as above. Do not install it yourself. That is Step 1's job.

   Do not open Obsidian's own list of vaults to find the vault. That list shows every vault the person owns, and the others are none of your business.

5. Note which optional pieces are there. A `backtalk` folder in home means the voice piece is there. An `ai-visualizer` folder means the face piece is there. A folder alone does not prove the piece finished installing, so say "the voice piece is there" and not "the voice works". You use this in Part 3.

6. Tell the person what you found in three or four short lines: the home folder, the agent's name, the vault path, and whether the voice and face are there. Then ask: "Ready to add the companion layer? Every part is optional and I will ask before each one."

## Part 2: the companion layer

Offer these one at a time, in this order. After each one, say what you did or that you skipped it.

### A. Persona additions

Say: "Your agent already has a name and a personality. This part is for extras you might want written into its boot file, such as which channels it reaches you on. Want to add anything?"

If no, skip to B.

If yes, open `persona/CORTANA.md` in this repo and use it as the list of things to ask about. Skip the name and the personality unless the person asks to add to them. Ask one question at a time.

Do not ask about HeyGen or avatar and voice IDs. If the person brings them up on their own, tell them they can paste the IDs and you will add them. Only use IDs they paste. Never look them up.

Then build a short block from their answers. Show them the exact text, and say exactly where it goes: under the heading `## Make it yours` in their boot file, or at the very end of the file if that heading is not there. Tell them you will first save a copy of the boot file next to it, named `CLAUDE.md.before-companion`. If a copy with that name already exists, add today's date to the new one.

Only on a clear yes: make the copy, add the block, then read the file back and confirm the block is there and nothing else changed.

If they pasted avatar or voice IDs, those are fine in their own boot file on their own computer. Remind them once never to put those into anything public.

### B. A helper agent

Say: "Claude Code can hand a job to a helper with one narrow task, so your main agent stays focused. This repo has one example, a research helper that looks things up and reports back with sources. Want it?"

If yes, tell them it will be copied to `.claude/agents/research-specialist.md` inside their home folder. Create the folders if needed. If that file already exists, do not overwrite it. Ask whether to skip or use a different file name. After the copy, tell them `agents/README.md` in this repo explains how to write their own.

### C. Skills

A skill is a set of written lessons the agent reads when a matching job comes up. Offer only the ones that fit. Ask one question at a time.

1. "Do you plan to make talking avatar videos with HeyGen?" If yes, offer `skills/avatar-video-automation`. A yes here only means you offer the skill file. Do not ask for IDs, and do not open their HeyGen account.
2. "Do you want to set up or reorganize a Discord server?" If yes, offer `skills/discord-bot-provisioning`.

For each yes, tell them the folder will be copied into `.claude/skills/` inside their home folder, then copy it. If a folder with that name already exists there, do not overwrite it. Ask whether to skip.

### D. Your vault on your phone

Say: "Want your agent's notes on your phone too? This uses Syncthing, a free tool that copies files straight between your own devices, with no account. It works with Android phones."

Ask whether their phone is Android or iPhone.

If iPhone only, say plainly that this step does not cover iPhone because there is no official Syncthing app for it, and move on.

If Android, copy `skills/free-vault-sync` into `.claude/skills/` in their home folder, with the same no-overwrite rule. Then ask: "Set it up now, or later? It takes about twenty minutes and you need the phone in your hand." If now, read `skills/free-vault-sync/SKILL.md` and follow it faithfully, using the vault path you found in Part 1. If later, tell them the words to say when they are ready: "set up free vault sync."

## Part 3: voice and face options

Nothing gets installed in this part. It is a short menu so the person knows their choices. The details live in two guides in this repo: `guides/voice-options.md` and `guides/face-options.md`.

This part is for reading only. Do not offer to sign in to, connect, or set up HeyGen or ElevenLabs. Do not mention anything about what is in the person's accounts. If they want to switch voice or make an avatar, point them to the guide and stop there.

Start with what they already have, from Part 1 step 5.

Voice. There are two choices, and both run through the voice piece from Step 1.
- The built-in voice runs on their own computer with no voice account. It needs a microphone and a large download the first time.
- ElevenLabs is a hosted voice service on its own account. It sounds more natural. For cost, check their site.

If the voice piece is installed, tell them which one they are on if you can tell, and that they can switch by asking their agent. If it is not installed, tell them it comes from Step 1 and that running the base installer again adds it.

Face. There are two choices, and they do different jobs.
- The live face from Step 1 is a moving picture on screen that reacts while the agent listens and talks. Several faces come with it, and one of them can use the person's own picture.
- HeyGen makes talking avatar videos. It is not a live face on screen. Say this up front so nobody picks it expecting the wrong thing. It is a separate service on its own account. For cost, check their site.

Ask whether they want to read either guide now. If yes, open it and walk through it with them.

## Part 4: wrap up

1. Read back the list: each thing you added with its full path, and each thing they skipped.
2. Tell them how to undo any of it. Each added file or folder can simply be deleted. The boot file change can be undone by removing the block you showed them, and the copy you saved first is right next to it.
3. Tell them to close their chat with the agent and open it again, so the new helper and skills load.
4. Tell them how to come back. Anything they skipped can be added later by opening this folder in Claude Code and saying "set me up" again. You will skip what is already there.
5. Credit. The agent, the memory, the voice, and the face are Jared Rhod's work at https://github.com/jaredrhod. This repo only adds a layer on top.
