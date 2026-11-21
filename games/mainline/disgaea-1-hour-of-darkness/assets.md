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
1010, palettes 0–5 → cut-ins 1011–1016; Laharl 10 → 11, Etna 20 → 21). Exception: Dark Flonne (body 30, palette 2)
uses block 32, her own drawing, not 33; the game's code has a hard-coded character list containing her ID (3060)
that probably explains it (**From decomp**, not verified) (**Verified offline** for the rule: checked on every block).

**Cut-in gaps (2026-11, Verified offline):** Carter (body 2560) has no authored cut-in (blocks 2561 and 2562 are absent).
Of 45 characters checked, every other imported cut-in equals its source block pixel for pixel.

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
D5 Mod Kit development fork (Sean13128): `d5lib/d1cart.py`; this project's D1C reports; [pg_disatools](https://github.com/ProgSys/pg_disatools).
