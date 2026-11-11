---
name: free-vault-sync
description: Walk the user through a free, account-free sync of their Obsidian vault between a Windows computer and an Android phone using Syncthing, so the phone opens the same notes without a paid sync service. Use this when the user wants their assistant's vault on their phone, asks about Obsidian sync without a subscription, or mentions Syncthing. Covers the install on both devices, ignore rules that keep a git-backed vault clean, pairing, versioning, testing, conflict files, and recovering deleted notes, plus real pitfalls hit doing exactly this.
---

# Free Vault Sync

This skill sets up Syncthing so an Obsidian vault on a Windows computer stays in sync with an Android phone. Syncthing copies files straight between your own devices. There is no account, no cloud copy, and no monthly fee.

It was written from a real setup on a Windows laptop and an Android foldable, with every test below run for real.

## How to run this skill

Go one step at a time. Explain each step in plain words, say exactly what will change before changing it, and wait for the user's yes before any install, config change, or file edit. Ask any questions one at a time.

Never open the Syncthing config file (`config.xml`). It holds an API key. Use the `syncthing cli` commands shown below instead, which read the config for you.

Let the user set the Syncthing web page password themselves, in their browser, so it never shows up in the chat.

## Before you start

1. **Check that a vault exists.** Ask the user where their Obsidian vault is and confirm the folder exists. If they have no vault and no Obsidian yet, stop and send them to the first step of the base agent installer first. This skill assumes the vault is already in use.
2. **Check where the vault lives.** If it is inside OneDrive, iCloud Drive, Google Drive, or Dropbox, warn them. Those services can turn files into online-only placeholders that Syncthing and the agent cannot read, and two sync tools fighting over one folder causes conflicts. Recommend moving the vault to a plain local folder first.
3. **Check the phone.** This skill covers Android. There is no official Syncthing app for iPhone, so iPhone users need a different route (Obsidian's paid sync, or a third-party iOS app they choose themselves). Say so plainly and stop if they only have an iPhone.
4. **Ask two questions, one at a time:**
   - Will the phone be used for new notes and reading only, or for editing anything? Recommend new notes and reading only, with new notes in an inbox folder. Files the agent also writes, like daily notes and folder index notes, are the most likely to clash.
   - Sync on Wi-Fi only, or cellular too? Recommend Wi-Fi while charging to start.
5. **Make a safety copy.** If the vault is a git repo, check that the last push worked and that `git status` is clean. Either way, copy the whole vault to a dated folder outside the vault before changing anything.

## Step 1: install Syncthing on Windows

Use the official build from The Syncthing Authors:

```
winget install --id Syncthing.Syncthing --exact
```

Then find where it landed. This winget package does not create a shortcut in `WinGet\Links`. It adds its own versioned folder to the PATH instead, so a path guessed from the Links folder will fail with error `0x80070002` (file not found). Find the real one:

```
Get-ChildItem "$env:LOCALAPPDATA\Microsoft\WinGet\Packages" -Recurse -Filter syncthing.exe | Select-Object -ExpandProperty FullName
```

Do not upgrade Syncthing through winget later. Syncthing updates itself inside the same folder. A winget upgrade installs to a new versioned folder and breaks the startup task below.

## Step 2: start it with Windows

Create a Scheduled Task that runs at logon, hidden, and restarts if it crashes. Save this as a `.ps1` file, fill in the path from step 1, and run it with `powershell -NoProfile -ExecutionPolicy Bypass -File <file>`:

```
$exe = "[FILL IN: full path to syncthing.exe from step 1]"
$a = New-ScheduledTaskAction -Execute $exe -Argument 'serve --no-browser --no-console'
$t = New-ScheduledTaskTrigger -AtLogOn -User "$env:USERDOMAIN\$env:USERNAME"
$s = New-ScheduledTaskSettingsSet -AllowStartIfOnBatteries -DontStopIfGoingOnBatteries -ExecutionTimeLimit ([TimeSpan]::Zero) -RestartCount 3 -RestartInterval (New-TimeSpan -Minutes 1) -Hidden
Register-ScheduledTask -TaskName Syncthing -Action $a -Trigger $t -Settings $s | Out-Null
Start-ScheduledTask -TaskName Syncthing
```

Two things that tripped up the real setup:

- Claude Code's safety checks may block an agent from creating a logon task, even with the user's approval. If that happens, have the user run the script themselves.
- If the user runs it from Claude Code's `!` prompt, that prompt runs through bash. A PowerShell one-liner with `$variables` in double quotes gets emptied by bash before PowerShell sees it. Running a `.ps1` file avoids this. Put the path in single quotes.

Check it worked: the task state should be Running, and http://127.0.0.1:8384/rest/noauth/health should return `{"status": "OK"}`. Two `syncthing` processes is normal (a monitor and the main process). To undo: `Unregister-ScheduledTask Syncthing`.

## Step 3: lock the web page

Have the user open http://127.0.0.1:8384, go to Actions, Settings, GUI, and set a username and password. Leave the listen address as `127.0.0.1:8384` so the page is only reachable from this computer. Then confirm a request without a login is refused (it should return 403).

## Step 4: ignore rules, then share the vault

Create a `.stignore` file in the vault before sharing it, so the rules apply from the first sync:

```
.git
.stversions
.obsidian/workspace.json
.obsidian/workspace-mobile.json
```

`.git` matters most. A half-copied git folder on the phone can damage the repo. The two workspace files are Obsidian's window layout, which changes constantly and is different on each device.

If the vault is a git repo backed up on a schedule, add these lines to its `.gitignore` so Syncthing's own files never get committed:

```
.stfolder
.stversions
*.sync-conflict-*
.syncthing.*.tmp
~syncthing~*.tmp
```

Then share the vault with 30 days of staggered versioning. Fill in the vault path:

```
syncthing cli config folders add --id my-vault --label "My Vault" --path "[FILL IN: full path to the vault]" --type sendreceive
syncthing cli config folders my-vault versioning type set staggered
syncthing cli config folders my-vault versioning params set maxAge 2592000
syncthing cli config folders my-vault versioning params set cleanInterval 3600
```

Check that a `.stfolder` marker now exists in the vault and that `git status` does not list it. The marker is a safety feature: if the vault folder ever goes missing, Syncthing stops instead of treating it as "everything was deleted" and wiping the phone's copy.

Get the computer's device ID for pairing with `syncthing cli show system` (the `myID` field). A device ID is not a secret. Pairing still has to be approved on both devices.

## Step 5: set up the phone

Walk the user through these one at a time.

1. **Install two apps from the Play Store:** Obsidian, and Syncthing-Fork (`com.github.catfriend1.syncthingandroid`). The official Syncthing Android app was discontinued and archived in December 2024. Syncthing-Fork is the common replacement. It changed maintainers, so check its store listing and recent reviews before installing. Another option is "Syncthing for Android" by dev.benedek, a revival of the original app. Check the current state of both before recommending one.
2. **Permissions.** Allow storage or "All files access", which is required. Location can be denied. Android only needs it to read the Wi-Fi network name, which the app uses for an optional "only on these networks" rule. That rule needs "Allow all the time" if the user wants it later.
3. **Pair.** In Syncthing-Fork, open Devices, tap plus, and scan the QR code from the computer's web page (Actions, Show ID). Name the device.
4. **Check the phone's ID before accepting.** The computer's web page shows a new-device banner. Read the waiting device with `syncthing cli show pending devices`, and have the user compare the first and last blocks of that ID with the one on the phone (Syncthing-Fork menu, Show device ID). Only then accept it, and tick the vault under Sharing.
5. **Accept the folder on the phone.** The share offer arrives as an Android notification, not a popup in the app. If nothing shows up, notifications are probably turned off for the app. Set the folder path to `/storage/emulated/0/Documents/My Vault`. Documents is shared storage that both apps can reach. Leave the type as Send & Receive.
6. **Add the same four ignore lines on the phone.** Open the folder in Syncthing-Fork, find Ignore patterns, and enter them. Ignore files are not synced between devices, so each side needs its own.
7. **Open the vault in Obsidian.** Choose Open folder as vault, pick Device storage (not App storage, which Syncthing cannot reach), and select the vault folder itself. If it asks whether to trust the author and enable plugins, check the vault's `.obsidian/plugins/*/manifest.json` files first. Any plugin with `"isDesktopOnly": true` will not load on the phone. Plugins the user already runs on the computer are fine to trust.
8. **Run conditions.** In Syncthing-Fork settings, under Run conditions, match what the user chose (for example Wi-Fi on, mobile data off, charging only).
9. **Stop Android from killing it.** Set the app's battery use to Unrestricted. On Samsung phones, also check Battery, Background usage limits, and make sure the app is not in Sleeping or Deep sleeping apps. It may not appear in the "Never sleeping" picker once it is Unrestricted, which is fine.

## Step 6: test

Run each test and report the result in plain words.

1. **Phone to computer.** Make a note on the phone and watch it appear on the computer.
2. **Computer to phone.** Change a note on the computer and watch it change on the phone.
3. **Conflict.** Pause the phone from the computer (`syncthing cli config devices <phone ID> paused set true`), edit the same note on both, then unpause. Syncthing should keep the newer edit under the real name and save the other as `<note>.sync-conflict-<date>-<time>-<device>.md`. Nothing should be silently lost.
4. **Delete and recover.** Delete a throwaway note on the phone. Obsidian moves it to the vault's `.trash` folder, and the computer also keeps a copy in `.stversions`, with the time it was saved at the end of the name. Copy it back from `.stversions` and watch it return on the phone.
5. **Plugins.** Confirm Obsidian on the phone opens with no plugin errors.

Move the test notes out of the vault afterwards.

## Living with it

- **Conflict files.** A `.sync-conflict` file means both devices changed the same note while apart. Open both, merge what you want into the main note, then remove the conflict copy. If the vault is in git, these files are ignored, so nothing will remind you they exist. Add a line to your agent's instructions telling it to look for `*.sync-conflict-*` files at the start of each session and report them.
- **Recovering a note.** Look in `.stversions` on the computer (kept for 30 days) or in the vault's `.trash` folder if it was deleted in Obsidian.
- **New notes on the phone.** Obsidian's New note button saves to the vault's top folder, not the folder you have open. Set the default location for new notes in Obsidian's settings to your inbox folder. Also, tapping a folder can open its same-named index note, which is an easy way to edit the wrong file by accident.
- **Typing on a phone.** Hand-editing a long line on a small screen can cut words off. That is one more reason to keep the phone to new notes and reading.
- **Harmless git noise.** Obsidian on the phone sometimes re-saves a file with identical content. Git may list it as modified with an empty diff. Nothing gets committed.
- **Stopping Syncthing.** It runs as two processes, a monitor and the main one. Stopping only the monitor's process ID leaves the main one running and holding its lock, and a fresh start then fails with "Failed to acquire lock". Use `Stop-ScheduledTask Syncthing`, or stop both processes.
- **Optional privacy setting.** By default Syncthing uses public discovery servers and relays, and may open a port on the home router (UPnP) so devices can find each other away from home. If both devices only ever sync on the same home Wi-Fi, you can turn off Global Discovery, Enable Relaying, and Enable NAT traversal in Actions, Settings, Connections. Local discovery keeps working on the home network. Sync stays end-to-end encrypted either way.
- **Both devices must be awake.** Syncthing only syncs while both devices are on at the same time. If the computer sleeps at night, changes wait until both are up. An always-on third device, such as a small home server, fixes this.
