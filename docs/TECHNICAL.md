# Technical notes (V11)

Client: Space Punks Open Beta, Unreal Engine 4.27.2, `REL_UPDATE_9_Shipping_1221514`. All addresses below are RVAs in that build. Every patch checks the exact original bytes / file hash first and refuses anything unknown.

## Base EXE: `SpacePunks-Win64-HubReturn.exe`

Since V6 the launcher creates it on the first PLAY from the original `SpacePunks-Win64-Shipping.exe` (SHA-256 `0c9bfa0b…40e93`) with an embedded 703-byte delta (400 new bytes, the rest are copy instructions from the original file). Result SHA-256 `f12d96b1…d5`. It contains three earlier steps:

1. **No loading overlay** – at 0x14712c6 the call that re-creates the in-game loading screen after the old one was removed is replaced by a jump to the existing epilogue (0x1471643).
2. **Hero selection** – new section `.splocal`; the native controller vtable's ProcessEvent slot is redirected to a 127-byte handler that, once the controller is ready (`ControllerIsReady` +0x818, PlayerState +0x228), calls `ClientForcePlayerCharacterSelection_Implementation` a single time and then always forwards to the original ProcessEvent (0x308f6a0).
3. **Return to hub** – new section `.sphub`; `ClientGoToHub_Implementation` (0x1528130) builds the FName `/Game/Maps/HUB/Hub` and calls `UGameplayStatics::OpenLevel` (0x3340bf0) instead of the online JoinHub/K1dedi flow.

The exception table (`.pdata`) is copied, extended and kept sorted, so new code has valid unwind data.

## Patches applied by the launcher on every PLAY

| Patch | What it does |
| --- | --- |
| `GameNamePatch` | returns the chosen nickname from GetNickname (changing `Name=` in the config alone is not enough) |
| `MissionPatch` (in `Core.cs`) | offline matchmaking whitelist so missions start locally |
| `MissionAbortPatch` | Abort Mission opens the Hub instead of MainMenu, original cleanup kept |
| `PremiumShopCatalogPatch` | premium store uses the existing `LoadAllPurchasables` in both entry paths |
| `PurchaseValidatePatch` | `UPlayerCRUDSystem` vtable (0x5710d80) slot +0x748 `ServerPurchaseItem_Validate` points to the existing "return true" stub 0x85ea20 (already used by ServerPurchaseHero) instead of "always reject" 0x85eb50 |
| `MainMenuHubPatch` | the three remaining `OpenLevel(MainMenu)` continuations (lea at 0x14e0a5c, 0x14e0edc, 0x1528522) use the existing `/Game/Maps/HUB/Hub` string |
| `PartyIdentityPatch` | before the native `FOnlinePartySystemEOS::CreateParty` (crash at 0xe8f428, null EOS user ID) the caller at 0x14fa51e checks the ID; null → original false/cleanup branch ("Unable to start party"), valid ID → original call. 34 bytes in free space of `.sphub` +0x100 |
| `NullCurvePatch` (V7) | a hero stat is a base value times two curves reached through weak references (function at 0x3ca03e0). For a new player previewing a hero, a curve can be unloaded and the game read address 0xa8 and crashed. Jumps at 0x3ca0441/0x3ca049a and two small checks in `.sphub` (+0x1a0, +0x1c0) use the game's existing default value when a curve is missing |
| `SpawnDataPatch` | `StaticData.k1z`: Get-a-Job step 225 had an empty spawn list (Type 0, `NoSquadFound`). Step 194 uses the same map 9, mission preset 157, planet 1, faction 1 and base power 13; its 60 spawn mappings are copied for 225 with new IDs (table at decompressed offset 4123653, 28-byte records, 4692 → 4752). Original backed up as `.before-reborn-v7.bak` |

Config pack: `SpacePunksLocal_P.pak` (PAK v3) holds a modified `K1/Config/DefaultEngine.ini` (`GameDefaultMap=/Game/Maps/HUB/Hub.Hub`, `RequireEpicGamesLauncher=False`, `[URL] Name=<nickname>`), generated from `res_EngineTemplate`.

## Save file

`Player0.sav` is read with `res_SaveSchema` (field names, offsets and sizes of the save structs, read from the running client's reflection data). The Premium tab changes only the `ResourceId 0` row of `PlayerResourceData` (adds it if missing), re-reads the result and compares every other field, cap 999 999 999, backup first, atomic write.

## V9: LAN multiplayer

- The config pack swaps `/Script/OnlineSubsystemRedpointEOS.EOSNetDriver` for `/Script/OnlineSubsystemUtils.IpNetDriver`.
- **Host** (`SpacePunks-Win64-Host.exe`): `LoadMap` always calls `UWorld::Listen` (0x37669de `74 6e` → `90 90`), so the hub and every mission listen on port 7777.
- **Join** (`SpacePunks-Win64-Join.exe`): the party-key path at 0x14fa2a0 jumps to code in `.sphub` that calls `OpenLevel(FName(address))`; the address (max 27 characters) is stored at 0x6d5c208. The local-selection helper (0x699f000) reconnects whenever the client is alone in its world (NetDriver null at `world+0x38`, max 50 attempts), because server travel doesn't move clients in this game.
- Guards for the missing online services (null EOS IDs): peer guard at 0x141f628, presence guard at 0x15064c5, compare guard at 0x14bd838, HUD fix at 0x15c29c0 – all jump to small checks in `.sphub` with their own unwind data.
- Joining 127.0.0.1 on the hosting PC starts the second game with `-UserDir` and a copy of the save (`Multiplayer test profile`).

## V10: AI bots (experimental)

V1–V7 spawned the game's `Char_Sidekick_C` through the developer cheat manager (`BP_CheatManager_C`), but the bots never shot. V8 removed the option. V10 brings it back with three fixes; the bot build is the host build plus these patches:

1. **Firing** – `Task_Fire` only sets `IsFiring` (+0x950) on the sidekick; nothing calls its weapon. The sidekick vtable's ProcessEvent slot (0x4c78668 + 0x220) points to a helper in a new section `.spfire`: while `IsFiring` is set and the weapon (+0x880) has the native weapon vtable (0x4b48460), it calls the native Fire (vtable +0x6b8, 0x1644740) at most every 250 ms per bot (time from `KUSER_SHARED_DATA`), then forwards to ProcessEvent.
2. **Obstacle test** – Fire skips the shot when the barrel is blocked. The test (0x1654010) reports "blocked" if `ObstacleTestLocationNative` / `ObstacleTestLocationEndNative` (+0x3f8/+0x400) are missing – and sidekick weapons don't have them. 0x16541fa `mov al,1` → `mov al,0` treats missing components as "not blocked" (player weapons always have them).
3. **Damage** – projectile damage comes from the owning player's stats, so sidekick projectiles have `Damage` (+0x29c) = 0. At the start of the two native projectile damage functions (0x1484e40, 0x1485210) a check in `.spfire` sets a fixed damage (0.2) for projectiles whose instigator is a sidekick, team is the player team and damage is still 0. The game then scales it like an AI attack.

Bots have no shooting animation (`AnimBP_Hostage_C`).

## V11: unlocked hub characters and The Crack

`StaticData.k1z` (UE compressed blocks of 128 KiB, zlib) holds the game data; task and interaction requirements are JSON strings inside it. All edits keep the exact length, so only 3 of the 50 compressed blocks change (resource `StaticDataV11`, SHA-256 `c776980a…7adc75c37`; the V7 Get-a-Job spawn fix is included):

- 15 task requirements of `TaskNpcRobot` (KI-42), `TaskNpcComputer` (The Choir) and `TaskNpcSoldier` (Vault Support tasks), plus the Vault Support unlock interaction: `AlwaysBlockedRequirement` → `FameRequirement` with default 0. Other requirements (fame, previous tasks) are kept.
- Carrasco's *Take a Deep Dive* required `DA_Task_Endless_Unlock` to be finished, a task that nothing ever starts; that requirement is replaced by `FameRequirement` 0. The Crack keeps its own planet requirement (hero level 16). Entry costs one Phaser (resource 15).
- **Task window fix** (`TaskWindowPatch`): opening KI-42's or The Choir's window read a task asset list that this build no longer has (null) and crashed at 0x3d275ea. 0x3d275df `33 ed 8b dd` → `eb 36 90 90` and 0x3d275e3 → a 27-byte null check in int3 padding at 0x5a2350; both missing cases use the game's existing default (0x3d27617).
- KI-42's and The Choir's missions are on *Xena*, a game-mode location that isn't on the star map in this build.
