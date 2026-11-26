# Motion ids and what they mean, across games

Animation ids in [anm](anm.md) files are plain numbers. This page collects what is known about what each id *is* (walk, idle,
hit reaction, a weapon swing) and how that carries from one game to another. It exists because the numbering looks shared but
is not, and a converter that assumes it is will pick the wrong pose.

**Used by:** Disgaea 5, Disgaea 4, Disgaea D2, Disgaea 1 Complete (anm files); Disgaea RPG (Unity, names only).

Evidence labels are on every row group. Names are plain-word labels chosen by this project; where the Disgaea RPG developers
named the same drawing, that is said so. Nothing here reproduces game text or art.

## Are the motions named anywhere?

**Verified offline** (searched 2026-11-25): **no.** None of the anm files, the Disgaea 5 animation-table database, Disgaea 4's
animation-info file, the executable string tables of Disgaea 5, 4 or 1 Complete, or the function names recovered from the Disgaea 5 executable hold a table of pose names.

- anm animations carry a numeric id (u16) and no string table ([anm](anm.md)).
- Disgaea 5's `Anime_TableDataBase.dat` has 284 rows of three integers (row id, a motion base such as 1000, 1010, 1050,
  1500, 2070 or 4000, and a small parameter), grouped per body and closed by a `-1, -1` row. It lists which motion bases a
  body offers; it names nothing.
- Disgaea 4's `AnimeInfo.dat` holds 49 large multi-motion sheets whose purpose is **Unknown**.
- The only named animations are **Disgaea RPG's**: its sprite atlases name each drawing (stand, walk, damage, squat, dash,
  attack by weapon family, launch, lift, roll ...) and its animator clips name the situations (walk, wait, damage, dead, jump,
  lift, throw, magic start and end, attacks). RPG draws the same humanoid poses as Disgaea 5.

## How an id is built

**From decomp** (D5 and D4 code; D1 Complete from its state table): a motion id is **pose number × 10 + facing digit**. The
front view ends in 1, the back view in 7 (front + 6); the engine mirrors the other directions
(see [anm](anm.md#variants-by-game)). So `1001` is pose 100 seen from the front and `1007` the same from behind.

Pose numbers the game code itself writes (**From decomp**; D4 re-checked in its executable, D5 from the decomp, D1 Complete from its own state table):

| Pose | Meaning | Where seen |
|---|---|---|
| 100 | Idle | D5, D4 |
| 1 | Walk (set while a unit moves) | D5, D4 |
| 10 | "Ready" while an action sequence runs | D4 |
| 106 | A unit being carried | D5, D4 |
| 110 | The lifter | D5, D4 |
| 113 | Holding | D5, D4 |
| 114 | Dangling carried unit (alias of 106) | D5 |
| 115 | Being hit (field) | D5 |
| 118 | Hit reaction, about 60 frames | D5 |

Weapon swings are one id per weapon family, with the same family order in D4 and D5 (**From decomp** for D4, via the script
that starts the attack event; **Verified offline** for D5 cells): 2001 fist, 2011 sword, 2021 spear, 2031 bow, 2041 gun,
2051 axe, 2061 staff or arms-up pose. 3001-3061 repeats the set in D5 (the second attack of a pair).

## The same id is not the same motion

**Verified offline.** Of 36 ids shared by D1 Complete and D4 with three or more poses, 8 have identical key timing and cell
pattern; D5 against D4, 5 of 60; D5 against DD2, 1 of 60. Examples: D1 Complete 1071 is "one arm up" (a cast pose), while D5
1071 is an alias of the damage pose; D1 Complete 1091 and D5 1091 and D5 1101 are three different things (D1 Complete's 1091
equals D5's lifter 1101). There is therefore no single table of names that fits all games. Any conversion has to go through
the drawing, not the number.

## The bridge: shared humanoid drawings

The names of Disgaea RPG can be carried to D5 and D1 Complete because they **draw the same poses**:

- **D5 and D1 Complete** (**Verified offline**): D1 Complete draws an HD humanoid with D5's own animation module. Its shared
  block 1 is D5's resident block 1 renumbered: across the 15 humanoid D1 Complete packs studied, all 120 distinct (texture,
  rectangle) cells it draws exist in D5's block 1 with the same rectangle. Cell *numbers* differ after about index 42, so
  match by rectangle. Of 105 D1 Complete motions, 93 equal a D5 resident motion key for key.
- **D5 and Disgaea RPG** (**Verified offline**): the RPG humanoid atlas holds the same drawings. A calibration over 18 vanilla
  D5 generic classes against their RPG twins gave exact pixel matches on most cells, and RPG's 48 front and 43 back "standard
  humanoid" rectangles equal named atlas sprites for 127 of 170 RPG looks (75%); the rest are monsters and other layouts.
- **D4 and DD2** (**Verified offline**): **no bridge.** They draw humanoids with their own art and cell sizes (a walk cell of
  96 × 176 against 112 × 192): 1 of 140 distinct D4 cells equals a D5 cell, 1 of 544 for DD2. A silhouette matcher calibrated on
  D1 Complete (where the answer is known) found only 31% exact and 43% same-family, too weak to name anything.
- **Monsters** have no bridge in any game: each body draws its own cells.

## What is established

Confidence grades used here: **confirmed by code** (game code requests that pose for that game, and the cells agree where the
bridge exists); **consistent** (cells, an RPG clip and structure agree); **single source** (one of them only).

| Motion (front id) | Draws | D5 | D1 Complete | D4 |
|---|---|---|---|---|
| 1 | Stand pose | confirmed by code | single source | confirmed by code (pose 0) |
| 11 | Walk cycle (six poses, back to the first) | confirmed by code | consistent | confirmed by code |
| 21, 1011 | Also a walk cycle (not a hurt, not an alternate idle) | consistent | consistent | not established |
| 31 | One held walk pose (spawn) | single source | not present | not established |
| 41 | Walk poses in reverse order | single source | not present | not established |
| 1001 | Idle loop (the body's own wait cells) | confirmed by code (alias of 6001) | confirmed by code | confirmed by code |
| 1021, 1031 | A hop: stand, squat, dash (or walk pose), squat, stand. Not damage | consistent | consistent | 1031 confirmed by code (move hop), 1021 structural only |
| 1041 | Taking out a weapon | single source | single source | single source |
| 1051 | Jump | single source | single source | confirmed by code (level-up reaction) |
| 1061 | Damage (limp) pose, also the carried pose | confirmed by code | consistent | confirmed by code |
| 1071-1081, 1141-1261 | Aliases of 1061 in D5, so most D5 hit ids play the damage cell | single source / confirmed by code (1141, 1151, 1181) | 1071: one arm up | hit-reaction helper sets A, B, C (our letters) |
| 1101 | Lifter, arms up | confirmed by code | (1091) single source | confirmed by code |
| 1131 | Holding, arms up | confirmed by code | (2671) consistent | confirmed by code |
| 2001-2061 | Weapon swings by family | consistent (fist, spear, bow, gun) / single source (sword, axe, 2061) | 1501 fist; 2511-2571 other weapons | confirmed by code |
| 1601 / 2061 | Launch then arms up | single source | single source | not established |

In D1 Complete the numbers in the 2001-2071 range are the **weapon-carrying variants of states 0-6** (+1000), not attacks; its
real attacks are 1501 (unarmed) and 2511-2571 (weapon types).

Open: hit reactions beyond the damage cell, throw, victory, cast, per-weapon attacks in D4 and DD2, and every monster body.
Settling D4 and DD2 needs the D4 script events traced to the pose numbers they set, or a pose-level comparison against RPG
by eye.

## Method

1. Take the **drawings** a motion plays (the cells under its keys, in key order), not the id.
2. Match cells across games by **rectangle** where a bridge exists (above), by pixel comparison otherwise.
3. Name the cell sequence with the RPG sprite names; check it against the RPG clip that plays the same sprites in the same order.
4. Check any pose number the game writes in code (decomp) against the sequence.
5. Record each name's evidence separately so a reader can tell "named by code" from "named by the look of the cells".

A table built by pairing a conversion tool's sheet-row assignments with ids is a *conversion choice*, not a claim about the
game: 16 of the D5 and 13 of the D1 Complete rows in one such table disagreed with the cells.

## Aliases (D5 resident block 1)

**Verified offline.** Many motions have no cell of their own; their first key plays another motion: 1001 plays 6001 (the body's
own idle), 1011 plays 11 (walk), 1071, 1081 and 1141-1261 play 1061, 1501-series ids play 7001. This agrees with live
observation that hits select ids in the 1181-1217 range and then settle on 1061.

## Sources

Disgaea 5 Steam build B, Disgaea 1 Complete Switch v0, Disgaea 4 and Disgaea RPG data read with the D5 Mod Kit development
fork's readers (a fork of [eraser4521's D5 Mod Kit](https://github.com/eraser4521/D5ModKit), MIT); that fork's `docs/ANM.md` and
`docs/IMPORT_GATES.md`; this project's offline research,
2026-11-25.
