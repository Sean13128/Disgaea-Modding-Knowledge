# Disgaea 5 Complete — Assets

PC version. Where each kind of asset lives, its format, and which other games' assets fit. Archive and table names
are on [INDEX.md](INDEX.md) and [data.md](data.md).

**Evidence:** formats are **Community-reported** from D5 Mod Kit (eraser4521). The Kit reports byte-identical
round trips for anm, map and obf files (**Verified offline (Kit)**).

## Formats

| Asset | Format | Where | Read / write with |
|---|---|---|---|
| **Sprites and animation** | `anmNNNNN.lzs`: NIS LZS around an anm file (node tree, keyframes, cells, palettes) | `Data/Anime/ANM_HI_{JP,EN,FR}.dat` | D5 Mod Kit `d5lib/anm.py` |
| **Portraits / cut-ins** | anm files numbered `10000 + sprite_id` | same | same |
| **Faces and list icons** | Atlases in UI textures (face block 9017; icons `wf_unique02_48px` / `_64px`) | `Data/Start/Start_*.dat` | D5 Mod Kit |
| **Weapon sprites** | anm, numbered by `item.dat +0x56` | `ANM_HI_*.dat` | same |
| **3D models** (map objects, effects) | `.obf` container with TXF textures | `Data/Object/Object_*.dat`, `Start_*.dat/Effect.dat`, inside maps | `d5lib/obf.py`; exports OBJ |
| **UI textures** | `.txf` (16-byte big-endian header, 32 bpp); some are DDS inside YKCMP | `Start_*.dat/Texture.dat` | `d5lib/txf.py` |
| **Maps** | `mapNNNNN.lzs` = LZS around a pac of `.hex` (terrain), `.chr` (units), `.obf` (mesh), `.gop` (Geo panels): [format](../../../formats/d5-maps.md) | `Data/Map/Map_*.dat` | `d5lib/mapfile.py` |
| **Music** | `.ogg` in a `PS_FS_V1` archive (not obfuscated) | `Data/Sound_BGM/` | D5 Mod Kit, D5tools `musicer` |
| **Sound effects** | `.vag` in a `DSARC FL` archive (not obfuscated) | `Data/Sound_SE/` | D5 Mod Kit, D5tools `vagger` |
| **Voices** | `%09d.vag` in packs `voice_en/%06d.dat`, `voice_jp/…`, in a `FAFULLFS` archive. 48 kHz MS-ADPCM | `Data/Sound_Voice/`, `Data/Sound_Msg/` | `d5lib/fafs.py` |
| **Text** | `talk.dat` lines: language byte + UTF-8, per language | `Data/Script/talk.dat` | `d5lib/talk.py` |
| **Fonts** | Game fonts | `Start_*.dat` | |
| **Movies** | One opening movie per language. The executable asks for `Movie/dis5_ja.mp4` / `dis5_en.mp4`; the install listing shows `dis5_*.wmv` | `Data/Movie/` | standard players |

### Voice situations

A voice line plays for a **voice set** (`kind × 100000 + voice_id × 10 + personality`) in a **situation**; each row of
`Sound_Voice_*_0.dat` has 34 situation flags ([functions](functions.md#voices)). Situation numbers established so far
(from scripts and the D4 → D5 voice mapping; **From decomp**, not all measured in game):

| D5 situation | Role |
|---:|---|
| 0 | Attack |
| 1, 2, 6 | Special-attack shouts (the game picks per skill) |
| 4 | Elemental cast |
| 9 | Cast release / healing cast |
| 10 | Lift |
| 11 | Throw |
| 13 | Move / landing |

Weapon-skill scripts name situations {0, 1, 2, 4, 6, 11, 13}.

**Situations an imported Disgaea D2 voice can use (2026-11, **From decomp**; not heard in game).**

- **3** plays for a unit when it is dispatched: the deploy screen's update `0x14054F830` calls the voice picker for the
  dispatched unit right after the unit image appears. Seen at every dispatch in a logged run.
- **4** plays for the caster of a spell on a hostile target: all twelve Fire, Wind, Ice and Star spells (skills 1001–1003,
  1101–1103, 1201–1203, 1301–1303) call voice category 4 on the caster. Spell scripts ask only categories 4, 7, 8 and 9.
- **9** plays for a heal cast on an ally (Heal, Mega Heal, Giga Heal, 1401–1403, and two scripted casts). Support and
  buff casts (skills 1499–1506) use category 8.
- **Support attack has no voice**: when a helper joins an attack the game queues the helper's ordinary attack
  (`0x140544130`) and no voice call is made at that site.
- **Level up**: no situation was found in the reward and experience functions opened (`0x14061F640`, `0x14061F840`,
  `0x140809E10` and others), which only swap sound effects. **Unknown.**


**More situations, found by matching recordings (2026-11, Verified offline).** Many characters exist in D5 and in
Disgaea RPG or Disgaea D2 **with the same recordings**. Matching a source clip to the D5 line of the same character
(normalised waveform cross-correlation; 451 of 571 Disgaea RPG clips and 2,322 of 3,336 Disgaea D2 clip slots have a
D5 twin) shows which D5 situation flags hold the recordings that Disgaea RPG names by cue:

| D5 kind 0 situation | Disgaea RPG cue whose recordings D5 stores there |
|---|---|
| 0 (also 1, 10, 11, 13) | `voice_attack`: attack shout; D5 reuses these grunts for lift 10, throw 11, move 13 |
| 2 (also 1) | `voice_h_attack`: heavy attack shout |
| 4, 3 | `voice_magic`: spell cast |
| 9, 8, 12 | `voice_recover`: healing |
| 6 | `voice_spNN`: special-skill shout |
| 3, 4, 5, 7, 17 | `voice_turn`: taking a turn |
| 14, 15, 18 | `voice_glad`: joy (level up, victory) |
| 20, 19 | `voice_damage` |
| 21 | `voice_dead` |

Kind 3 repeats the reactions (0–2 attack tiers, 20 damage, 21 death, 31 / 33 joy, 32 pain); kind 1 situation 22 holds
the spoken (worded) lines. Which engine event plays kinds 1–3 is only partly traced. D5's own table has 231 `char.dat`
rows whose voice ID has no line at all: a voice ID without lines is how the game itself makes a silent character.
Nothing in this block has been checked by ear in the running game.

### Sprite (anm) essentials

- 60 fps keyframes. Animation IDs end in 1 (front, facing down-left) or 7 (back, facing up-left); the other two
  directions are the same frames mirrored by the engine.
- Textures are 8-bit indexed (256-colour palettes, ARGB) or 32-bit. The PC release converted the console's
  16-colour textures to 256 colours.
- Palettes are picked by colour variant: generic class rank *k* is drawn with palette *k*. Ten palettes per sprite.
- Named nodes: 1 body, 10 weapon slot, 11–17 / 111–116 attachment points (hands, effects). Named-node meanings are
  the Kit's guess.
- Humanoid generic classes share one layout of motion cells; monsters each have their own (11–36 poses) but share 57
  common animation IDs.

### Which picture the game uses

- **Every picture of a character is keyed by its `char.dat` `color_id`**, not by its character ID: face blocks, status
  face, list icons. A new row whose `color_id` has no picture of its own makes the game fall back to its vanilla lookup,
  which shows a *different* character's face (for colour 226 the icon of colour 220, Panchos). Give each new row its own
  pictures for its own colour.
- **Recruiter wheel preview** (from the game's wheel code at `0x140666920`): the centre slot plays **motion 1001** of the
  class's bank; neighbouring slots play 101 (or 1 when an entry's type byte is 1); all of them at scale 1.0117. The
  appearance set-up also binds the bank at sprite ID **+ 2** as a resident picture at motion 101; if that bank has no
  motion 101 the game falls back to its first animation (ID 0), which draws nothing. So for a new class, motion 1001 is
  what the player sees first.
- **For humanoids, motions 1001 / 1007 are stubs:** zero-length animations whose only body key attaches the class's own
  idle (6001 / 6007), which the game then loops. Keep that stub. Replacing it with a copy of the idle's frames turned the
  preview into a 100–160-tick one-shot that sits still on its first long hold and then plays too slowly.
- **Parts:** when a class's sprite is bound (`0x14014DFD0`), parts 21 and 31 start disabled; each named part gets its own
  record and palette (`0x14014ED00`). An animation ID the bank doesn't have falls back to the bank's first motion.

### Motion IDs beyond the basic set

Besides the stand, walk, attack and idle motions every class has, D5's banks carry a few bands that are easy to miss
when building a class. Measured in the vanilla banks (keys, drawings, attachment targets) by the D5 Mod Kit fork's
animation work, 2026-10; meanings marked *pose* are read from a rendered frame, not traced to a caller.

| Motion IDs | Who has them | What they are |
|---|---|---|
| 41 / 47 | Humanoids | Dash; 47 ticks, attaches to body motions 1 / 7 / 11 |
| 1001 / 1007 | Humanoids | Wheel preview stubs (zero length, attach the idle; see above) |
| 1011 / 1017 | Monsters | Wait loop, 47–59 ticks |
| 1501 / 1507 | Most banks | Attachment targets that other stubs point at (1507 a variant of 1501, 11–58 ticks) |
| 1511–1557 | Monsters | Whole-body idle variants (the creature bobbing), front on odd, back on even last digit |
| 1561–1607 | Humanoids | Stand placeholders: two keys, **no picture**, attach 7061–7107 |
| 6001 / 6007 | Humanoids | The class's own idle |
| 7001 / 7007 | Humanoids | Stance loop, 71–159 ticks, real drawings |
| 7011 / 7021 | Humanoids | Short special poses, 23 ticks |
| 7031–7081 | Humanoids | Single-frame held poses (combat stance, lean back, crouch, guard …; *pose*) |

**Where they come from:** the per-character state motions (the ones a unit enters by itself, such as `0x69`, `0x133`,
`0x191`, 400) are supplied by `0x1402EAB70`. The 70xx and 1500-band IDs arrive from **scripts**: the unit-motion
script command (`0x14016CF40`) takes a u16 motion number and selects it through `0x1402F4BA0`, which falls back to
the bank's first animation when the ID is missing. So a class without these motions doesn't crash; it shows its first
animation instead, which can look wrong in cutscenes and skills. **From decomp.**

### What plays when a unit is hit

Motion IDs are a pose number × 10 plus 1 (front) or 7 (back). On an ordinary hit the target plays one of three
four-step **reaction runs** (1171→1141, 1211→1181 or 1251→1221, counting down by 10) and then **holds 1061 / 1067**.
The pose at **21 / 27** (often called the hurt slot) is **not** played on a hit: a simulation of all 671 vanilla skills
that move a target found 1061/1067 in 664 of them and 21/27 in none, and a live memory probe on three classes read the
same. Holding a lifted unit plays **1101 / 1107**, and walking plays **11 / 17** (a class whose 11/17 only holds a stand pose visibly slides across the tiles; with a real walk there it strides, verified in game on two imported classes). So new hurt art belongs on the reaction poses and 1061/1067.
(**Verified in game**: motion probe, D5 Mod Kit fork, 2026-11-02; the skill survey with the D5 Mod Kit's skill
simulator.) The Recruiter wheel's preview motions do reach 21/27 through their attachment chain, which is how an art
change to 21/27 can affect the Hire screen.

### Weapons on sprites

Battle sprites carry **attachment nodes** for the weapon. The game draws the main weapon there (in idle poses the
sub-weapon isn't drawn):

| Node (hand / across the body) | Weapon type | Base weapon sprite |
|---|---|---|
| 11 / 111 | Sword | 3100 |
| 12 / 112 | Spear | 3200 |
| 13 / 113 | Bow | 3300 |
| 14 / 114 | Gun | 3400 |
| 15 / 115 | Axe | 3500 |
| 16 / 116 | Staff | 3600 |

- Each weapon item has its own model (`item.dat` `weapon_anm`, e.g. 3618); all models of a type share one grip point.
- The weapon is drawn with its grip on the node's origin, turned and mirrored with the node; the key's parameter picks
  the weapon animation; key flag high byte 1 or 2 shows or hides it from that key on.
- **How the game picks the slot** (**From decomp**, build B; static fixtures for all 802 weapon rows, Character Forge
  research, 2026-10). The actor keeps a 32-row attachment table (16 bytes each: enable bit, slot, resource ID,
  palette override); the writer `0x14014ED00` uses a fixed list of 17 slot numbers at `0x1408FC698`
  (1, 10–16, 110–116, 21, 31). A unit has two equipment records (actor `+0x200` / `+0x218`); `0x14014E3A0` reads the
  item's row (`item.dat`, 0x460-byte rows: weapon category at `+0x4E`, animation resource at `+0x56`) and
  `0x14014DC00` places weapon category *c* (1–7) at slot `9 + c` or, for the second bank, `109 + c`. The second bank
  is chosen when the actor's flag `+0x24C` is 0 and this is equipment record 1, or the flag is set and it's record 0.
  Selecting a weapon disables the other slots in that bank; actor `+0x1E0` can suppress the attachment entirely.
- The game's own humanoids store only their idle and special poses (6xxx, 7xxx) per character; their other motions
  are shared by every humanoid in `anm_resident`, so those can't change per character.

### Palettes and colour parts

A sprite palette is edited in **rows of 16 colours**; the game's own sprites keep one body part per row (skin, hair,
clothes …). Row 0 holds small details. The game's Change Colors menu picks among the sprite's palettes (Normal, Extra
1–3); D5 Mod Kit adds slots of its own through a runtime hook.

**How many palettes a sprite can have:** the palette count is one `u32` in the anm header (`+0x0C`), so the format allows
many: generic class bodies carry **10**, some system anm files over 1,000. The practical limit is the colour menu, which
offers 2–10 choices (**Verified offline**: read from the files; the menu limit from the code).

## Compatibility with other games

What's known about bringing other games' assets into D5. Most of it comes from the import work described in
[mods/](mods/) and this project's research.

| From | Asset | Fits D5? | What it takes | Evidence |
|---|---|---|---|---|
| **Disgaea RPG** | Generic class and monster sprites | Yes, closely | D5's generics and their Disgaea RPG twins are the same drawings at the same size. D5 Mod Kit maps each D5 cell to the DRPG atlas (94–100% agreement between twins) | **Verified offline (Kit)**: a D5 class rebuilt from its DRPG twin matches frame by frame (median outline overlap 1.000) |
| **Disgaea RPG** | Character outfits | Yes | D5 Mod Kit ships 175 outfits for 26 characters built from DRPG art | **Community-reported** (Kit README) |
| **Disgaea 4** | Sprites, palettes, voices | Yes | Both are HD 2D sprite games. D4's ten palettes per class map to D5's ten | **Community-reported** (project's D4 import, installed and playtested 2026-10) |
| **Disgaea 1 Complete** | Sprites | Yes | HD like D4 | **Community-reported** (project research) |
| **Disgaea D2** | Sprites | Yes | HD | **Community-reported** (project research; one class and one monster installed as a test) |
| **Disgaea 2, Disgaea 3** | Sprites | Needs scaling | Low-resolution sprites. Recommended: whole-number pixel scaling to D5 figure height, not AI upscaling, which invents detail and blurs the palette rows | **Unknown** (planned, not done) |
| **Disgaea 6, 7** | 3D models | No | D6 and D7 use 3D character models; D5 uses 2D sprites | — |
| Any | Skill animations | Partly | Skills usually map to an existing D5 skill's animation. Importing another game's special-attack animations is still research | **Community-reported** (project research) |

## Open questions

- `.obf` sections 2–6 (hierarchy, motions) and several texture formats (`0x0b`/`0x0c`/`0x0d`) aren't fully understood.
- Several anm node and key flags are unknown.
- What do D2/D3 sprites look like after integer scaling next to D5's?
- Movies: the executable names `.mp4` files, but the install has `.wmv`. Which does it actually play?

## Sources

- D5 Mod Kit by eraser4521: [FORMATS.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/FORMATS.md), [ANM.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/ANM.md), [MAPS_AND_OBJECTS.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/MAPS_AND_OBJECTS.md), [VOICES.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/VOICES.md), [CLASSES.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/CLASSES.md), [MONSTERS.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/MONSTERS.md), [OUTFITS.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/OUTFITS.md), [WEAPON_GRIPS.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/WEAPON_GRIPS.md), [COLORS.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/COLORS.md), [README](https://github.com/eraser4521/D5ModKit).
- [D5tools](https://github.com/BTAxis/D5tools) by BTAxis.
- This project's research (source-pack program overview and prior-art map, 2026-10).
