# Space Punks Reborn – návod česky

Neoficiální fanouškovský **offline launcher** pro Space Punks Open Beta. Oficiální servery už neexistují; launcher umožní spustit hru bez Epic přihlášení a bez serveru, načíst hub, vybrat hrdinu a hrát mise na jednom PC.

> Projekt nemá nic společného s Flying Wild Hog ani Jagexem. Repo **neobsahuje žádné herní soubory** – potřebuješ vlastní kopii klienta Space Punks Open Beta (UE 4.27, build REL_UPDATE_9 / 1 221 514).

Aktuální verze: **V6**.

## Instalace

Potřebuješ jen Windows 10/11 (.NET Framework 4.x je jeho součástí) a vlastní kopii hry.

1. **Zkopíruj hru** do zapisovatelné složky, např. `C:\Hry\Space Punks Reborn\SpacePunks-experiment`. Uvnitř musí být `K1\Binaries\Win64\SpacePunks-Win64-Shipping.exe`.
2. **Stáhni launcher**: otevři složku [`release`](release), klikni na `Space Punks Reborn.exe` a pak na *Download raw file*.
3. Dej `Space Punks Reborn.exe` do složky **nad** hrou, např. `C:\Hry\Space Punks Reborn\Space Punks Reborn.exe`. (Když se složka hry jmenuje jinak nebo je jinde, launcher se na ni zeptá.)
4. Spusť `Space Punks Reborn.exe`, případně nastav přezdívku (**Rename**) a boty (**Bots**) a klikni na **PLAY**.

Při prvním PLAY si launcher hru sám připraví (pár sekund): vedle původního EXE vytvoří `SpacePunks-Win64-HubReturn.exe`. Původní `SpacePunks-Win64-Shipping.exe` se nikdy nemění a jiný build hry launcher odmítne.

Pokud hra hlásí chybějící komponenty, spusť `SpacePunks-experiment\Engine\Extras\Redist\en-us\UE4PrereqSetup_x64.exe`.

## Co funguje

- Start rovnou do lokálního hubu, bez Epicu a bez serveru.
- Výběr a nákup hrdiny, vlastní přezdívka ve hře (Rename).
- Spouštění misí; Get-a-Job krok 225 má opravené nepřátele (dřív byl prázdný).
- Konec mise i **Abort Mission** vrací do hubu, ne do nefunkčního online menu.
- **Esc → Main Menu** znovu načte lokální hub.
- Opravený pád po **P → Create Party** – hra jen napíše „Unable to start party“ a běží dál.
- 0–3 AI boti v misích (záložka Bots, v hubu se neukazují).
- Přehled postav a surovin ze savu, počítadlo odehraného času.
- Prémiový obchod ukazuje položky a ceny; záložka Premium přidá +500 / +1 000 / +5 000 prémiové měny (před změnou zálohuje save); nákup prémiových položek funguje.
- Automatická příprava čisté instalace hry (V6).

## Co nefunguje / není ověřené

- Online služby (party přes Epic, přátelé, matchmaking, online co-op) obnovené nejsou.
- **LAN co-op v této verzi není** – vyvíjí se zvlášť a zatím není stabilní.
- Oprava nepřátel je jen pro krok 225; jiné zakázky můžou být bez nepřátel.
- Neověřeno: odmítnutí nákupu při nedostatku měny, nasazení koupené kosmetiky a její zobrazení po restartu, start s úplně novým profilem bez `Player0.sav`.
- Podporovaný je jen přesně tento build hry.

## Kde je postup a zálohy

- Save: `%LOCALAPPDATA%\K1\Saved\SaveGames\Player0.sav`.
- Rename a Premium před změnou uloží zálohu do složky `Save backups` vedle launcheru.
- Před **Rename** a **Premium** vždycky zavři hru.
- Launcher ve složce hry vytváří `SpacePunks-Win64-HubReturn.exe`, `SpacePunks-Win64-Launcher.exe` a `SpacePunksLocal_P.pak` a poprvé nahradí misní data `StaticData.k1z` (originál schová jako `StaticData.k1z.before-reborn-v7.bak`).

## Verze

V1 boti · V2 přehled surovin a odehraný čas · V3 návrat z mise do hubu, prémiový obchod · V4 záložka Premium a nákupy · V5 Esc → Main Menu do hubu, oprava pádu po Create Party · V6 automatická příprava hry.
