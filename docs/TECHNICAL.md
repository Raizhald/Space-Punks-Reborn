# Technical notes (V6)

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
| `BotPatch` | 0–3 friendly AI sidekicks in missions |
| `SpawnDataPatch` | `StaticData.k1z`: Get-a-Job step 225 had an empty spawn list (Type 0, `NoSquadFound`). Step 194 uses the same map 9, mission preset 157, planet 1, faction 1 and base power 13; its 60 spawn mappings are copied for 225 with new IDs (table at decompressed offset 4123653, 28-byte records, 4692 → 4752). Original backed up as `.before-reborn-v7.bak` |

Config pack: `SpacePunksLocal_P.pak` (PAK v3) holds a modified `K1/Config/DefaultEngine.ini` (`GameDefaultMap=/Game/Maps/HUB/Hub.Hub`, `RequireEpicGamesLauncher=False`, `[URL] Name=<nickname>`), generated from `res_EngineTemplate`.

## Save file

`Player0.sav` is read with `res_SaveSchema` (field names, offsets and sizes of the save structs, read from the running client's reflection data). The Premium tab changes only the `ResourceId 0` row of `PlayerResourceData` (adds it if missing), re-reads the result and compares every other field, cap 999 999 999, backup first, atomic write.

## Not solved

Online identity: the game has no Epic Online Services user ID offline. V5 only guards the Create Party path; other online calls (party, presence, friends) can still expect an ID. The separate LAN experiment adds more of these guards but is not part of this release.
