# Disgaea: Hour of Darkness — What Can Be Modified

**Disgaea PC** has a long-running modding scene built on pg_disatools. **Disgaea 1 Complete** has readers but no public
mods beyond save editing.

**Evidence:** **Community-reported** unless noted.

## Disgaea PC

| Area | Can change | How | Limits |
|---|---|---|---|
| Archive contents | Extract and insert files in `.DAT` / `.MPP` | pg_disatools File Manager | Most file positions are hard-coded: **don't delete entries**. Grown files can need more space; the File Manager can expand it |
| Data tables | Dungeons, hospital, characters, music shop, skills, innocents (`HABIT`), help text | pg_disatools Data Editor | Formats defined in editable files |
| Sprites | View, edit, import and export sprite sheets (PNG/TGA) | pg_disatools Sprite Sheet Editor | Oversized data can stop the game loading |
| Textures | TX2 ↔ TGA/PNM | pg_disatools TX2 Editor, `TX2Converter` | Back up first |
| Maps | Edit maps, make new ones; adds a "Campaigns" menu | pg_disatools Map Editor | Beta |
| Rendering, UI | Custom models and UI, native resolution | pg_disatools DisaReverseEngineering; Pretty Prinny | |
| Dialogue | `TALK.DAT` entries ([scripts](scripts.md)) | Netherworld Content Pack; pg_disatools Map Editor (`.tscript`) | No call stack; picture loads are asynchronous |
| Data size | — | Disa PC File Manager can enlarge the game's load buffer | The game loads data into **one fixed buffer**; going over it crashes (**over 180 MB of `SUBDATA.DAT` crashed at launch** in one report). A patched `dis1_st.exe` by Maide also lifted the limit. See [functions](functions.md#fixed-memory) |
| Saves | Decode, edit, re-pack | HenryEx's QuickBMS script; Xkeeper0's disgaea-pc-tools | XOR + `YKCMP_V1` ([layout](../../../formats/saves.md#disgaea-pc-layout)); no checksum reported. The save is a memory copy, so unit records match [functions](functions.md#unit-record-and-saves) |

### pg_disatools' mod framework (no file editing)

Since v1.0 (Nov 2024: campaigns; Apr 2025: custom characters, recruitable in the Dark Assembly) pg_disatools also
works as a **runtime mod loader**. DisaModManager patches `dis1_st.exe` so it loads
the tools' module ([how](functions.md#how-pg_disatools-gets-into-the-game)); mods are `.zip` files imported in
DisaModManager and placed under `mod/`. The game's archives aren't rewritten.

| Mod type | Folder | What it can do |
|---|---|---|
| Custom characters | `mod/CustomCharacters` | New characters, sprites, faces (SD and HD), skills; or override existing ones (an HD sprite mod is just overrides of `disa::<sprite id>`) |
| Translations | `mod/CustomTranslation` | Replace any on-screen string by hooking text drawing; CSV files; custom fonts; `F2` terminal commands `tb` (base file) / `tr` (record strings); `F3` toggles |
| Campaigns | `mod/CustomCampaigns` | Map packs with their own story, made in the Map Editor, chosen from an added *Campaigns* menu |
| Engine mods | — | Better Controls (radial menu, path preview, commands while units move), Better Font |

**IDs without collisions:** mods name content with string IDs, `namespace::name` (the namespace defaults to the zip's
name; the game's own content is `disa::<number>`), and each mod has a **UUID** that saves and the load order use. When custom characters are in the party, the save gets
extra metadata so they can be remapped on load. A
mod meant to change another mod's content uses that mod's namespace and loads after it.

**Known conflict:** with custom characters installed, Pretty Prinny crashes the game (pg_disatools wiki).

## Disgaea 1 Complete

| Area | Read | Written back |
|---|---|---|
| Sprites (`start.dat` member 98) | Yes: it's a Disgaea 5 anm pack (**Verified offline (Kit)**) | **Unknown** |
| Character table, `magic.dat` | Yes (**Verified offline (Kit)**) | **Unknown** |
| Saves (Switch) | EdiZon config | Yes, through EdiZon |

Public prior work on Disgaea 1 Complete's files is thin: a [ZenHAX request](https://www.zenhax.com/viewtopic.php@t=11554.html)
to open `start.dat` (Switch) got no working answer, and pg_disatools and the usual QuickBMS scripts don't read it. The
`start.dat` layout on [INDEX.md](INDEX.md) is, as far as this project found, the first public description.

Switch and PS4 need a modded console or an emulator to change files at all. On a modded Switch, Atmosphère's LayeredFS
serves replacement files from `atmosphere/contents/01004B100AF18000/romfs/` (general Switch method; **Unknown** whether
anyone has used it for Disgaea 1 Complete) ([guide](https://switch.hacks.guide/extras/game_modding)).

## Sources
[pg_disatools](https://github.com/ProgSys/pg_disatools) and its [wiki](https://github.com/ProgSys/pg_disatools/wiki) (How to install a Mod, Adding Custom Characters, Adding Custom Translation, Map Editor, memory allocation; read 2026-11-04), [Liminori/Test](https://github.com/Liminori/Test) (pg_disatools tool notes), [HD sprite mod thread](https://steamcommunity.com/app/405900/discussions/4/1742260837206441731), [ZenHAX save thread](https://www.zenhax.com/viewtopic.php@t=4698.html), [Disgaea PC mod index thread](https://steamcommunity.com/app/405900/discussions/4/371918937281416805/?ctp=27); D5 Mod Kit development fork D1 readers.
