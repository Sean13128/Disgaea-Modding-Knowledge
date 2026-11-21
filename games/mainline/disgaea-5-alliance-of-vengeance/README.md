# Disgaea 5: Alliance of Vengeance

- **Released:** 2015 (PS4) · **Developer/publisher:** Nippon Ichi Software / NIS America
- **Versions covered here:** Disgaea 5 Complete for PC (Steam app 803600): the October 2018 launch build and the January 2019 Steam update (adds Chinese and Korean). Switch and PS4 aren't covered yet
- **Modding status:** mature. Data edits, archive overlays, script edits, executable patches and proxy-DLL runtime hooks are all in use
- **Research on hand:** Deep

## Start here

- **I want to install mods:** use the mod's own installer, or [D5 Mod Kit](https://github.com/eraser4521/D5ModKit). See [tools.md](tools.md).
- **I want to make a mod:** read [modifiable.md](modifiable.md) for the rules that keep saves safe, then use D5 Mod Kit.
- **I want to understand the game:** [INDEX.md](INDEX.md) → [data.md](data.md) → [functions.md](functions.md).

## Five things to know first

1. **No loose files.** Everything is inside archives; change files by rebuilding or adding archive entries.
2. **Append, never insert.** Saves remember `char.dat` row positions. New rows need IDs above every existing ID.
3. **Evilities are code.** A table row names an evility; its behavior is in the executable.
4. **47 classes are hard-coded** in the class menus. More need executable patches.
5. **Two builds.** Addresses differ between the 2018 launch build and the 2019 Steam update.

## Pages

| Page | What's there |
|---|---|
| [INDEX.md](INDEX.md) | Files, archives, saves, config |
| [data.md](data.md) | All 90 tables; field layouts for characters, classes, skills, evilities, items |
| [data-reference.md](data-reference.md) | Every named field of all 90 tables |
| [functions.md](functions.md) | Function addresses by system; the unit record |
| [scripts.md](scripts.md) | Event IDs, script commands, skill animations, effects |
| [modifiable.md](modifiable.md) | Methods, limits, crash causes, mod conflicts |
| [character-bank.md](character-bank.md) | Moving units between saves: what travels, the rules, what was seen in game |
| [id-registry.md](id-registry.md) | ID ranges vanilla, D5 Mod Kit and Overlord use |
| [evility-consumers.md](evility-consumers.md) | For each evility ID, the functions that test it (where to patch) |
| [learned-evility-sites.md](learned-evility-sites.md) | Every code site that reads or writes the learned-evility bits |
| [generic-range-sites.md](generic-range-sites.md) | Every place the game checks the generic class-ID ranges |
| [assets.md](assets.md) | Asset formats; which other games' assets fit |
| [tools.md](tools.md) | Tools by build |
| [mods/](mods/) | How existing mods work |

## Shares code or formats with

**Format pages:** [NISPACK](../../../formats/nispack.md) · [PS_FS_V1](../../../formats/ps_fs_v1.md) · [FAFULLFS](../../../formats/fafullfs.md) · [DSARC](../../../formats/dsarc.md) · [YKCMP_V1](../../../formats/ykcmp.md) · [NIS LZS](../../../formats/nis-lzs.md) · [anm](../../../formats/anm.md) · [textures](../../../formats/textures.md) · [OBF](../../../formats/obf.md) · [engine families](../../../engines/README.md)

- **Disgaea 4, 6, 7:** much shared engine code (function matching). D4 sprites and their ten palettes import into D5.
- **Disgaea RPG:** its generic sprites are the same drawings as D5's.
- **Disgaea 1/2 PC:** the `YKCMP_V1` compression also appears in their saves.
- **Disgaea DS:** the `DSARC FL` archive format, used here for sound effects.

## Open questions

- Turn order, command menu and Move/Jump code.
- Where the stat cap is enforced.
- Switch and PS4 differences.

## Sources

See each page. Most knowledge here comes from D5 Mod Kit by eraser4521.
