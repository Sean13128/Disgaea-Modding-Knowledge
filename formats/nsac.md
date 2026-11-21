# NSAC

Archive format. **Used by:** Disgaea 4 Complete+ PC (`DATABASE_<LANG>.dat`, `ANM_HI.dat`), Disgaea 4: A Promise Revisited (Vita).

## Layout (version 2, Disgaea 4 PC)

| Offset | Size | Field |
|---|---|---|
| 0x00 | 4 | Magic `"NSAC"` |
| 0x04 | 2 | Version (2) |
| 0x06 | 2 | Entry count |
| 0x08 | 4 | File size |
| 0x0C | 4 | Data start |
| 0x10 | 4 | Index size (= count × 10) |
| 0x14 | 4 | Names block size |
| 0x18 | 4 | — |
| 0x1C | | Index, then the names block: `count × {u16 name length, u32 offset, u32 size, name, \0}` |

Not obfuscated in the PC database archive. See [Disgaea 4 INDEX](../games/mainline/disgaea-4-a-promise-unforgotten/INDEX.md#archives--containers).

## Tools
D5 Mod Kit development fork (`d5lib/d4source.py`, reads only), Scarlet (Vita).
