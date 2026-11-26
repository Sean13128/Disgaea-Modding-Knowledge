# FAFULLFS / FAFS

Archive format. **Used by:** Disgaea 5 Complete PC (`Sound_Voice`, `Sound_Msg`), Disgaea 4 Complete+ PC (`SOUND/<LANG>/voice.dat`),
Disgaea 6 Complete (`data/data.dat`, 3 GB), Disgaea 7 (`data/map_1_00.dat`, maps stored uncompressed; likely the other `*_1_00.dat` archives too).

**Evidence:** layout read from Makai Kit's `fafullfs` reader (**Verified offline**: source read; the reader is used by its
D6/D7 tools). All values little-endian.

## Layout

**Header** (40 bytes):

| Offset | Size | Field |
|---|---|---|
| 0x00 | 8 | Magic `"FAFULLFS"` |
| 0x08 | 4 | File count |
| 0x0C | 4 | Unknown |
| 0x10 | 8 | Offset of the path block |
| 0x18 | 8 | Length of the path block |
| 0x20 | 8 | Offset of the file table |

**File table:** `count × 48` bytes, at the file-table offset:

| Offset | Size | Field |
|---|---|---|
| 0x00 | 8 | Checksum |
| 0x08 | 8 | Path offset (into the path block) |
| 0x10 | 8 | Unknown |
| 0x18 | 8 | Size |
| 0x20 | 8 | Data offset |
| 0x28 | 8 | Timestamp. Disgaea 5's readers never read it, so any value works there (**From decomp**, 2026-11-25; addresses on the [Disgaea 5 functions page](../games/mainline/disgaea-5-alliance-of-vengeance/functions.md)) |

**Path block:** NUL-terminated file paths (e.g. `en/<group>/voice_<line>.nlsd` in D4's voice archive).

What the checksum covers, and whether the games check it, is **Unknown**.

## Tools
[Makai Kit](https://github.com/HybridEidolon/makaikit) (`fafullfs` crate, Rust), D5 Mod Kit (`d5lib/fafs.py`).
