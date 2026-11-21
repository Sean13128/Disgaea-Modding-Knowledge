# Disgaea 2: Cursed Memories — File Index

Releases: PS2 (2006), PSP *Dark Hero Days*, and **Disgaea 2 PC** (2017, with all PS2 and PSP content).

- **Version / platform covered:** Disgaea 2 PC (Steam app 495280, build 2643362). The scanned copy is the **macOS** build; the Windows executable differs
- **Install / root path:** `steamapps/common/Disgaea 2 PC/`
- **Folder tree with sizes:** [layout-pc-steam.txt](layout-pc-steam.txt)

## File map

| Path | Type / format | Contents | Notes |
|---|---|---|---|
| `disgaea2app.app/Contents/MacOS/` | Mach-O | Mac executable | Keeps C++ symbol names: see [functions.md](functions.md) |
| `DATA.DAT` (628 MB) | Archive | Main data; contains `WFPACK_PC.DAT` (faces, `SWF<body>.TXP`) and `CUTINPACK.DAT` (illustrations, ANM) | Compressed IMY packages, addressed by chunk ranges |
| `SUB_DATA.DAT` (511 MB) | PSPFS (52-byte rows: name, size, offset) | `ANMPACK.DAT` (NISPACK of 334 `anmNNNN` sprite files); `START.LZS` / `START_VM.LZS`, which expand to a file table (count, then end offset and name per file) holding `char.dat` and `anm0000`–`anm0002.dat` | |
| `mov00.ogm`, `mov01.ogm` | Ogg media (Theora, judging by the bundled libraries) | Movies | |
| `SAVEDATA/` | | Saves (Steam Cloud) | |
| `lib*.dylib` | Libraries | OpenAL, Ogg/Vorbis/Theora, Steam | |

## Save files
Like Disgaea PC: each save is XORed as a whole, then compressed with `YKCMP_V1`. HenryEx's QuickBMS script decodes
and re-packs them (**Community-reported**: [ZenHAX](https://www.zenhax.com/viewtopic.php@t=4698.html)). No save editor
was found for the PC version (a [Steam thread](https://steamcommunity.com/app/495280/discussions/0/133256240737837930)
asks for one). The PSP version has [one](https://github.com/lmc/disgaea-2-save-editor) (needs decrypted saves).

## Check that an install is clean

[hashes-pc-steam.txt](hashes-pc-steam.txt) has the SHA-256 of all 26 files of a clean Steam install (app 495280, build
2643362, scanned 2026-11-02; save data left out). From the folder that **contains** `Disgaea 2 PC/`, run:

```bash
shasum -a 256 -c hashes-pc-steam.txt
```

(`sha256sum -c` on Linux.) Any `FAILED` line is a file a mod or tool changed, or a different build.

## Sources
File listing: this project's scan (2026-11-03) ([method](../../../CONTRIBUTING.md#file-listings)); format notes from this project's classic-art readers; linked threads.
