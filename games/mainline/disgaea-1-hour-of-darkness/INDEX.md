# Disgaea: Hour of Darkness — File Index

Disgaea 1 has many releases: PS2 (2003), PSP *Afternoon of Darkness*, Nintendo DS, **Disgaea PC** (2016, based on the
PSP version) and **Disgaea 1 Complete** (2018, Switch/PS4, remade with HD sprites). They're different builds with
different file layouts.

- **Versions covered:** Disgaea PC (Steam app 405900, build 1375097); Disgaea 1 Complete for Switch (v0, 01004B100AF18000) and PS4 (EU v1.00, CUSA13171)
- **Install / root path:** PC `steamapps/common/Disgaea PC/`. Switch romfs `data/`. PS4 package `data/`
- **Folder trees with sizes:** [layout-pc-steam.txt](layout-pc-steam.txt), [layout-d1-complete-switch.txt](layout-d1-complete-switch.txt), [layout-d1-complete-ps4.txt](layout-d1-complete-ps4.txt)

## Disgaea PC

| Path | Type / format | Contents | Notes |
|---|---|---|---|
| `dis1_st.exe` (2 MB) | Win32 PE | Executable | May be wrapped by Steam DRM; unwrap before analysis |
| `DATA.DAT` (2 GB) | Archive | Main data: maps, sprites, story pictures (`BU`/`NEW_BU`, TX2) | Most file positions are hard-coded: deleting entries can break it (pg_disatools) |
| `SUBDATA.DAT` (49 MB) | Archive | Contains `START.DAT` | **Crashed the game at launch past 180 MB**; a community-patched `dis1_st.exe` lifts this (**Community-reported**) |
| `START.DAT` (in `SUBDATA.DAT`) | IMY package: chunk count, 131,072-byte chunk size, chunk offsets, then type-1 IMY chunks; decoded, an offset-list archive | Data tables (41 `magic.dat`, 44 `char_e.dat`), 61 face icons, 71 `ANM00.dat` (sprites, SOLA container) | `SUBDATA.DAT` itself is PSPFS |
| `SAVEDATA/` | | Saves (Steam Cloud) | See below |

### `START.DAT` members (Disgaea PC)

98 members, found by position (names from pg_disatools' name table, `resources/script/disa/nameTables/START.DAT.csv`;
**Community-reported**):

| Members | Contents | Files |
|---|---|---|
| 0–3 | Scripts and random maps | 0 `script.dat`, 1 `talk.dat`, 2 `RMAPS.dat`, 3 `RMAPS2.dat` |
| 4–11 | Fonts (JP/EN, Chinese `_c`, Korean `_k`) | `new_font.ffm`, `new_font.tx2`, `font.ffm`, `font.tx2`, `new_font_c.ffm`, `new_font_c.tx2`, `new_font_k.ffm`, `new_font_k.tx2` |
| 12–33 | UI textures | `sys02.tx2`, `sys03.tx2`, `sys04.tx2`, `sys05.tx2`, `sys06.tx2`, `wbg03.tx2`, `sys07.tx2`, `number.tx2`, `waku01.tx2`, `wbg01.tx2`, `wbg02.tx2`, `waku02.tx2`, `waku03.tx2`, `item.tx2`, `name.tx2`, `wbg04.tx2`, `wbg05.tx2`, `wbg06.tx2`, `wbg08.tx2`, `wbg09.tx2`, `back.tx2`, `eef.tx2` |
| 34–40 | Models: effects (`effect.km3`), base, gate, cannons, cursor | 34 `effect.km3`, 35 `base.km2`, 36 `mgate.km2`, 37 `taiho01.km2`, 38 `taiho02.km2`, 39 `cursor.km2`, 40 `enn.km2` |
| 41–60 | Data tables | 41 `magic.dat`, 42 `HABIT.dat`, 43 `char_j.dat`, 44 `char_e.dat`, 45 `char_c.dat`, 46 `char_k.dat`, 47 `charhelp.dat`, 48 `mitem.dat`, 49 `dungeon.dat`, 50 `GE.dat`, 51 `name_j.dat`, 52 `name_e.dat`, 53 `name_c.dat`, 54 `name_k.dat`, 55 `WISH.dat`, 56 `HOSPITAL.dat`, 57 `THIEF.dat`, 58 `zukan.dat`, 59 `musicshop.dat`, 60 `geocube.dat` |
| 61 | SD face icons | 61 `face.dat` |
| 62 | A map | 62 `mp25001.dat` |
| 63–70 | Sound-effect and voice banks (`.phd` headers, `.pbd` data) | 63 `se01.phd`, 64 `se01.pbd`, 65 `se02.phd`, 66 `se02.pbd`, 67 `voice01.phd`, 68 `voice01.pbd`, 69 `voice02.phd`, 70 `voice02.pbd` |
| 71 | Sprite archive (SOLA) | 71 `ANM00.dat` |
| 72 | Keyboard prompts | `keyboard.tx2` |
| 73 | HD face icons | 73 `new_face.dat` |
| 74–95 | HD UI textures (`new_…`) | `new_gui01.tx2`, `new_keyboard.tx2`, `new_gui02.tx2`, `new_sys06.tx2`, `new_waku3.tx2`, `new_gui03.tx2`, `new_gui04.tx2`, `new_item.tx2`, `new_gui05.tx2`, `new_gui06.tx2`, `new_gui07.tx2`, `new_gui08.tx2`, `new_gui09.tx2`, `new_gui10.tx2`, `new_gui11.tx2`, `new_gui12.tx2`, `new_sys01.tx2`, `new_number.tx2`, `new_sys05.tx2`, `new_gui13.tx2`, `new_name.tx2`, `new_number02.tx2` |
| 96 | The map, HD | 96 `new_mp25001.dat` |
| 97 | Menu strings | 97 `menu_list.dat` |

### Sprite IDs in `ANM00.DAT`

61 sprites, by ID (pg_disatools' `ANM00.DAT.csv`): **1–5** the story leads (Laharl, Etna, Flonne, Gordon, Jennifer);
**1010–1170** generic humans (Brawler, Warrior, Majin, Ninja, Ronin, Knight, Archer, Skull, Mage, Cleric, Scout,
Recruit, Angel, Rogue; male and female rows where they differ); **2000–2210** monsters (Hobbit to Prinny 2210);
**2500–2610** story bosses (Vyers, Vulcanus, Lamington, Kurtis, Don Joaquin, Prism Ranger); **2916–2917** Prinny maid,
Dimension Phone; **3000–3040** guest characters (Priere, Marjoly, Adell, Rozalin, Zetta); **3500** Pleinair.
IDs 6 and 3190 are named `MOD-KRISAN_PLEINAIR` and `MOD-KRISAN_ASAGI` in the table: sprites that mods add (probably the
Netherworld Content Pack's; its Asagi sprites are what the combined HD pack had to import to stop a crash). Weapon sprites like `9203` are in other
`ANM` archives.

pg_disatools also reads `.MPP` files and edits these data tables: `DUNGEON.DAT`, `HOSPITAL.DAT`, `CHAR_C.DAT`,
`CHAR_K.DAT`, `MUSICSHOP.DAT`, `MAGIC.DAT`, `HABIT.DAT`, `CHARHELP.DAT`. Dialogue is in `TALK.DAT`.

## Disgaea 1 Complete (Switch, PS4)

| Path | Type / format | Contents | Notes |
|---|---|---|---|
| `data/DATA.DAT` (1 GB) | Archive | Main data; story pictures in `new_bu` (NLTX) | Same layout on Switch and PS4 |
| `data/SUBDATA.DAT` (925 MB), `SUBDATA_FR.DAT` | `PS_FS_V1` archive | 229 entries, including `start.dat` (916 MB) | `PS_FS_V1` is also D5's music archive format |
| `start.dat` (in `SUBDATA.DAT`) | `u32 count`, then `count + 1` u32 offsets | 107 members. **Member 98 (Switch; 96 on PS4):** all HD sprites and cut-ins, as one Disgaea 5-format anm pack (669 blocks, 1,346 textures, 1,658 palettes). **Member 43:** character table. **Member 73:** an older face set (the HD faces are `anmdat.fad`, below). **Member 40:** `magic.dat`. **Members 42 to 46:** character tables (JP, EN, Chinese, Korean, French); **47:** descriptions (EN; `SUBDATA_FR.DAT` has the French). **64, 66, 68, 70:** English voice banks (`DSARC FL`). **71:** the 216 classic sprites (the three Cannons and weapons). | **PS4 numbering differs:** Switch 47 to 54 are one lower on PS4, 56 to 95 and 99 to 105 two lower (Switch 73 is PS4 71); members 43 and 71 are byte-equal across editions (PS4 43 and 69), while Switch members 26 to 30, 33, 64, 66, 68, 70, 96, 97, 98 and 103 have no byte-equal PS4 member (**Verified offline**, 2026-11-24) |
| `anmdat.fad` (in `SUBDATA.DAT`; PS4 `zz_anmdat.fad`) | FAD with `YKCMP_V1` entries | 268 HD face textures `swf####`, 256 × 256 (22,532,352 bytes on Switch) | [data.md](data.md#face-portraits-disgaea-1-complete) |
| `data/script.dat`, `script_fr.dat` | Script archive | Event scripts and text | |
| `data/font.bin` | Font | | |
| Voice banks `voice01.pbd`, `voice02.pbd` (+ `_en` versions) | `.pbd` banks | Voices | Which clip plays for which event is **Unknown** |
| PS4 `eboot.bin`, `sce_sys/` | PS4 executable and metadata | | |
| Switch `main` (ExeFS) | ARM64 executable | | |

`PS_FS_V1` layout: `char magic[8] "PS_FS_V1"`, `u64 count`, then `count × {char name[48], u64 size, u64 offset}`.

## Disgaea DS

The DS version's ROM uses `DSARCIDX` and `DSARCFL` archives and `.dat.tbl` tables (**Community-reported**:
[FireyFly's notes](https://gist.github.com/FireyFly/d9ef476107d8b11cbe7f)). `DSARC FL` is also the format of Disgaea 5's
sound-effect archive.

## Save files

- **Disgaea PC:** `SAVEDATA/` (Steam Cloud). Each save's payload is XORed with a 4-byte key stored in the file, and the
  payload is `YKCMP_V1`-compressed, the same compression Disgaea 5 uses ([layout](../../../formats/saves.md#disgaea-pc-layout)). A QuickBMS script by HenryEx decodes and re-packs them (aluigi noted the size field
  includes 20 header bytes) (**Community-reported**: [ZenHAX](https://www.zenhax.com/viewtopic.php@t=4698.html)).
- **Disgaea 1 Complete (Switch):** the EdiZon save editor has a config for it (**Community-reported**).

## Check that an install is clean

[hashes-pc-steam.txt](hashes-pc-steam.txt) has the SHA-256 of all 9 files of a clean Steam install (app 405900, build
1375097, scanned 2026-11-02; save data left out). From the folder that **contains** `Disgaea PC/`, run:

```bash
shasum -a 256 -c hashes-pc-steam.txt
```

(`sha256sum -c` on Linux.) Any `FAILED` line is a file a mod or tool changed, or a different build.

## Sources

- File listings: this project's scans (2026-11-03) ([method](../../../CONTRIBUTING.md#file-listings)).
- [pg_disatools](https://github.com/ProgSys/pg_disatools) by ProgSys: archive and data-file notes.
- D5 Mod Kit development fork (Sean13128): `d5lib/d1skill.py`, `d5lib/d1cart.py`; this project's D1C source reader report (2026-11-02).
- [Disgaea PC HD sprite mod thread](https://steamcommunity.com/app/405900/discussions/4/1742260837206441731) (SUBDATA size limit).
- [ZenHAX save thread](https://www.zenhax.com/viewtopic.php@t=4698.html); [FireyFly's DS notes](https://gist.github.com/FireyFly/d9ef476107d8b11cbe7f).
