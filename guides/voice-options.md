# Voice options

Last checked: 2026-11-11. Sites change their menus and sign-up steps. If a step here does not match what you see, trust the site, and ask your agent to help you find the new spot.

Your agent can talk out loud in two ways. Both run through [backtalk](https://github.com/jaredrhod/backtalk), the voice piece by Jared Rhod that the base installer sets up. You are choosing which voice it speaks with, not which program to install.

## The short comparison

| | Built-in voice | ElevenLabs |
|---|---|---|
| What it is | A voice that runs on your own computer | A hosted voice service with its own account |
| Cost to start | No voice account needed | Check [their pricing page](https://elevenlabs.io/pricing) |
| How it sounds | Clear, but you can tell it is a computer | Much closer to a real person |
| What you need | A microphone and about 1 GB of downloads the first time | A microphone, an internet connection, an ElevenLabs account, and a small free tool called ffmpeg |
| Works offline | Yes | No |
| Who it suits | Anyone starting out, or anyone who wants everything on their own machine | Anyone who talks to their agent a lot and wants it to sound natural |

You do not have to pick once and live with it. The built-in voice always stays installed as the backup, so if the hosted voice ever fails, your agent still talks.

## Option 1: the built-in voice

1. Run the base installer from the Install section of [fullstack-agent](https://github.com/jaredrhod/fullstack-agent) and say yes to the voice piece. If you already ran it without the voice, run it again. It only adds what is missing.
2. When it asks which voice engine you want, pick the built-in one.
3. Let the first run finish its downloads. It can sit for several minutes and look stuck. It is not.
4. Want a different built-in voice? Open a chat with your agent and say "let me hear the other built-in voices." It plays them and saves the one you pick.

## Option 2: ElevenLabs

You do not set this up by hand. The backtalk setup walks you through it one step at a time, and your agent does the typing. The steps below are so you know what is coming.

1. Make an account at [elevenlabs.io/app/sign-up](https://elevenlabs.io/app/sign-up). For what each plan costs and includes, and whether you need a card to start, check [their pricing page](https://elevenlabs.io/pricing).
2. Open a chat with your agent and say "switch my voice to ElevenLabs." If you are running the base installer for the first time, pick ElevenLabs when it asks about the voice engine.
3. Make an API key when your agent asks for one. An API key is a password that lets your agent use your ElevenLabs account. At the last check it was under Developers in the left sidebar, on the API Keys tab. Their own page on it is [here](https://elevenlabs.io/docs/help-center/technical/how-do-i-authorize-myself-using-an-api-key.md).
4. Give the key to your computer, not to the chat. Your agent tells you the exact command to type in your own terminal window. Never paste the key into a chat, a note, or any file.
5. Pick a voice by ear. Your agent plays a few through your speakers and you choose. You can also browse the [Voice Library](https://elevenlabs.io/app/voice-library) yourself.
6. Let your agent install ffmpeg if it is missing. It asks first.

The full walkthrough lives in backtalk's own setup file. If anything goes wrong, tell your agent what happened. backtalk ships a troubleshooting guide written for your agent to read.

## Keeping the key safe

Treat the API key like a bank card number. Do not put it in your vault, in a screenshot, or in anything you share. If you think someone saw it, delete it on the ElevenLabs site and make a new one. ElevenLabs also lets you limit what a key can do, which is described on the same page linked in step 3.

## Recheck

These links and menu names were checked on the date at the top. Recheck them every few months, or whenever a step stops matching.
