# Disgaea D2: A Brighter Darkness — File Index

- **Version / platform covered:** PS3, North America (BLUS31313), with update 1.40 and the DLC pack (title NPUB31321)
- **Root path:** `PS3_GAME/USRDIR/` on the disc; the update and DLC have their own `USRDIR/Data/`
- **Folder tree with sizes:** [layout-ps3.txt](layout-ps3.txt)

## File map

| Path | Type / format | Contents | Notes |
|---|---|---|---|
| `USRDIR/EBOOT.BIN` | PS3 executable (PowerPC + SPU) | Game code | Update 1.40 ships a newer one. Must be decrypted (e.g. by RPCS3) before analysis |
| `USRDIR/Data/START.dat`, `START_1.dat` … `START_6.dat` | NISPACK | Start-up data, including the data tables | Each is a different **patch level** (below) |
| `USRDIR/Data/ANM_HI.dat`, `ANM_HI_1.dat` … `ANM_HI_6.dat` | Archives of `anmNNNNN.lzs` | Sprites; illustrations | |
| `USRDIR/Data/BG.dat`, `ITEMSYMBOL.dat`, `SHIP.dat`, `MAP/` | Archives | Backgrounds, item icons, ship data, maps | Maps are `MAP/mpNNN/mapNNNNN.lzs`: the castle hub is 30001 (Castle Hallway) and 30003, the first battle stage 101 (`map00101`). `ITEMSYMBOL.dat` is the last archive the loading thread reads |
| `USRDIR/Data/SOUND/` | | Sound; voices in `SndPakVoice` banks | Voice banks have anonymous index tables |
| `USRDIR/Data/dis3_0.dat` | | **Unknown** | Disgaea 4 PC ships a file of the same name (its opening movie) |
| Update 1.40 `USRDIR/Data/` | 26 files | Replacement data | Load over the disc's |
| DLC `USRDIR/Data/` | 45 small files | DLC data | |

**Add-on archives:** the base `ANM_HI.dat` holds stand-in art (Laharl or a Warrior) under the numbers of add-on
characters (Adell 400, Mao 430, Zetta 460, Baal 580 …); their real art is in `ANM_HI_1.dat` … `ANM_HI_6.dat`. Read the
add-on archives first, newest first. Likewise `START.dat` names add-on characters in Japanese, while `START_4.dat` has
the English names (**Community-reported**: Character Forge).

**Archive overlay order** (**From decomp** and **Verified offline**, 1.40; the rule is tested with a broken variant): the
game registers `START.dat` then `START_1.dat` … `START_7.dat`, and `ANM_HI.dat` then `ANM_HI_1.dat` … `ANM_HI_6.dat`,
into pack lists, and a lookup scans from the last registered back to the first and takes the first name match. So
`START_7` beats `START_6` … beats `START`, and `ANM_HI_6` beats … `ANM_HI`. The effective `char.dat` is therefore the
copy in `START_4.dat` (the last pack holding it); the copies in `START.dat`, `START_1.dat` and `START_2.dat` differ
from it in 100 rows. A reader that takes the first match gets the wrong art for some characters (see
[assets.md](assets.md#sprite-banks-for-a-character)).

**Disc, update and DLC:** the disc's `Data/` is the base. Update 1.40 adds `START_7.dat` (holding `mitem.dat` and
`saveicon.dat`) and replaces `SndPakVoiceEN_0.pak` (48 English clips differ) and `SndPakMsgEN_4.pak`. The DLC (45
`flag*.edat` files, NPD-protected) holds no art, tables or audio. The game's file layer builds root-relative paths;
that the PS3 puts the update over the disc by replacing files of the same relative path is assumed, not read
(**Unknown**).

## Archives & containers

- **NISPACK**, as in Disgaea 5: `"NISPACK\0"` + 16-byte header, 44-byte entries with 32-byte names. The Kit's D2
  reader opens it with no de-obfuscation step.
- **Tables** inside: a **big-endian** `u16` row count, two unused bytes, then fixed-size rows. Text is NUL-terminated
  UTF-8 (verified over every row).
- **Patch levels:** `START.dat`, `START_1.dat` and `START_2.dat` hold the same table names as `START_4.dat` but with
  **different** `magic.dat`, `mskill.dat` and `charhelp.dat` contents. Read one patch level; never mix tables from two.
- **Compression:** sprite members are NIS LZS (`dat\0` header). Face textures are `txf\0` + LZS around a TXF
  (format 0, ARGB8888, big-endian width/height/depth).

## Data tables
See [data.md](data.md).

## Save files
PS3 saves (directories such as `NPUB31321_NORMAL_xx`) are protected with Sony's PFD scheme (AES with the console's
public keys). [pfdtool](https://github.com/SteffenL/pfdtool) and [Apollo](https://github.com/bucanero/apollo-ps3)
document the algorithm; the D2 native port's `d2_save_import.py` uses it to turn retail saves into plaintext. The
plaintext layout isn't documented here yet.

## Sources
- D5 Mod Kit development fork (Sean13128): `d5lib/dd2source.py`. **Verified offline (Kit)** for the table geometry.
- [Disgaea D2 native Mac port](https://github.com/Sean13128/disgaea-d2-recomp) (Sean13128): `tools/d2_save_import.py`, `tools/d2_face_atlas.py`, `tools/d2_anm.py`.
- File listing: this project's scan of a disc dump with update 1.40 and DLC (2026-11-03) ([method](../../../CONTRIBUTING.md#file-listings)).
