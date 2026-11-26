# Disgaea: Hour of Darkness — Assets

## Formats

| Asset | Disgaea PC | Disgaea 1 Complete |
|---|---|---|
| Sprites | `.SH` sprite sheets (4-bit indexed, colour tables) in SOLA archives (`ANM00.DAT` …) in `START.DAT`: [format](../../../formats/disgaea-pc-containers.md#sh-sprite-sheet); pg_disatools Sprite Sheet Editor | **Disgaea 5 anm format**: all HD bodies in one pack (`start.dat` member 98) |
| Textures / pictures | TX2 (type 2 = DXT5); story pictures in `BU` / `NEW_BU` | NLTX; story pictures in `new_bu` |
| Face icons | `START.DAT` member 61 | HD faces: `SUBDATA.DAT` member `anmdat.fad` (268 `swf####`); `start.dat` member 73 is an older set ([data.md](data.md#face-portraits-disgaea-1-complete)) |
| Cut-ins | — | 250 blocks in the anm pack: each one 336 × 1080 ARGB32 picture (D5's cut-in size), a 32 × 32 white flash card, and 17–18 animations in the 8000 range |
| Voices | — | `voice01.pbd`, `voice02.pbd` (+ `_en`) |
| Text | `TALK.DAT` ([scripts](scripts.md)); Shift-JIS in tables | `script.dat`; Shift-JIS in tables |
| Maps | `.MPD` layouts + `.MPP` textures/models (GEO) | |
| Effects | `EFFECT.KM3` (GEO models) | |
| Music | 44 `.ogg` tracks; loop points hard-coded in the executable | |

**Disgaea 1 Complete's sprites are Disgaea 5 sprites.** Monsters carry D5's own monster action IDs (1/7 stand,
11/17 walk, 1021 damage, 2001 melee, 2011 second swing, 2021 chant, 2031 release, 2041 recovery). Generic humanoid
bodies only have standing and guard animations (6001/6007 idle, 7001 stance), so attacks and walking must be
built from held poses (**Verified offline (Kit)**: frames rendered and inspected).

**Which cut-in a character uses:** for every cut-in block, **block = HD body + 1 + palette** (Martial Artist body
1010, palettes 0–5 → cut-ins 1011–1016; Laharl 10 → 11, Etna 20 → 21). The rule is the skill cut-in function
`0x7100056838` (**From decomp**, Switch v0: block = HD block of the row's sprite + palette byte + 1, animation 8091) and was
checked against every block (**Verified offline**). Twenty-three HD rows have no such block (cutscene rows, Jn:Revamp, Carter).
**Corrected 2026-11-25:** an earlier version said Dark Flonne (body 30, palette 2) uses block 32 "not 33" and that a hard-coded
character list explains it. Her character-table row has sprite 3 and **palette byte 0** (the red-bow palette 2 seen in some imports was an importer author's choice, not read from the row; see
[data.md](data.md#character-table-disgaea-1-complete)), so the rule gives block 31, Flonne's cut-in; block 32 is a separately
drawn Dark Flonne picture that the rule does not reach. Whether a hard-coded list or another path reaches it is **Unknown**.

**Cut-in gaps (2026-11, Verified offline):** Carter (body 2560) has no authored cut-in (blocks 2561 and 2562 are absent).
Of 45 characters checked, every other imported cut-in equals its source block pixel for pixel.

**How Disgaea 1 Complete humanoids are built (2026-11, Verified offline).** The full chain is on
[functions.md](functions.md#sprite-and-animation-chain-switch-v0-2026-11-24-and-25). What it means for the art:

- **Humanoids share D5's pose set.** Across 15 humanoid bodies, all 120 distinct (texture, rectangle) cells drawn by the shared
  block 1 exist in Disgaea 5's resident block 1 with the same texture index and rectangle (cell *numbers* differ after about index
  42, D1 Complete's being one lower, so match by rectangle). Several motion IDs are the same motion key for key: D1 Complete
  2671 / 2677 is D5's 1131 / 1137 (arms-up holding), 1091 is D5's 1101 (lifter), and 2061 is D5's 1061 (the damage pose).
  Eighteen block-1 motions exist only in D1 Complete: 1071 / 1077 (this one differs from D5's 1071), 2071 / 2077, 2131 / 2137,
  2151 / 2157, 2171 / 2177, 2181 / 2187. D5 would reach them only if a slot held them.
- **Empty cells.** The humanoid bodies leave cells 1, 3, 105 to 108 and 124 empty (Dark Flonne: 1, 107, 108, 124); bodies 1030 to
  1130 leave only 124.
- **Monsters are D5-numbered but incomplete.** A monster body carries 39 to 54 motions in D5's numbering (1/7, 11/17, 1001, 1021,
  2001, ...), but lacks 40 to 42 of the 58 IDs that every vanilla D5 monster bank holds (the 1091/1097 and 1111 to 1267 reaction
  pages, for example). Carter (2560) has only 8 animations.
- **Cut-in colours.** The source has six cut-in colours per generic class (palette 0 to 5), one per rank row.
- **The three Cannons are classic sprites** (member 71, below), not Laharl's HD body; how the classic art draws is
  [Unknown](functions.md#sprite-and-animation-chain-switch-v0-2026-11-24-and-25).
- **Classic sprite pack.** `start.dat` member 71 holds 216 sprites (`u32` count, offsets, `u16` IDs, records; 4-bit or 8-bit
  textures, low nibble first; weapon IDs 9101 to 9720 and sprites 1 to 5). On PS4 the same pack is member 69.

**Sprite tips (Disgaea PC)** (**Community-reported**, pg_disatools wiki):
- Sprites are palette indices, 16 colours by default. To add an HD sprite, the easiest route is a new sheet in the
  file; then add a layer and keyframe to the animation (e.g. combat idle 16/38) and fit it to the old frames.
- Weapon placement and size are stored in each **character's** sprite, not the weapon's; HD weapons mean editing
  every character's weapon placement. Many weapon shapes are one image with different colour tables.
- With pg_disatools' mod framework, overriding `disa::<sprite id>` replaces a sprite without touching the archives.
- Faces: SD in `FACE.DAT`, HD in `NEW_FACE.DAT` (both in `START.DAT`).

## Compatibility with other games

| To | Asset | Works? | Evidence |
|---|---|---|---|
| **Disgaea 5** | D1 Complete sprites | Yes, directly: same format. Humanoids need held poses for actions D1C doesn't draw | **Community-reported**: 13 classes and 22 unique characters built (not installed) |
| **Disgaea 5** | Disgaea PC sprites | Lower resolution; only where the HD set lacks a character | Project rule |

## Sources
D5 Mod Kit development fork: `d5lib/d1cart.py`; this project's D1C reports; 2026-11-24/25 offline checks of the retail Switch files (sprite, cut-in and classic-sprite chains); [pg_disatools](https://github.com/ProgSys/pg_disatools).
