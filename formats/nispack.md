# NISPACK

Archive format. **Used by:** Disgaea 5 Complete PC (every data archive), Disgaea D2 PS3 (`START*.dat`), and other NIS games ([Scarlet](https://github.com/xdanieldzd/Scarlet) lists it as "various NIS games").

## Layout

| Offset | Size | Field |
|---|---|---|
| 0x00 | 8 | Magic `"NISPACK\0"` |
| 0x08 | 4 | 0 (Scarlet reads it as a big-endian flag) |
| 0x0C | 4 | Entry count |
| 0x10 | count × 44 | Entries: `char name[32]`, `u32 offset`, `u32 size`, `u32 stamp` |
| … | | Data (usually 0x800-aligned) |

## Variants by game

| Game | Differences |
|---|---|
| [Disgaea 5](../games/mainline/disgaea-5-alliance-of-vengeance/INDEX.md#archives--containers) | Whole archive **byte-obfuscated**: `plain = swap_nibbles(b XOR 0xF0)`, header and directory included. Nested NISPACKs inside `Start_*.dat` are plain. The game matches names case-insensitively by **prefix**, first match wins, language archive first |
| [Disgaea D2](../games/spin-offs/disgaea-d2-a-brighter-darkness/INDEX.md#archives--containers) | Not obfuscated. Tables inside use **big-endian** counts |

## Tools
D5 Mod Kit (`d5lib/nispack.py`), D5tools (`databaser`), Scarlet.
