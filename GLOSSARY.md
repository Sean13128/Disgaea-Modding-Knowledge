# Glossary

Game terms and modding terms used across this wiki.

## Game terms

| Term | Meaning |
|---|---|
| **Evility** | A passive ability. Units have unique (innate) evilities and can equip learned ones in limited slots. Introduced in Disgaea 3 |
| **Magichange** | A monster turns into a weapon for a humanoid ally. Introduced in Disgaea 3 |
| **Overload** | A character's unique special ability in Disgaea 5, usable in Revenge Mode |
| **Revenge Mode** | Disgaea 5: a unit's revenge gauge fills when allies are hurt; at full, Overloads open up |
| **Generic class / rank** | A recruitable class (Warrior, Healer …). Each class has ranks (tiers, six in D5) with their own rows, names and colours |
| **Unique character** | A named character (story cast, DLC guests) |
| **Class mastery** | Disgaea 5: progress in a class that unlocks its ranks and gives stat bonuses |
| **Sub-class** | Disgaea 5: a second class whose skills and mastery a unit also earns |
| **Reincarnation** | Rebirth into a new class or rank at level 1, keeping some stats |
| **Recruiter / Hire Character** | Where generic units are created |
| **Personality** | A generic unit's voice variant (three per class in D5) |
| **Innocent** | A resident inside an item that boosts it (stored in `HABIT.dat` in D5) |
| **Item World** | Random dungeons inside an item |
| **Geo panels / Geo symbols** | Coloured map panels and the symbols that give them effects |
| **Netherworld** | A demon world; D5's netherworlds also carry battle effects ("Netherworld skills") |
| **Dark Assembly** | The senate where you pass bills (wishes) |
| **Team attack** | Adjacent allies join an attack; `comb.dat` holds the rates in D5 |
| **Mana** | Currency spent on evilities, bills and skills |
| **HL** | Hell, the money currency |
| **Prinny** | The series' penguin-like mascot demons |

## Modding terms

| Term | Meaning |
|---|---|
| **Table** | A data file of fixed-size rows (`char.dat`, `magic.dat` …) or JSON records (D6/D7) |
| **Row index vs ID** | A row's position in the file vs the ID stored in it. D5 saves cache the index, so inserting rows breaks saves |
| **Archive** | A container of game files (NISPACK, NSAC, FAFULLFS …: [formats](formats/README.md)) |
| **Overlay** | Adding or replacing archive entries instead of rewriting the whole archive |
| **Obfuscation** | A simple byte transform over archives (D5: swap nibbles of `b XOR 0xF0`), not encryption |
| **Executable patch** | Bytes changed in the game's `.exe`, or code added in new sections |
| **Proxy DLL** | A DLL named like a system one (`dinput8.dll`, `XINPUT9_1_0.dll`) that the game loads, letting a mod hook functions in memory without changing files |
| **Hook / hook site** | The place in the game's code where a mod redirects execution to its own code |
| **Build** | One release of an executable. Addresses only apply to the build they were found in |
| **Virtual address (VA)** | An address as the executable is loaded (`0x140…` for 64-bit PC games) |
| **Decomp / decompile** | Machine-generated C from the executable (Ghidra). A lead, not proof; never published here |
| **BSim** | Ghidra's function-similarity tool, used to match functions between games |
| **anm** | NIS's 2D sprite animation format ([anm](formats/anm.md)) |
| **Choreography** | The script events that animate a skill (D5: `15xxxxxx` events) |
| **Offset-list archive (OLA, COLA, SOLA)** | Disgaea PC's unnamed archives: a count and offsets (OLA); the same split into IMY-compressed chunks (COLA); or with a sprite ID per file (SOLA) ([formats](formats/disgaea-pc-containers.md)) |
| **IMY** | Disgaea PC's chunk compression |
| **SH** | A Disgaea PC sprite sheet: animations, layers, keyframes, cutouts, colour tables, indexed images |
| **MPD / MPP / GEO / KM3** | Disgaea PC map layout / map textures and models / 3D model scene graph / effect model archive |
| **TALK and SCRIPT** | Disgaea PC's two script languages: dialogue and menus, and camera, units and sound ([scripts](games/mainline/disgaea-1-hour-of-darkness/scripts.md)) |
| **Game-state bits** | A global bit array the scripts set, clear and test to track story progress (Disgaea PC) |
| **Special K** | Kaldaien's injection framework; Pretty Prinny is a plug-in for it |
| **Merge patch / JSON Patch** | Makai Kit's partial table edits for D6/D7 (`.merge.json`, `.patch.json`), applied in mod load order |
| **Evidence labels** | Verified in game / Verified offline / From decomp / Community-reported / Unknown ([rules](CONTRIBUTING.md#evidence-labels)) |
