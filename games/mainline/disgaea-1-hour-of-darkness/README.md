# Disgaea: Hour of Darkness

- **Released:** 2003 (PS2) · **Developer/publisher:** Nippon Ichi Software / NIS America (Atlus in 2003)
- **Versions:** PS2; PSP *Afternoon of Darkness*; Nintendo DS; **Disgaea PC** (2016); **Disgaea 1 Complete** (2018, Switch/PS4, HD remake). Covered here: Disgaea PC (Steam app 405900) and Disgaea 1 Complete (Switch, PS4)
- **Modding status:** Disgaea PC: established (pg_disatools, since 2016). Disgaea 1 Complete: readable, no public mods
- **Research on hand:** Medium

## Start here

- **Disgaea PC modder:** get [pg_disatools](https://github.com/ProgSys/pg_disatools), read [modifiable.md](modifiable.md), then try the [first-mod guide](../../../guides/first-mod-disgaea-pc.md).
- **Disgaea 1 Complete:** its sprites are Disgaea 5's format: see [assets.md](assets.md).
- **Understand the files:** [INDEX.md](INDEX.md) → [data.md](data.md).

## Pages

| Page | What's there |
|---|---|
| [INDEX.md](INDEX.md) | Files for PC, Complete and DS; save format |
| [data.md](data.md) | `magic.dat` (both editions), both character tables, dungeon / hospital / names, music |
| [scripts.md](scripts.md) | TALK and SCRIPT: dialogue, menus, cutscenes, game-state flags |
| [scripts-reference.md](scripts-reference.md) | Every SCRIPT and TALK command with its argument layout |
| [functions.md](functions.md) | Disgaea PC addresses, stat and EXP formulas, unit record, how pg_disatools hooks in |
| [modifiable.md](modifiable.md) | What pg_disatools can change, its mod framework; limits |
| [assets.md](assets.md) | Formats; D1 Complete → D5 |
| [tools.md](tools.md) | pg_disatools and others |
| [mods/](mods/) | Known mods |

## Shares code or formats with

**Format pages:** [Disgaea PC containers](../../../formats/disgaea-pc-containers.md) · [PS_FS_V1](../../../formats/ps_fs_v1.md) · [DSARC](../../../formats/dsarc.md) · [YKCMP_V1](../../../formats/ykcmp.md) · [anm](../../../formats/anm.md) · [textures](../../../formats/textures.md) · [engine families](../../../engines/README.md)

- **Disgaea 5:** Disgaea 1 Complete's sprites (anm format, monster action IDs) and its `PS_FS_V1` archive.
- **Disgaea 2 PC:** the same save encoding (XOR + `YKCMP_V1`) and Pretty Prinny.
- **Phantom Brave:** TX2 textures.

## Open questions
- Item table layouts; most SCRIPT commands.
- Whether Disgaea 1 Complete reads modified archives on emulators.

## Sources
See each page.
