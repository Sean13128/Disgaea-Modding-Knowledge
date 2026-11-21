# DSARC FL / DSARCIDX

Archive formats. **Used by:** Disgaea DS (`DSARCIDX`, `DSARCFL`, with `.dat.tbl` tables), Disgaea 5 Complete PC
(`Data/Sound_SE/`: `DSARC FL` of `.vag` sound effects, not obfuscated).

## DSARC FL layout

From Makai Kit's `dsarcfl` reader (**Verified offline**: source read). Little-endian.

| Offset | Size | Field |
|---|---|---|
| 0x00 | 8 | Magic `"DSARC FL"` |
| 0x08 | 4 | File count |
| 0x0C | 4 | — |
| 0x10 | count × 128 | Entries: `char name[0x74]`, `u32 size` at `0x74`, `u32 offset` at `0x78`, 4 bytes |

The DS variant (`DSARCFL`, with a separate `DSARCIDX` index) is described in FireyFly's notes; whether it matches this
layout exactly is **Unknown**.

## Tools
[FireyFly's notes and C tools](https://gist.github.com/FireyFly/d9ef476107d8b11cbe7f) (DS), [Disgaea-DS-Manager](https://github.com/Team-BrewSearch/Disgaea-DS-Manager) (DSARC, MSND), [Makai Kit](https://github.com/HybridEidolon/makaikit) (`dsarcfl`: read and write), D5tools (`vagger`).
