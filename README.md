# Space Punks Reborn

An unofficial, fan-made **offline launcher** for the Space Punks Open Beta (Unreal Engine 4.27, build REL_UPDATE_9 / 1,221,514). The official servers are gone; this launcher lets the existing client start, load the hub, pick a hero and play missions on one PC without any backend or Epic login.

> **Not affiliated with Flying Wild Hog or Jagex.** This repository contains **no game files**. You need your own copy of the Space Punks Open Beta client. The launcher only applies small, hash-checked patches to that copy on your PC.

Current version: **V6** (shown in the top-left corner of the launcher).

---

## Install

**Requirements:** Windows 10/11, .NET Framework 4.x (already part of Windows) and your own Space Punks Open Beta client.

1. **Copy the game** to a writable folder, e.g. `C:\Games\Space Punks Reborn\SpacePunks-experiment`. It must contain `K1\Binaries\Win64\SpacePunks-Win64-Shipping.exe`.
2. **Download the launcher**: open the [`release`](release) folder, click `Space Punks Reborn.exe` and then *Download raw file*.
3. Put `Space Punks Reborn.exe` in the folder **above** the game folder, e.g. `C:\Games\Space Punks Reborn\Space Punks Reborn.exe`. (If your game folder is named differently or is somewhere else, the launcher asks you to pick it.)
4. Start `Space Punks Reborn.exe`, set your nickname (**Rename**) and bots (**Bots**) if you want, and click **PLAY**.

On the first PLAY the launcher prepares the game automatically (a few seconds): it creates `SpacePunks-Win64-HubReturn.exe` next to the original EXE. The original `SpacePunks-Win64-Shipping.exe` is never changed, and a different game build is refused.

If the game reports missing runtime components, run `SpacePunks-experiment\Engine\Extras\Redist\en-us\UE4PrereqSetup_x64.exe`.

---

## What works

Tested on one Windows PC with an existing save profile:

| Feature | Status |
| --- | --- |
| Start straight into the local hub (no Epic login, no backend) | ✅ works |
| Hero selection and buying a hero | ✅ works |
| Your own nickname in game (Rename in the launcher) | ✅ works |
| Starting missions locally | ✅ works |
| Get-a-Job step 225 – spawns enemies offline (it had an empty enemy list) | ✅ works, finished and returned to hub |
| Mission end / **Abort Mission** returns to the hub instead of the dead online menu | ✅ works |
| **Esc → Main Menu** reloads the local hub instead of the broken online menu | ✅ works |
| Fatal crash after **P → Create Party** fixed – the game shows "Unable to start party" and keeps running | ✅ works |
| 0–3 friendly AI bots in missions (Bots tab; they do not appear in the hub) | ✅ works |
| Characters / Resources overview read from your save | ✅ works |
| Time played counter | ✅ works |
| Premium store shows items and prices | ✅ works |
| Premium tab: add +500 / +1 000 / +5 000 premium currency (save is backed up first) | ✅ works |
| Buying premium items (tested: two avatar backgrounds, currency deducted, items saved) | ✅ works |
| Automatic game preparation from a clean install (V6) | ✅ tested: creates the exact expected file |

### Not working / not verified

- **Online services are not restored**: no Epic party, friends, matchmaking or online co-op.
- **LAN co-op is not part of this release.** It is in progress as a separate experiment, but not stable yet.
- Only Get-a-Job step **225** got the enemy fix. Other mission steps may still start without enemies.
- Rejecting a purchase without enough currency, equipping bought cosmetics and seeing them after a restart were not tested.
- Starting with a **brand-new profile** (no `Player0.sav` at all) was not tested.
- Only the exact Open Beta build above is supported.

---

## What the launcher changes in your game folder

- First PLAY: creates `K1\Binaries\Win64\SpacePunks-Win64-HubReturn.exe` from the original EXE.
- Every PLAY: builds `SpacePunks-Win64-Launcher.exe` (your nickname, bots and the fixes) and the small config pack `K1\Content\Paks\SpacePunksLocal_P.pak`.
- First PLAY: replaces the mission data `K1\Saved\PersistentDownloadDir\StaticData\StaticData.k1z` with the fixed version and keeps the original as `StaticData.k1z.before-reborn-v7.bak`.

Your progress is saved in `%LOCALAPPDATA%\K1\Saved\SaveGames\Player0.sav`. The Premium tab and Rename back it up to `Save backups` next to the launcher first. **Close the game before using Rename or Premium.**

SHA-256 of `release/Space Punks Reborn.exe` (V6): `950f77c336df4181ef4706e4f69fcd911bb44ed661c48216c5259ad192e3cc46`

---

## Version history

- **V1** – Bots tab (0–3 AI bots in missions).
- **V2** – version badge, Resources shows only the named currencies, Time played counter.
- **V3** – Abort Mission returns to the hub; premium store shows items and prices.
- **V4** – Premium tab (add premium currency, with save backup); buying premium items allowed.
- **V5** – Esc → Main Menu reloads the local hub; fix for the fatal crash after Create Party.
- **V6** – prepares a clean game install by itself on the first PLAY; no extra tools needed.

Czech guide: [NAVOD-CZ.md](NAVOD-CZ.md) · Technical notes: [docs/TECHNICAL.md](docs/TECHNICAL.md)
