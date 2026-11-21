# Disgaea 5 Complete — File Index

- **Version / platform covered:** Disgaea 5 Complete, PC (Steam app 803600, build 3528766). Two executable builds exist; see [functions.md](functions.md#which-build-an-address-belongs-to)
- **Install / root path:** `steamapps/common/Disgaea 5 Complete/`
- **Folder tree with sizes:** [layout-pc-steam.txt](layout-pc-steam.txt) (from a modded install, with mod files filtered out)
- A clean copy can be downloaded from the Steam console: `download_depot 803600 803601 2485590530922474185` (build 3528766)

## File map

| Path | Type / format | Contents | Notes |
|---|---|---|---|
| `disgaea5.exe` | x64 PE, not packed | Game executable | Source paths and format strings are left in (`anm%05d.lzs`, `map%05d.%s`, `skit%04d.dat`, `effect%04d.obf`, `mki0%04d.obf`) |
| `Data/Database/Database_0.dat` | NISPACK | 90 data tables | [data.md](data.md) |
| `Data/Anime/ANM_HI_{JP,EN,FR}.dat` | NISPACK of `anmNNNNN.lzs` | Sprites, portraits, cut-ins, weapon sprites; also `anm_resident`, `anm_startup_N` | [assets.md](assets.md) |
| `Data/Map/Map_{JP,EN,FR}.dat` | NISPACK of `mapNNNNN.lzs` | 448 maps; each is a pac of `.hex` `.chr` `.obf` `.gop` | EN/FR hold 10 localized replacements |
| `Data/Object/Object_*.dat` | NISPACK | `.obf` 3D objects (map objects; `mki*` = Netherworld Edit parts) | |
| `Data/Start/Start_*.dat` | Nested NISPACKs (inner ones not obfuscated) | UI textures (`Texture.dat`, `.txf`), `charworld.dat`, `Effect.dat` (`effect*.obf`), fonts | |
| `Data/Script/script.dat` | Obfuscated bytecode | 19,053 event and skill scripts | Opened directly, not through an archive |
| `Data/Script/talk.dat` | Obfuscated | Dialogue, all languages | Opened directly |
| `Data/Skit/Skit_0.dat` | NISPACK of `skitNNNN.dat` | Skits | |
| `Data/Sound_BGM/` | `PS_FS_V1` archive of `.ogg` | Music | Not obfuscated |
| `Data/Sound_SE/` | `DSARC FL` archive of `.vag` | Sound effects | Not obfuscated |
| `Data/Sound_Voice/`, `Data/Sound_Msg/` | `FAFULLFS` | Battle voices, story voice lines | |
| `Data/Movie/dis5_*.wmv` | Movie | Opening movie per language | See [assets.md](assets.md#open-questions) |

## Archives & containers

- **Obfuscation:** every NISPACK archive, plus `script.dat` and `talk.dat`, is byte-obfuscated:
  `plain = swap_nibbles(b XOR 0xF0)`. It covers the header and directory too. Sound archives aren't obfuscated.
- **NISPACK:** `"NISPACK\0"`, `u32 0`, `u32 count`, then `count × {char name[32], u32 offset, u32 size, u32 stamp}`;
  data usually 0x800-aligned (the game doesn't require it). The game reads the whole directory at startup.
- **Lookup:** case-insensitive **prefix** match, linear, first match wins; text-language archive first, then JP, then
  the rest. No sorting needed, no sizes or stamps checked, no checksums. An entry with offset `0xFFFFFFFF` is skipped
  on read (a tombstone).
- **Languages:** `*_EN.dat` / `*_FR.dat` only hold replacements for files in `*_JP.dat`. Chinese and Korean use the EN
  archives with text from five-language slots in the tables.
- **Compression:** `.lzs` (NIS LZS: `ext[4]`, `u32 rawsize`, `u32 compsize`, `u32 marker`, back-references up to 255
  bytes); `YKCMP_V1` (type 7 zlib, type 4 Yukes LZ); `pac` inside maps (`u32 count`, 12 pad bytes,
  `count × {u32 offset, char name[28]}`).

## Data tables
All in `Database_0.dat`: see [data.md](data.md).

## Save files

- **Folder:** `%APPDATA%\Nippon Ichi Software, Inc\Disgaea 5 Complete\<Steam account ID>\SaveData\`
  (`Save_001.sav` …, `AutoSave.sav`, `SystemSave.sav`). `config.ini` and `keysettings.bin` are one level up.
- **Format:** bytes `0x000–0x1BF` are a nibble-obfuscated header with the magic `NIS_DISGAEA5_COMPLETE_SAVEDATA_`, title
  and summary. From `0x1C0`: `u64 0xE5B2688ACB279CA6`, then every u64 XORed with `0xE76FCBE3F6D46A37`, giving a
  `YKCMP_V1` (type 4) stream that decompresses to exactly `0x89B8C8` bytes: the whole game state.
- **No checksum** beyond the magic. `SystemSave.sav` is a 100-byte nibble-obfuscated block.
- Unit records inside the state: [functions.md](functions.md#unit-record-in-the-game-state).

## Configuration

`config.ini` `[ConfigSettings]`: `TextLanguage`, `VoiceLanguage`, window and resolution, VSync, AA, `ButtonGuide`,
`UseMouse`, `SkipMovie` (1 skips the opening movie; useful under Wine, see [Mac and Linux](../../../guides/modding-on-mac-and-linux.md)). `[DbgSettings] EnableCrashReport` is the only debug key. There are no command-line switches and no debug
menu in the executable.

## Sources

- D5 Mod Kit by eraser4521: [FORMATS.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/FORMATS.md), [EXE_ANALYSIS.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/EXE_ANALYSIS.md), [MAPS_AND_OBJECTS.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/MAPS_AND_OBJECTS.md). **Community-reported.**
- File listing and build number: this project's scan of a Steam install (2026-11-03) ([method](../../../CONTRIBUTING.md#file-listings)).
