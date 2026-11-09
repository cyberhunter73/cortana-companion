---
name: discord-bot-provisioning
description: How to build a one-time, idempotent discord.py provisioning script that sets up a Discord server's categories, channels, roles, permissions, pinned messages, server name, and icon. Use this whenever the user wants to stand up or redesign a Discord server (categories/channels/roles, a welcome/rules/announcements setup, read-only announcement channels, a custom server name or icon) via a bot script rather than clicking through Discord's UI by hand. Covers real pitfalls hit building exactly this (Pin Messages vs Manage Messages permission split, overwrite-replace-not-merge semantics, discord.py API version drift, Windows path/encoding gotchas, square-icon requirements) so they aren't rediscovered from scratch.
---

# Discord Bot Provisioning

Built from a real multi-round community-server build (8 categories, 15 text channels, 2 voice channels, a role, pinned welcome/rules/announcements, a custom name and icon). The pattern: a **one-time idempotent script**, not a persistent bot process — it connects, diffs against the live server, applies whatever's missing or wrong, logs a summary, and exits. Re-running it is always safe.

## Overall shape

- **Idempotent by name lookup.** Look up every category/channel/role by name before creating it. Never assume a fresh server — assume a partially-provisioned one (re-runs after a partial failure are the normal case, not an edge case).
- **`--dry-run` should still connect live.** A fully offline dry run can't produce a meaningful diff ("what's missing vs. what exists") — it can only ever say "create everything." Connect read-only, diff against the real guild, log every action that *would* be taken, but don't call any create/edit/send/pin endpoint.
- **Wrap each step individually.** Catch `discord.Forbidden`/`discord.HTTPException` per category/channel/role/message, log and count it, and keep going — one missing permission shouldn't abort the whole run. Report created/updated/skipped/errors in a final summary line, non-zero exit code on any error, with a note that re-running is safe.

## The big one: Pin Messages is NOT covered by Manage Messages

Discord split **Pin Messages** into its own permission bit (`1 << 51`), separate from **Manage Messages** (`1 << 13`), in a newer permissions model (present in discord.py 2.7+). A bot can have Manage Messages and still get a genuine 403/50013 on `message.pin()` specifically, while `channel.send()` on the same channel with the same overwrite works fine.

If a bot can send to a channel but can't pin in it, check for `pin_messages` explicitly in its overwrite — don't assume Manage Messages covers it. This single gap caused a long multi-round debugging loop (checked overwrite correctness, propagation delay, timeout status, raw error codes, embed permissions) before phase-tracking logging (log whether the failure happened during `channel.send()` vs. `message.pin()`) revealed it was pin-specific. **Add phase-tracking logging from the start** on any send+pin flow — it would have shortcut the whole loop.

## Bot self-lockout on read-only channels

When you apply a read-only `@everyone` overwrite (`view_channel=True, send_messages=False`) to a welcome/rules/announcements channel, the bot's server-wide OAuth2 permissions do **not** override that channel-specific deny. Only an explicit channel-level ALLOW overwrite on the bot's own member/role does. If the bot needs to post+pin in a locked-down channel, give it its own explicit overwrite on that channel: `view_channel=True, send_messages=True, manage_messages=True, pin_messages=True` (see above for why all four, not three).

## `channel.edit(overwrites=...)` fully replaces, never merges

Confirmed via introspection against the real installed discord.py: `TextChannel.edit(overwrites=...)` sends a complete replacement of the channel's `permission_overwrites` array — built solely from the dict you pass. If a channel already has other overwrites (e.g. a `Member` role's), merge them into your dict yourself before calling edit; passing only your new entries silently wipes everything else out.

## Verify discord.py API shape against the actually-installed version, not memory or old docs

This library's async surface shifts between versions in ways that break at runtime, not at compile/lint time:

- `TextChannel.pins()` changed from a coroutine returning a list to an async iterator in discord.py 2.6+. Use `[message async for message in channel.pins()]`, not `await channel.pins()`.
- `Message.delete()` / `PartialMessage.delete()` only accepts an optional `delay` kwarg — **not** `reason`, even though `Message.pin(reason=...)` *does* accept `reason`. This asymmetry is easy to miss if you pattern-match one call's signature onto another.

Before relying on any discord.py call signature, check it directly: `python -c "import discord, inspect; print(inspect.signature(discord.TextChannel.edit))"` (or equivalent) against the actually-installed `.venv`, rather than assuming based on older examples or general familiarity.

## Windows-specific gotchas

- **Force UTF-8 on stdout/stderr.** Windows consoles default to a legacy codepage that can't render em dashes or emoji used in channel names/log messages — this corrupts output silently rather than erroring. Fix with `sys.stdout.reconfigure(encoding="utf-8")` (and same for stderr) rather than stripping emoji from the spec.
- **Git Bash POSIX paths (`/c/Users/...`) don't translate for every consumer.** Git Bash's MSYS2 layer rewrites POSIX-style paths for its own built-in commands (`cp`, `ls`, `rm`), but anything it `exec`s as an external program — including the native Windows Python interpreter — gets the raw string, which Windows' file APIs can't open (`FileNotFoundError`). If a Python one-liner invoked from the Bash tool fails to find a file that `cp`/`ls` just confirmed exists at that same path, rewrite the path in Windows style (`C:\Users\...`) for the Python call, or run the equivalent through the `PowerShell` tool instead, which never has this ambiguity.

## Server icon upload

`guild.edit(icon=<bytes>)` needs the raw bytes of a **square** image — unlike Discord's own browser upload UI, the API does not auto-crop a rectangular image into a circle/square. If the source image (e.g. a generated logo/icon concept) isn't already square, center-crop it first (Pillow: `img.crop((left, 0, left+side, side))` where `side = min(w, h)`) before reading it for the edit call. Read the file in binary mode (`icon_path.read_bytes()`). There's no cheap way to check "is this already the exact image on the server" without downloading and re-hashing the live icon, so it's fine to just always apply it unconditionally on every run — same bytes in each time, so the effect is idempotent even if the call itself isn't skip-gated.

## Scoping conversation before building

Before writing the script, nail down in a short brainstorming pass: server theme/name, full category/channel list, roles (and whether any reaction-role/verification gating is actually needed — for a small friends server, often not), and the exact copy for welcome/rules/announcements. Getting this approved up front avoids re-opening the provisioning script for cosmetic changes after it's already been tested live.
