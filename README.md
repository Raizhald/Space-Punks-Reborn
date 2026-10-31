# Space Punks Reborn

An unofficial, fan-made **offline launcher** for the Space Punks Open Beta. The official servers are gone – this launcher lets you play the game on your PC without any server or Epic login.

> Not affiliated with Flying Wild Hog or Jagex. This repository contains no game files.

Current version: **V10**

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
- **Multiplayer (LAN)** – play the hub and missions together with friends (new in V9).
- **AI bots (experimental)** – up to 3 AI teammates that fight with you in missions (new in V10).

## Multiplayer

1. One player opens the **Multiplayer** tab and clicks **Host game**. The tab shows their address (for example `192.168.1.20`).
2. The other players type that address into the field and click **Join**.
3. Start missions from the hub as usual – the joined players follow the host into the mission and back to the hub. After every map change they reconnect automatically.

Notes:

- It works on the same network (LAN). Over the internet you can try a virtual LAN such as Hamachi, ZeroTier or Radmin VPN and use the address it gives you – this is not tested yet.
- If Windows asks, allow network access for the game (`SpacePunks-Win64-Host.exe`). The game uses port 7777 (UDP).
- You can't join a mission that has already started – join while the host is in the hub.
- Your save is backed up before every multiplayer game.
- To test on one PC: click **Host game**, then join `127.0.0.1` – the second game uses a separate test profile.
- The game kicks a player who doesn't move for a while (AFK), even in a mission.

## AI bots (experimental)

Open the **Bots** tab and choose Off / 1 / 2 / 3 bots, then click **PLAY**. The bots appear only in missions, follow you and shoot at enemies.

They are the game's own unfinished "sidekick" characters: they have no shooting animation, their damage is a fixed value (it doesn't scale with mission level) and the game may crash. Bots are used only with **PLAY**, not with Host / Join. Your save is backed up before every game with bots. Windows may ask to allow network access.

## Reporting bugs

Please open an **[issue](../../issues/new/choose)** using the bug report form, so problems stay searchable. Attach the newest game log from the `Launcher logs` folder next to the launcher. You can also message me on Discord: \_sestak\_

Technical notes: [docs/TECHNICAL.md](docs/TECHNICAL.md)
