# Disgaea 3: Absence of Justice — Data Tables

**Version:** PS Vita (*Absence of Detention*). **Evidence:** **Community-reported** from this project's Character Forge
reader (decrypted copy).

Disgaea 3 is the **first game with evilities**; many carried into Disgaea 4, so [D4's tables](../disgaea-4-a-promise-unforgotten/data.md)
are a good guide.

## `char.dat`

In `START.dat` (24 members) → `database.lzs` (a NISPACK of 41 members). Two equal LE32 counts, then **721 rows of 656
bytes**. Stats are stored in the order **HP SP ATK DEF INT SPD HIT RES** (RES and SPD swapped against D5), as in D4:

| Offset | Field |
|---|---|
| 0x004 | Base stats (eight u16) |
| 0x194 | Character ID |
| 0x196 | Body ID |
| 0x19C | Portrait reference |
| 0x19E | Colour |
| 0x1B6 | Name (UTF-8) |
| 0x210 | Title |
| 0x26A | Aptitudes (eight bytes) |

The ID and body offsets match Disgaea 4's and Disgaea D2's `char.dat` (`0x194` / `0x196`). For class ranks, the palette
seems to be `character ID − body ID` for the first six ranks (matches the colours; not confirmed in code).

## Open questions
- Skills, evilities and items.

## Sources
This project's Character Forge research notes (2026-10-11). (reader code not published; its results are the tables on this page)
