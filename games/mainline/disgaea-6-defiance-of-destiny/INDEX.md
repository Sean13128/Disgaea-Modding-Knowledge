# Disgaea 6 — File Index

- **Version / platform covered:** Disgaea 6 Complete, PC (Steam app 1749750, build 9145810)
- **Install / root path:** `steamapps/common/Disgaea 6 Complete/`
- **Folder tree with sizes:** [layout-pc-complete.txt](layout-pc-complete.txt)

## File map

| Path | Type / format | Contents | Notes |
|---|---|---|---|
| `Disgaea6.exe` | x64 PE | Executable | Ships with `lz4.dll` and `xaudio2_9redist.dll` |
| `data/data.dat` (3 GB) | `FAFULLFS` | Database tables, plus language and DLC FAD texture tables | Six-digit illustration textures match a model's four-digit character family |
| `data/model.dat` (1 GB) | Archive | 3D character models | |
| `data/msg.dat` (628 MB) | Archive | Text | |
| `data/script.dat` (3 MB) | Archive | Lua event and skill scripts | Makai Kit can rebuild it from mods' Lua files |
| `data/bgm.dat`, `se.dat`, `voice.dat` | Archives | Music, sound effects, voices | |

## Archives & containers
`FAFULLFS` is the NIS archive family also used by Disgaea 5's voice archives. Readers: Makai Kit's
[`fafullfs`](https://github.com/HybridEidolon/makaikit) crate, and D5 Mod Kit's `d5lib/fafs.py`.

## Data tables
See [data.md](data.md).

## Save files
`%APPDATA%\Nippon Ichi Software, Inc\Disgaea 6 Complete\<user ID>\`, with `Setting.ini` (PCGamingWiki). Format not documented yet. See [saves](../../../formats/saves.md).

## Check that an install is clean

[hashes-pc-steam.txt](hashes-pc-steam.txt) has the SHA-256 of all 12 files of a clean Steam install (app 1749750, build
9145810, scanned 2026-11-02; save data left out). From the folder that **contains** `Disgaea 6 Complete/`, run:

```bash
shasum -a 256 -c hashes-pc-steam.txt
```

(`sha256sum -c` on Linux.) Any `FAILED` line is a file a mod or tool changed, or a different build.

## Sources
File listing: this project's scan of a Steam install (2026-11-03) ([method](../../../CONTRIBUTING.md#file-listings)); [Makai Kit](https://github.com/HybridEidolon/makaikit).
