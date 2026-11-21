# Disgaea 2: Cursed Memories — Data Tables

**Version:** Disgaea 2 PC. **Evidence:** table **types** come from the symbol names in the Mac build's database code
(`clsDataBase<…>` and `clsDataBase_Multi<…>` templates; see [functions.md](functions.md)). Their byte layouts aren't
documented yet.

D2 has **no evilities** (they began in Disgaea 3).

## `char.dat`

In `SUB_DATA.DAT` → `START.LZS`. 4-byte header, then **498 rows of 352 bytes**: name at byte 0, ID at 150, body at 152
(decimal); **base stats** at `+0xA0` (eight LE16), **aptitudes** at `+0x87` (eight bytes, ×10), in the order HP SP ATK DEF INT SPD HIT RES. A body's battle
sprites are `anm<body + 1>` in `ANMPACK.DAT`; class bodies carry six palettes, one per rank (**Community-reported**:
this project's Character Forge reader).

## Table types

**Single-language tables** (`clsDataBase<…>`):

| Type | Probably holds |
|---|---|
| `stcCharacterDat` | Characters and classes |
| `stcCharacterHelpDat` | Character descriptions |
| `stcMagicDat` | Skills and spells |
| `stcItemDat` | Items |
| `stcHabitDat` | Innocents (specialists) |
| `stcDungeonDat` | Stages / maps |
| `stcDarksun1Dat`, `stcDarksun2Dat` | Dark Sun (D2's battle-condition system) |
| `stcJudgmentDat` | Dark Court / judgment |
| `stcSenatorDat` | Dark Assembly senators |
| `stcWishDat`, `stcWish2Dat` | Dark Assembly bills |
| `stcPirateDat` | Item World pirates |
| `stcThiefDat` | Steal results |
| `stcZukanDat` | Encyclopedia |
| `stcMusicDat` | Music |

**Multi-language tables** (`clsDataBase_Multi<…>`): `stcStringListDataBase`, `stcTextureDataBase`,
`stcScreenPosDataBase`, `stcConvertAnimeDataBase`, `stcConvertSequenceDataBase`, and sound tables
`stcSoundTable_BGMDataBase`, `_SEDataBase`, `_VOICEDataBase`, `_MSG_ENDataBase`, `_MSG_JPDataBase`.

## Shared with Disgaea 5

Many of these match **Disgaea 5's** table names exactly: `StringListDataBase`, `TextureDataBase`,
`ScreenPosDataBase`, `senator`, `pirate`, `THIEF`, `Wish`, `zukan`, `HABIT`, `dungeon`, `charhelp`, `music`, and the
sound index tables. The 2017 PC port of Disgaea 2 appears to use the **same database framework as Disgaea 5 PC**, so
D5's [table layouts](../disgaea-5-alliance-of-vengeance/data.md) and loading rules (tables binary-searched by ID; row
count ≤ 65,535) are the best first guess. Byte layouts will still differ.

The PS2 version's cheat codes list item, skill and specialist values
([CodeBreaker list](https://almarsguides.com/retro/walkthroughs/PS2/Games/Disgaea2CursedMemories/CodeBreaker/ItemsSkillsAndSpecialistsModifiers)).

## Open questions
- Where the tables are stored (`DATA.DAT` or `SUB_DATA.DAT`) and their byte layouts.

## Sources
This project's private decomp index of the Mac build (symbol names only); D5 table names from D5 Mod Kit.
