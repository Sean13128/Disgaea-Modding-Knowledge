# Disgaea 4 — File Index

- **Versions covered:** Disgaea 4 Complete+ for PC (Steam app 1233880, build 6150765; `Disgaea4pc.exe` SHA-256
  `2436d4b39aff4291dee71b65055a11161e7ec9186ff98cc2deebc4411614c765`). Disgaea 4: A Promise Revisited for PS Vita
  (PCSE00360), file names only
- **Install / root path:** PC `steamapps/common/Disgaea 4 Complete+/`. Vita `app/PCSE00360/`
- **Folder trees with sizes:** [layout-pc-complete-plus.txt](layout-pc-complete-plus.txt), [layout-vita-a-promise-revisited.txt](layout-vita-a-promise-revisited.txt)

The PC version is also on Xbox Game Pass; whether its files match the Steam build is **Unknown**.

## File map (Complete+ PC)

| Path | Type / format | Contents | Notes |
|---|---|---|---|
| `Disgaea4pc.exe` | x64 PE, image base `0x140000000` | Game executable | See [functions.md](functions.md) |
| `Data/DATA/<LANG>/DATABASE_<LANG>.dat` | NSAC v2 archive | Data tables: `char.dat`, `mskill.dat`, `magic.dat`, `charhelp.dat`, `charPersonal.dat` … | [data.md](data.md). One per language folder: `CN`, `EN`, `FR`, `JP`, `KO`, `TW` |
| `Data/DATA/<LANG>/<LANG>_GameString.dat` | Text | UI and game strings | |
| `Data/DATA/<LANG>/EFFECT.dat`, `BG.dat` | Archives | Effects, backgrounds | |
| `Data/DATA/ANM_HI.dat` (1 GB) | NSAC of `anmNNNNN.lzs` | Sprites; illustrations in groups `10000 + body` | Language folders also have `ANM_HI_<LANG>.dat` |
| `Data/DATA/anm0.fad` (41 MB) | FAD | | Purpose **Unknown** |
| `Data/DATA/WF.dat` | Archive | Face icons, `swf<body>.nltx` | |
| `Data/DATA/START.dat` | Archive | Start-up resources; contains `InProgramTxt.dat`, whose strings hint at a debug menu (**Community-reported**: TCRF) | |
| `Data/DATA/MAP.dat`, `MAPDATA.dat` | Archives | Map geometry, map data | |
| `Data/DATA/OBJ.dat`, `TEXTURE.dat` | Archives | 3D objects, textures | |
| `Data/DATA/SHIPPARTS.dat` | Archive | Pirate-ship parts | |
| `Data/DATA/ITEMSYMBOL.dat` | Archive | Item icons | |
| `Data/DATA/FONT.dat`, `Data/font.bin` | Fonts | | |
| `Data/DATA/LOGO.dat`, `NOWLOADING.dat`, `NISANMSHADOW.dat`, `StuffedRoll.dat` | Small archives | Logo, loading screen, sprite shadow, credits (by name) | Contents unchecked |
| `Data/SOUND/bgm.dat`, `se.dat` | Archives | Music, sound effects | |
| `Data/SOUND/<LANG>/voice.dat` | FAFS | Voices: `en/<group>/voice_<line>.nlsd` | [assets.md](assets.md#voices) |
| `Data/SOUND/SoundData_*.dat` | Tables | Indexes for BGM, SE, voice and story-voice (`Msg`) lines | |
| `Data/SAVEDATA/<lang>/` | | One save file per language | Format **Unknown** |
| `steam_api64.dll`, `libcurl.dll`, `libssl`/`libcrypto` | DLLs | Steam, networking (online features) | |

The Vita release uses the same names, with language data in `Data/DATA_EN/` (`database_EN.dat`, `EN_GameString.dat` …)
and sound in `SOUND/`, `SOUND_EN/`, `SOUND_JP/`.

**Event scripts:** the import research reads a `script.dat` with 24,036 events (15.2 MB) from the database archive
(**Community-reported**).

## Archives & containers

**Counts for the whole install** (Complete+ PC, this project's inventory, 2026-10-18): 77 files, 6.7 GB; **51 archives**,
of which 45 NSAC v2, 5 FAFULLFS and 1 DSARC FL. The EN database holds 957 character rows, 428 skill rows, 1,230
evility rows, 24,036 script events and 331 help rows. Five `Data/SAVEDATA/*/savedata.dat` files ship with the game.

- **NSAC v2** (`DATABASE_*.dat`, `ANM_HI.dat`): 28-byte header `{char magic[4] "NSAC", u16 version 2, u16 count,
  u32 file size, u32 data start, u32 index size (= count × 10), u32 names size, u32}`, then the index, then a names block of
  `count × {u16 name length, u32 offset, u32 size, name, \0}`.
- **Tables** inside: `u32 count`, then fixed-size rows. The Kit's reader opens the database archive with no
  de-obfuscation step, so it isn't obfuscated the way D5's archives are (**Verified offline (Kit)**). Whether any other
  D4 PC file is obfuscated is **Unknown**.
- **LZS:** sprite members are NIS LZS (the same compression as D5).
- **FAFS / FAFULLFS:** voice archives (the same family of container D5 and D6 use).
- **PS3 / Vita:** TXF images (PS3), NMT images and NSAC containers (Vita), NIS LZS on both (**Community-reported**:
  [Scarlet](https://github.com/xdanieldzd/Scarlet)).

## Data tables
See [data.md](data.md).

## Save files
PC: `%APPDATA%\Nippon Ichi Software, Inc\Disgaea 4 Complete+\<Steam account ID>\SaveData\`, with `config.ini` and
`keysettings.bin` one level up (PCGamingWiki, SteamDB). The install folder also has `Data/SAVEDATA/<lang>/`; which one
the game uses for saves is **Unknown**. Format not documented yet. See [saves](../../../formats/saves.md).

## Leftovers

- The opening movie file is still named `dis3_0.dat`, a sign D4 was built from Disgaea 3's code
  (**Community-reported**: TCRF). The PS3 D2 (Brighter Darkness) also ships a `dis3_0.dat`.
- Many sprite sheets in `ANM_HI.dat` hold invisible developer notes that show when the last palette colour is changed
  (**Community-reported**: TCRF).

## Check that an install is clean

[hashes-pc-steam.txt](hashes-pc-steam.txt) has the SHA-256 of all 77 files of a clean Steam install (app 1233880, build
6150765, scanned 2026-11-02; save data left out). From the folder that **contains** `Disgaea 4 Complete+/`, run:

```bash
shasum -a 256 -c hashes-pc-steam.txt
```

(`sha256sum -c` on Linux.) Any `FAILED` line is a file a mod or tool changed, or a different build.

## Sources

- D5 Mod Kit development fork (Sean13128): `d5lib/d4source.py` (NSAC reader, table strides), `docs/D4_IMPORT.md`.
- [The Cutting Room Floor: Disgaea 4](https://tcrf.net/Disgaea_4:_A_Promise_Unforgotten) (seen through search results; the page couldn't be opened from this session).
- [Scarlet](https://github.com/xdanieldzd/Scarlet) by xdanieldzd: PS3/Vita formats.
- File listing: this project's scan of a Steam install (2026-11-03) ([method](../../../CONTRIBUTING.md#file-listings)).
