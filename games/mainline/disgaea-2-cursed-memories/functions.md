# Disgaea 2: Cursed Memories — Engine Functions

**Executable:** the macOS build of Disgaea 2 PC (Steam app 495280, build 2643362), `disgaea2app.app/Contents/MacOS/`.
Addresses are virtual addresses in that Mach-O. The Windows build is a different executable; use the names, not the
addresses, there.

**Evidence:** names come from the **symbols the Mac build ships with** (C++ mangled names, demangled with `c++filt`):
these are the developers' own names, so they're reliable. What each function *does* beyond its name is a reading of the
name and signature (**From decomp** at best). About 6,200 functions; 2,146 have symbol names across 152 classes plus
about 1,000 free functions.

## Main loop and debug

| Function | Address | Notes |
|---|---|---|
| `GameMain_Update()` | `0x100007DD0` | Per-frame game update |
| `DebugView()` | `0x100023BB0` | |
| `DebugSet()` | `0x10003A630` | |
| `DebugMenu1(int, int)`, `DebugMenu2()` | `0x10003A670`, `0x10003A680` | A developer menu survives in the code |
| `debug_flag_view()` | `0x10003A920` | Flag viewer |
| `CDeveMgr`, `CDeveMenu` | `0x10017E1F0` …, `0x10017DE30` … | Developer-menu framework: `init`, `run`, `disp`, `addMenu` |

Whether any of these can be reached in the retail build is **Unknown**. TCRF documents a debug menu in the PS2 version.

## Files and archives

| Class / function | Address | Notes |
|---|---|---|
| `NisFs::Init(const char*, bool)` | `0x10014BE80` | File system start-up |
| `NisFs::Read(const char*, void*, int, int)` | `0x10014C270` | Read by name |
| `NisFs::SetBypassPath(const char*)` / `GetBypassPath` | `0x10014D320` / `0x10014D360` | **A "bypass path"**: by its name, a folder whose files are read instead of the archive's. If it works in the retail build, it's a loose-file mod route. **Unknown**: untested |
| `CFileMgrMlt::addFsDat(const char*)` | `0x100187440` | Registers a data archive |
| `CFileMgrMlt::openMlt` / `openArc` | `0x100189E60` / `0x10018A480` | Opens multi-file / archive files |
| `CFileMgrMlt::getImageTableIndex` | `0x1001897E0` | |
| `CFileArc::read(const char*, …)`, `size`, `index`, `exist` | `0x1001842B0` … | Archive member access by name |
| `CFile::load`, `loadGz`, `write`, `sync` | `0x100182D50`, `0x100182AF0`, `0x100183780`, `0x100183980` | gzip-compressed files are supported |
| `CCompress::slideDec`, `rlDec`, `zlibDec`, `autoDec` | `0x100180BB0`, `0x1001809E0`, `0x1001817E0`, `0x100181860` | Decompressors: sliding-window LZ, run-length, zlib, and an auto-detecting front end |
| `CCompress::slideEnc`, `slideEnc2`, `rlEnc` | `0x10017FA00`, `0x10017EF50`, `0x10017ECE0` | Compressors (the game can write these formats, e.g. for saves) |

## Database

| Class / function | Address | Notes |
|---|---|---|
| `clsBaseDataBase::mBSearch(unsigned int)` | `0x100004670` | **Binary search** by ID: tables must be sorted by ID |
| `clsBaseDataBase::mGetIndex`, `mInitializeIndexList`, `mSetNum` | `0x100004610`, `0x1000045B0`, `0x1000044B0` | Row count is `unsigned short` (≤ 65,535) |
| `clsLoadManager` | `0x1001B9260` … | Load queue and "Now Loading" screen |

Table types are listed on [data.md](data.md).

## Characters, items, skills

| Function | Address |
|---|---|
| `MakeCharacter(STATUS*, int, int, int, bool)` | `0x100014510` |
| `addCharacter(int, int)` | `0x10000F6B0` |
| `delete_char(int)` | `0x100013220` |
| `change_UnitMonster(CHARACTER*, int)` | `0x1000198B0` |
| `get_Statuss()` | `0x100019510` |
| `learn_EGmagic(CHARACTER*, int)` | `0x10001A760` |
| `add_magic2(STATUS*, int, int, int)` | `0x10001A6A0` |
| `GetMonsterWeaponMagicNo(STATUS*)` | `0x10001A540` |
| `MakeItem(ITEM*, int, int, int)` | `0x10000F760` |
| `DestroyItem(ITEM*)` | `0x100014D40` |
| `RNKtoLV(ITEM*, int)`, `LVtoRNK(int)` | `0x10001ADD0`, `0x10001AF40` |
| `set_rare(int)` | `0x100016090` |

## Battle and maps

| Function / class | Address | Notes |
|---|---|---|
| `damage(int, long long, float*, int, WORLD_ADM*)` | `0x10000B720` | Damage is a 64-bit value |
| `D_job1(void*)` | `0x10000BCE0` | |
| `put_GE(int, int, int, int, int)` | `0x10001C590` | Geo Effect placement |
| `DungeonClearCheck()` | `0x100015E90` | |
| `TreasureMapCheck(int, int)` | `0x1000136D0` | |
| `M_AR` (`Load`, `Init`, `MakeEffectPanel`, `AddCube` …) | `0x100061100` … | Map area: panels built from cubes |
| `M_RS` | `0x100060C40` … | Map resources |
| `AO` (`set_anmfile`, `set_group`, `set_3dobjfile`, `muki` …) | `0x1000249B0` … | Actor objects: sprite files, groups, facing (`muki` = direction) |
| `Anm::clsPlayManager`, `clsPlayTask`, `clsPlayControl`, `clsPlayManager3D` | | Sprite animation playback |

## Scripts and text

| Function | Address |
|---|---|
| `SCRIPT::sOpenScriptPack()` | `0x10009FE80` |
| `SCRIPT::job()` | `0x10009FF80` |
| `SCRIPT::talkjob()` | `0x1000A0F20` |
| `SCRIPT::loadS(char, char*, int)` | `0x10009AD10` |
| `SCRIPT::gettalkad(int)` | `0x1000A28B0` |

## Saves

| Function / class | Address |
|---|---|
| `setSaveData()` | `0x100012490` |
| `save_chrs(CHR_SAVE*, CHARACTER*)` | `0x100016620` |
| `NSavedata::CSavedataMenu` (`init`, `readHeaders`, `run`) | `0x10015FE20` … |

## Sound, rendering, platform

`NisSound::clsSound` (42 functions), `CSndPlay`, `CSndMgr`, `CFileArcSound`, `BGMplay` (`0x10000D170`),
`SEplay_DelaySet` (`0x10000D390`); `CGraMgr`, `CGraTexMgr`, `CGraClutMgr` (palettes), `CGraOpenGLShader` (OpenGL);
`clsFontManager`, `clsFont_ffm` (FFM fonts); `CPad`, `CRawPad`, `TOUCH_EVENT` (input, including touch);
`CSteamLib` (Steam); `clsNftpClient`.

## Open questions

- Does `NisFs::SetBypassPath` work in the retail build, and what path does it use?
- Can the developer menus be reached?
- The same functions in the Windows executable.

## Sources
This project's private decomp index of the Mac build (symbol names and addresses only).
