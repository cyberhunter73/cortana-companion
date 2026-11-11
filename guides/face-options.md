# Face options

Last checked: 2026-11-11. Sites change their menus and sign-up steps. If a step here does not match what you see, trust the site, and ask your agent to help you find the new spot.

Read this first, because it saves the most common mix-up. These two options do different jobs.

- The live face is a moving picture on your screen that reacts while your agent listens, thinks, and talks.
- HeyGen makes videos of a talking person or character. You give it words, wait, and get a video file back. It is not a live face on your screen.

Many people want both: the live face for daily use, and HeyGen for videos they post or send.

## The short comparison

| | Live face (ai-visualizer) | HeyGen |
|---|---|---|
| What it is | A web page that animates along with your agent | A hosted service that makes talking avatar videos |
| Live or video | Live | Video |
| Cost to start | No account needed | Check [their pricing page](https://www.heygen.com/pricing) |
| What you need | A browser and Python 3 | An internet connection and a HeyGen account |
| Your own face | One of the four faces can show your own picture | You can make an avatar from your photo, from a short recording of yourself, or from a written description |
| Who it suits | Anyone who wants their agent to feel present on screen | Anyone who wants to make videos with a presenter |

## Option 1: the live face

This is [ai-visualizer](https://github.com/jaredrhod/ai-visualizer) by Jared Rhod. The base installer sets it up.

1. Run the base installer from the Install section of [fullstack-agent](https://github.com/jaredrhod/fullstack-agent) and say yes to the face piece. If you already ran it without the face, run it again. It only adds what is missing.
2. Pick a face when it asks. Four come with it: a circuit board, a starburst, falling code with a face inside it, and a constellation brain. You can change your mind any time.
3. Start your agent with the Talk shortcut on your Desktop. The face opens in your browser and moves with the conversation.
4. Want your own picture? Choose the falling code face, then ask your agent: "use my own picture for the face." You need a portrait on a plain black background. Your agent puts it in the right place for you.

The face shows the real conversation only when the voice piece is installed too. Without a voice it plays a demo.

## Option 2: HeyGen

This part covers what a beginner needs: an account, your own avatar, and the two IDs your agent uses. For building a video that makes itself on a schedule, see `skills/avatar-video-automation/SKILL.md` in this repo.

### Make an account

1. Go to [heygen.com](https://www.heygen.com) and choose Get started for free.
2. At the last check, their pricing page said you can try it with no credit card. Check [their pricing page](https://www.heygen.com/pricing) for what each plan costs and includes.

### Make your own avatar

At the last check, all three ways started in the same place: Avatar, then Avatars, then New Avatar.

- From a photo. Choose Create a virtual character, then Upload photo. Upload a clear picture, name the avatar, and wait for it to be approved. [Their guide](https://help.heygen.com/en/articles/10034438-how-to-get-started-with-photo-avatars).
- From a written description, with no real person involved. Choose Create a virtual character, then Design with AI, and describe the look you want. This is the way to get a cartoon, a hologram, or any character that is not you. Same guide as above.
- From a short recording of yourself. Choose Clone a real person, then record with your webcam or phone, or upload a clip. HeyGen also asks for a short consent recording, to confirm the person in the video agreed. [Their guide](https://help.heygen.com/en/articles/12089286-create-your-first-digital-twin-video-avatar-with-avatar-iv).

Only make an avatar of a real person if that person is you, or has clearly said yes.

### Find your avatar ID and voice ID

Your agent needs two IDs to make videos for you: one for the look and one for the voice. We could not find them shown anywhere in the HeyGen web app. They come from HeyGen's developer side.

The easy way: if your Claude has the HeyGen connector turned on, ask your agent "list my HeyGen avatars and voices and show me the IDs." Then ask it to save the two you want in its boot file. `persona/CORTANA.md` in this repo has a spot for them.

The other way is an API key, which is a password that lets your agent use your HeyGen account. At the last check it was under the Developers icon in the left sidebar, then API keys. HeyGen sells API use separately from its regular plans, so check [their page on it](https://help.heygen.com/en/articles/10060327-new-heygen-api-plans) first. Treat the key like a bank card number and never paste it into a chat, a note, or any file you share.

### If you want a live talking avatar after all

HeyGen has a separate product for that, called [LiveAvatar](https://www.liveavatar.com/). Nothing in this repo sets it up, and we have not tested it.

## Recheck

These links and menu names were checked on the date at the top. Recheck them every few months, or whenever a step stops matching.
