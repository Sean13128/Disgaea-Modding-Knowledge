# PS_FS_V1

Archive format. **Used by:** Disgaea 5 Complete PC (`Data/Sound_BGM/`, not obfuscated), Disgaea 1 Complete
(`SUBDATA.DAT`). Related but different: `PSPFS_V1` (Phantom Brave PSP, per Scarlet; Disgaea PC's `DATA.DAT` and `SUBDATA.DAT`, with 32-bit fields and 52-byte entries: [layout](disgaea-pc-containers.md#pspfs_v1)).

## Layout (Disgaea 1 Complete)

| Offset | Size | Field |
|---|---|---|
| 0x00 | 8 | Magic `"PS_FS_V1"` |
| 0x08 | 8 | Entry count (u64) |
| 0x10 | count × 64 | Entries: `char name[48]`, `u64 size`, `u64 offset` |

## Tools
D5tools (`musicer`, for D5's music), D1C reader in this project's research.
