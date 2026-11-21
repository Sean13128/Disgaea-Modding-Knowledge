# Disgaea 7 — File Index

- **Version / platform covered:** PC (Steam app 2250600, build 13254462)
- **Install / root path:** `steamapps/common/Disgaea 7 Vows of the Virtueless/`
- **Folder tree with sizes:** [layout-pc-steam.txt](layout-pc-steam.txt) (from a copy with mods installed; mod files filtered out of the listing)

## File map

| Path | Type / format | Contents | Notes |
|---|---|---|---|
| `Disgaea7.exe` | x64 PE | Game executable | [functions.md](functions.md) |
| `data.dat` (2 GB, install root) | Archive | All database tables | [data.md](data.md). Read with Makai Kit's database tools |
| `data/model_1_00.dat` (4 GB) | Archive | 3D character and object models; FAD/FANM parts that make up body and expression layers | Models `.nmbm`, sets `.nmbs`, animations `.nmba` |
| `data/texture_1_00.dat` (1 GB) | Archive | Textures (`.nltx`), including face atlases | Faces: window face ID → `icon` crop → `texture` |
| `data/msg_1_00.dat` (1 GB) | Archive | Text | |
| `data/map_1_00.dat` | FAFULLFS (maps stored uncompressed) | Battle maps | |
| `data/misc_1_00.dat`, `shader_1_00.dat` | Archives | Misc data, shaders | |
| `data/bgm_1_00.dat`, `se_1_00.dat`, `voice_1_00.dat` | Archives | Music, sound effects, voices (`.nlsd`) | |
| `NmplDLL.dll`, `NmplDLLMemoryInterface.dll`, `lz4.dll` | DLLs | Engine runtime library, LZ4 compression | |
| `steam_api64.dll` | DLL | Steam | |

Lua scripts for skills and events are requested as files under `data/script/` (`act_*.lua` for skills), so a mod can
supply them through the loader's `files/` folder.

## What a mod install adds

Not part of the game; seen on a copy with mods:

| Path | What |
|---|---|
| `dinput8.dll` | Makai Kit's proxy DLL, which loads plugins (it keeps the original as `dinput8.dll.orig-makaikit` if one existed) |
| `mkplugins/makaikit_modloader_d7.dll` | Makai Kit's D7 mod loader |
| `mkplugins/<plugin>.dll` (+ `.ini`) | Other plugins |
| `mods/<mod>/` | Mods: `databases/<table>/*.json`, `files/…` |
| `mods/_generated/` | Tables the loader rebuilt at startup |

## Archives & containers
Makai Kit includes readers for the container and compression formats NIS uses here (`fafullfs`, `dsarcfl`, `nlsd`,
`ykcmp`). Which container each D7 archive uses isn't documented here yet.

## Data tables
See [data.md](data.md).

## Save files
`%APPDATA%\Nippon Ichi Software, Inc\Disgaea 7\<user ID>\`; `Setting.ini` in the `Disgaea 7` folder (PCGamingWiki, Steam patch notes). Format not documented yet. See [saves](../../../formats/saves.md).

## Check that an install is clean

[hashes-pc-steam.txt](hashes-pc-steam.txt) has the SHA-256 of the 15 files confirmed as stock in a Steam install (app 2250600,
build 13254462, scanned 2026-11-02). The scanned copy had mods installed, so other files are left out. From the folder that **contains** `Disgaea 7 Vows of the Virtueless/`, run:

```bash
shasum -a 256 -c hashes-pc-steam.txt
```

(`sha256sum -c` on Linux.) Any `FAILED` line is a file a mod or tool changed, or a different build.

## Sources

- File listing: this project's scan of a Steam install (2026-11-03) ([method](../../../CONTRIBUTING.md#file-listings)).
- [Makai Kit](https://github.com/HybridEidolon/makaikit) by HybridEidolon.
