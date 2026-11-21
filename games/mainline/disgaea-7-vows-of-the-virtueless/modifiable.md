# Disgaea 7 — What Can Be Modified

PC version. D7 is the easiest Disgaea to mod with data alone, thanks to Makai Kit's loader and D7's data-driven
effect system.

**Evidence:** **Community-reported** from Makai Kit's README and source, unless noted.

## The ground rules

- **Use Makai Kit's loader.** It loads through a `dinput8.dll` proxy and hooks the game's file manager
  (`Nmpl::File::CFileMgr::openCmn`), so no game file is changed on disk. Uninstall by removing `dinput8.dll` and
  `mkplugins/`. Repacked tables go to `mods/_generated/data/database/<table>.dat`.
- **Which tables:** the public GitHub source patches 37 tables ([data.md](data.md)).
- **Mods are folders** in `<game>/mods/`. The loader reads them in directory order (alphabetical on Windows' NTFS) and
  then **reverses** the list. Folder names starting with `_` are reserved (`_generated/` holds the rebuilt tables).
  Which mod wins depends on what kind of change it is: see [Mod conflicts](#mod-conflicts).
- **Files:** anything in a mod's `files/` replaces the game file of the same path.
- **Tables:** in a mod's `databases/<table>/`:
  - `<ID>_<ENUM>.json`: a whole record (new or replacing);
  - `*.merge.json`: a JSON Merge Patch (RFC 7386): only the fields you change. Lists are replaced whole;
  - `*.patch.json`: a JSON Patch (RFC 6902).
- **Records are validated** against Makai Kit's structs. A record with a missing field, or `0.0` in an integer field,
  is rejected.
- **New IDs** must not collide with vanilla or another mod's.

## Methods

| Method | How | Tools |
|---|---|---|
| Table edit | JSON records and patches in `databases/` | Makai Kit |
| File replacement | Files in `files/` | Makai Kit |
| Scripts | Lua skill and event scripts as files under `files/data/script/` | Makai Kit (D7 has no `script.dat` patching; that's D6-only) |
| Runtime plugin | A DLL in `mkplugins/` | Makai Kit's plugin loader |
| Memory | Cheat Engine tables | [GamePressure listing](https://www.gamepressure.com/download/disgaea-7-vows-of-the-virtueless-cheat-table-v6102023-mod/zf14a81) |

## By area

| Area | Can change | How | Limits |
|---|---|---|---|
| **Stats, classes, jobs** | Everything in `character`, `characlass`, `job` | Tables | |
| **Evilities** | Names, costs and effect blocks (`effect[]`) | Tables | Effect, value and condition codes aren't documented here yet |
| **Skills** | Numbers, areas (`actmap`), effects, and animation/logic through Lua | Tables + Lua files | |
| **Items, innocents, shops** | Item tables | Tables | |
| **Stages and enemies** | Stage records (`stage`) | Tables | Enemy placement tables are outside the public loader |
| **New characters from other games** | Models, animations, textures, voices, skills | Files + tables | **Unknown**. Overlord Edition's author has said D6 ports are a goal but may be blocked by file formats ([D7 beta post](https://reddit.com/r/Disgaea/comments/1q2dj5n/disgaea_7_overlord_edition_mod_beta/)) |
| **Voices** | New `.nlsd` files + `voice` rows | Files + tables | |

## Mod conflicts

From the public loader's source (`modloader-d7/src/win.rs`; **Verified offline**, source read, not tested in game):

- **Two mods replacing the same file:** the loader takes the first match in its reversed list, so the
  **alphabetically last** mod wins.
- **Two mods changing the same table record:** patches are applied one mod after another in that same reversed order,
  and each one overwrites what came before, so the **alphabetically first** mod is applied last and wins (field by
  field for merge and JSON patches; a whole-record `.json` replaces everything earlier mods did to that record).
- So the two rules point in opposite directions. To make a compatibility mod win both ways, put its files in a folder
  that sorts last and its table patches in a folder that sorts first, or avoid overlapping at all.

## Open questions

- Save format, and what happens when a save with mod content loads without the mod.

## Sources

- [Makai Kit](https://github.com/HybridEidolon/makaikit) by HybridEidolon.
- [D7 beta post](https://reddit.com/r/Disgaea/comments/1q2dj5n/disgaea_7_overlord_edition_mod_beta/) by Cryswar (Aryuto on Reddit).
