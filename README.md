# Space Punks Reborn

An unofficial, fan-made **offline launcher** for the Space Punks Open Beta. The official servers are gone – this launcher lets you play the game on your PC without any server or Epic login.

> Not affiliated with Flying Wild Hog or Jagex. This repository contains no game files.

Current version: **V8**

## Get the game

Download the Space Punks Open Beta from the Internet Archive:
**https://archive.org/details/space-punks-beta-epic-games-launcher**

Extract it to a writable folder. The game folder is the `SpacePunks` folder that contains `K1` (inside `Local Disk C\Program Files\Epic Games`).

## Install

1. Download `Space.Punks.Reborn.exe` from the **[latest release](../../releases/latest)**.
2. Put the downloaded launcher next to the game folder.
3. Start it, set your nickname and click **PLAY**. If the launcher asks for the game folder, select the `SpacePunks` folder.

The first PLAY prepares the game automatically. The original game files are kept.

If the game reports missing runtime components, run `Engine\Extras\Redist\en-us\UE4PrereqSetup_x64.exe` from the game folder.

## Features

- **Offline play** – starts straight into the hub, no server or Epic login needed.
- **Heroes and missions** – choose and buy heroes, play missions and return to the hub afterwards (also via Abort Mission or Esc → Main Menu).
- **Your nickname** in game.
- **Premium store** – add premium currency in the launcher and buy items in the game.
- **Profile overview** – heroes, resources and time played, read from your save. Your save is backed up before every change.

## Reporting bugs

Please open an **[issue](../../issues/new/choose)** using the bug report form, so problems stay searchable. Attach the newest game log from the `Launcher logs` folder next to the launcher. You can also message me on Discord: \_sestak\_

## Why are the AI bots gone?

Versions V1–V7 could add up to 3 AI bots to missions. In V8 this option was **removed**. The bots were the game's own "sidekick" characters, spawned through a developer cheat that is still in the client. Testing showed that the sidekick feature was never finished in the Open Beta: the bots find and aim at enemies and switch to "firing", but no shot is ever fired (they have no shooting animation – they use the hostage NPC animations – and their ammo never goes down). Making them fight would mean writing new game code, so the option was removed instead of shipping something that doesn't work.

Technical notes: [docs/TECHNICAL.md](docs/TECHNICAL.md)
