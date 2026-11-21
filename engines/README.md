# Engine Families

Which games share code and formats, so knowledge about one transfers to another. Based on function matching between
executables (BSim, run privately in this project; the results are game-derived and not published), plus the shared
formats on [formats/](../formats/).

## Function matching against Disgaea 5

| Game | Build analysed | Functions | Matched to D5 | Close matches (≥ 0.9) |
|---|---|---:|---:|---:|
| Disgaea 7 | PC Steam | ~60,600 | ~4,800 | ~3,000 |
| Disgaea 4 | Complete+ PC | ~19,900 | ~3,400 | ~2,300 |
| Disgaea 6 | Complete PC | ~41,900 | ~3,400 | ~1,700 |
| Disgaea 2 | PC (macOS) | ~6,200 | few | ~10 |
| Disgaea PC (D1) | PC (32-bit) | ~2,400 | few | ~20 |
| Disgaea 1 Complete | Switch (ARM64) | ~2,400 | few | ~20 |

Low numbers for D1 Complete are partly because it's ARM64 and D5 is x64. Its data is close to D5's (below).

## Families

| Family | Games | What they share |
|---|---|---|
| **Classic** | Disgaea 1 (PS2/PSP/PC), Disgaea 2 | IMY-compressed archives, TX2/TXP textures, sprite sheets; XOR + `YKCMP_V1` saves on PC. Disgaea 2 PC (2017) also uses D5 PC's database framework (same table type names), even though its code matches D5 poorly |
| **D3 / D4** | Disgaea 3, Disgaea 4, Disgaea D2 | D4 was built from D3's code (`dis3_0.dat` is still there; D2 ships one too). anm sprites with 16-byte node rows. D2 also uses D5-style NISPACK archives, so it sits between the D4 and D5 families |
| **D5** | Disgaea 5, Disgaea 1 Complete (data) | anm sprites with 20-byte node rows; NISPACK archives; D1 Complete's HD sprites are D5 files and its monsters use D5 action IDs |
| **3D** | Disgaea 6, Disgaea 7 | 3D models, Lua scripts, data-driven effects (confirmed for D7), Makai Kit loader; D6 characters port to D7 |

Across all of them: the skill event scheme `12,000,000 + skill ID` (seen in D4 and D5), kind/gender codes, and similar
table layouts for characters, skills and evilities.

## The same tables across games

The core tables keep their names from game to game while their rows grow. Row sizes in bytes (links go to each game's
data page):

| Table | D1 PC | D1 Complete | D2 (Brighter Darkness) | D4 Complete+ | D5 Complete | D6 / D7 |
|---|---|---|---|---|---|---|
| Characters / classes (`char.dat`) | 216 | 224 | 676 | 684 | 908 | JSON records (`character`) |
| *(Disgaea 2 PC: 352; Disgaea 3: 656)* | | | | | | |
| Skills (`magic.dat`) | 172 | 180 | 296 | 356 | 1,140 | JSON (`act`) |
| Evilities | — (none in D1) | — | 232 (`mskill.dat`) | 212 (`mskill.dat`) | 96 + string pool (`Skill.dat`) | JSON (`evility`, data-driven effects) |
| Descriptions (`charhelp.dat`) | — | — | 278 | 386 | 1,428 | JSON (`charahelp`) |

Patterns:
- **Base stats and aptitudes** by game (row-relative; eight values each):

  | Game | Header / row | Base stats | Aptitudes |
  |---|---|---|---|
  | Disgaea PC | 8 / 216 | `+0x62` LE16 | `+0x4C` bytes ×10 |
  | Disgaea 1 Complete | 8 / 224 | `+0x6A` LE16 | `+0x54` bytes ×10 |
  | Disgaea 2 PC | 4 / 352 | `+0xA0` LE16 | `+0x87` bytes ×10 |
  | Disgaea 3 | 8 / 656 | `+0x04` LE16 | `+0x26A` bytes |
  | Disgaea 4 | 4 / 684 | `+0x04` LE16 | `+0x27E` bytes |
  | Disgaea D2 | 4 / 676 | `+0x04` **BE16** | `+0x274` bytes |
  | Disgaea 5 | 4 / 908 | `+0x04` LE16 | `+0x350` bytes |

  From D3 on, the row starts with the stats and keeps the ID at `+0x194` (D3, D4, D2) before D5 moved it to `+0xD0`.
  (Source: this project's faithful-stats extraction, every listed row read.)
- **IDs grew** from 16-bit (D2, D4) to 32-bit (D5).
- **Stat order** is HP SP ATK DEF INT RES HIT SPD in D2 (Brighter Darkness), D5 and D7. **Disgaea 1, 2, 3 and 4 store
  RES and SPD swapped** (HP SP ATK DEF INT SPD HIT RES). The classic games store aptitudes as tenths (×10 for percent).
- **Evilities moved from rows to code to data:** D2 (Brighter Darkness) links evilities to classes only by ID numbering;
  D4 lists them per rank in `char.dat`; D5 keeps behavior in the executable; D7 describes behavior in the table itself.
- **Text** went from Shift-JIS (D1) to UTF-8 (D2 onward), and from one language per file to five (D5) or six (D7)
  slots per row.

Sources: [D1](../games/mainline/disgaea-1-hour-of-darkness/data.md), [D2 (Brighter Darkness)](../games/spin-offs/disgaea-d2-a-brighter-darkness/data.md),
[D4](../games/mainline/disgaea-4-a-promise-unforgotten/data.md), [D5](../games/mainline/disgaea-5-alliance-of-vengeance/data.md),
[D7](../games/mainline/disgaea-7-vows-of-the-virtueless/data.md).

## What this means in practice
- A D5 address or table layout is a good first guess for D4, D6 or D7. Check it against the target before relying on it.
- Classic-era games need their own research.
