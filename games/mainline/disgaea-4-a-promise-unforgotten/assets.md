# Disgaea 4 — Assets

**Evidence:** **Community-reported** from the D4 → D5 import work (D5 Mod Kit development fork) unless noted.

## Formats

| Asset | Format | Where | Notes |
|---|---|---|---|
| **Sprites** | `anmNNNNN.lzs`: NIS LZS around an anm file | `Data/DATA/ANM_HI.dat` (NSAC) | Same layout as D5's anm (32-byte header, blocks with a 0x44 header and ten tables) except node rows are **16 bytes** instead of 20. Palettes are 256 colours. Humanoid bodies hold 4 to 10 palettes (D4 reads one by the unit's colour index; see [below](#how-the-game-draws-a-body-2026-11-offline)) |
| **Illustrations / cut-ins** | anm groups `10000 + body (+ colour byte)` | `ANM_HI.dat` | Plain white background (the importer makes it transparent). 740 members across the cast and classes; each group's first cell is the picture. Which member a unit uses: [rule below](#faces-and-cut-ins-which-picture-a-character-gets-2026-11-offline) |
| **Face icons** | `swf<NNNN>`: 256 × 256 BC7 textures (`NMPLTEX1` wrapper, YKCMP_V1 zlib) | `Data/DATA/WF.dat` | 529 faces. Name is the picture's resource ID zero-padded to four digits (`swf0070`, `swf1220`). The number a character asks for is not always its colour ID: [rule below](#faces-and-cut-ins-which-picture-a-character-gets-2026-11-offline) |
| **Effects** | OBF v2 models with legacy `nismultitexform` textures | `Data/DATA/<LANG>/EFFECT.dat`, banks `effect%03d.lzs` | Textures are indexed 4-bit (low nibble first), indexed 8-bit or BGRA 32-bit, some with several palettes |
| **Voices** <a id="voices"></a> | `.nlsd`: 28-byte header + RIFF MS-ADPCM mono, sample rate per line (about 22 kHz) | `Data/SOUND/<LANG>/voice.dat` (FAFS), members `en/<group>/voice_<line>.nlsd` | Line table in `SoundData_Voice_<LANG>.dat` ([data.md](data.md#sound-tables)) |
| **Music, sound effects** | Archives | `Data/SOUND/bgm.dat`, `se.dat` | Not documented |
| **Maps, objects** | Archives | `MAP.dat`, `MAPDATA.dat`, `OBJ.dat` | Not documented |
| **PS3 / Vita** | TXF (PS3), NMT (Vita), NSAC, NIS LZS | | Readable with [Scarlet](https://github.com/xdanieldzd/Scarlet); sprites and TXF with [NisAnim](https://github.com/xdanieldzd/NisAnim) (PS3) |

Timing: one animation tick per main-loop iteration, which is 1/60 s when the game is presented at 60 Hz, as in D5.
(**Corrected 2026-11-25:** an earlier version said animations "run at 60 ticks per second" as a fixed rate. The tick
counter has no wall-clock divisor of its own; see [How the game draws a body](#how-the-game-draws-a-body-2026-11-offline).)

**Finding a character's sprite:** use the `char.dat` **`sprite`** column, not the character or class ID. Most rows
match (class 1190 → `anm01190`), but not all: Des X (ID 180) is drawn from `anm00050` and God's Part (230) from
`anm02040`; reading `anm00180` / `anm00230` finds only 3 motions instead of 76 and 62. Generic humanoids also share a
common motion block (`anm00001`, the block whose ID is 1: 248 motions) that their own bank adds to. Toro (240) and Kuro (250)
have only seven motions in D4's files (0, 1, 3, 7, 11, 17, 21) and Politan (610) three (0, 1, 7), so those bodies
have no attack, skill or lift motion of their own (**Verified offline**, 2026-11-24). (**Verified offline**: read from the files, D5 Mod Kit fork animation work, 2026-10.)

## Compatibility with other games

| To | Asset | Works? | What it takes | Evidence |
|---|---|---|---|---|
| **Disgaea 5** | Class and character sprites | Yes | Convert the 16-byte node rows; put D4's complete poses into D5's template animations. D5 animations sometimes draw body parts on separate tracks, so only one main body track is kept per animation | **Community-reported**: 45 generic classes and 26 unique characters converted; installed and playtested 2026-10 |
| **Disgaea 5** | Palettes | Yes | D4's ten palettes map colour for colour onto D5's ten | same |
| **Disgaea 5** | Idle animation timing and weapon grip | Yes | Re-clock D4's idle frames (the Armor Knight's loop is 153 ticks) | **Verified offline (Kit)** |
| **Disgaea 5** | Voices | Yes, adapted | Convert to D5's format; map D4's voice categories to D5's situations (only six mapped so far) | **Community-reported** |
| **Disgaea 5** | Effects | Partly | Outer model converts; textures need re-encoding to D5's TXF. Rendering not yet accepted | **Verified offline (Kit)** for pixels only |
| **Disgaea 5** | Skill animations | Partly (2026-11) | 77 of 80 adapted specials convert to D5 scenes offline, with audio, draw layers and some backdrops left out and 98 prop or event-sprite actors drawn invisible. D4 scenes use 42 commands whose operands differ from D5's, and some mechanics (Lift Catch's carry) need D5 equivalents. See [functions.md](functions.md#d4-special-attack-scenes-as-a-source-2026-11) | **Verified offline**; 6 of 10 cast attacks played and matched their timelines (**Verified in game**, in D5 build B) |

**Where faces and cut-ins come from (2026-11, Verified offline).** A full read of `WF.dat` and `ANM_HI.dat` gives 529
faces and 740 cut-in members with no decryption step, and matches an earlier independent extraction pixel for pixel. A
named character's screen art is not always its own row: rows 180 and 220 use the **faces** of resource IDs 54 and 74 (their cut-ins are members 10050 and 10070), and
an NPC row uses its source row's colour key for the **cut-in** (5301 uses 1154, 5305 uses 1014, 5388 uses 1184).
**Corrected 2026-11-25:** an earlier version said face *and* cut-in IDs come from the resource ID in the picture's name,
not from the character row. The game's own face rule differs from the cut-in rule; see the next section.

## Faces and cut-ins: which picture a character gets (2026-11, offline)

**Evidence:** **Verified offline.** Both rules were reproduced by running the game's own functions in an emulator over
the real `char.dat` (this wiki's offline checks of the retail files); the draw routine that consumes the face
number was not run, so that the number is the `swf` file number rests on the loader. Disgaea4pc.exe, Steam build 6150765.

| Picture | Number the game asks for | Function |
|---|---|---|
| **Face** (`WF.dat`, `swf<N>`) | `-1` (none) for character ID 0. Otherwise the row's **colour ID** (`+0x1A0`) when `0x1401B1430(id)` returns 1 (IDs below 1000 outside a few ranges, and `0x1518` to `0x157B` except `0x1530` to `0x1539`), and the **colour ID rounded down to tens** when it returns 0 (class rows, all six rank rows, NPC rows from 5301) | `0x14047BFB0`, reached through thunks `0x1401B9BE0` / `0x1401B9BF0` |
| **Cut-in** (`ANM_HI.dat`, `anm<NNNNN>`) | `10000 + sprite`, plus the unit's colour byte (`+0x115C`, copied from the row's `+0x294`) **only** when `0x1401B1430(id)` returns 0 (class and NPC rows), and again for ID `0x1519`. Des X (sprite 50) therefore uses `anm10050` and Nemo `anm10070`, not the members named for their colour IDs 54 and 74 | `0x1401B0A50` |

What follows from it:

- **All six rank rows of a class share the class face** (Armor Knight rank 1, ID 1081, shows `swf1080`; Medic 1241 to 1245 show `swf1240`, which exists; NPCs borrow their class face: Chief 1154 shows `swf1150`, Kojiro 1142 `swf1140`, Arsailles 2144 `swf2140`). The 154,613 id/colour cases tried had 0 mismatches.
- **Cut-ins are per colour**, so a class has six cut-in members (colour 0 to 5), one per rank row. The cut-in file holds one 512 × 1920 indexed page, one palette and motion 8000, which draws cell 1 (390 × 1248 at offset 1,1).
- `WF.dat` does hold recoloured faces for rank colour IDs (`swf1081` and so on; `swf1080` and `swf1081` differ by about 6 of 255 mean RGB), but no code path asks for them for these rows. An earlier reading that Medic and Necromancer ranks have no face was wrong: the game never requests those files.
- There is no portrait asset besides the face and the cut-in.

## How the game draws a body (2026-11, offline)

**Evidence:** **Verified offline** unless marked **From decomp** (read in code, not run). Where a statement rests on a
rule the offline checks could not run, it says so. Disgaea4pc.exe, Steam build 6150765; D4's animation module is the same
code family as D5's ([anm](../../../formats/anm.md)), so D5's notes are the best guide.

**Containers.** `ANM_HI.dat` is an NSAC v2 archive of 2,084 members named `anm<NNNNN>.lzs`, each an NIS LZS stream that
decodes to an anm file. The language overlays `ANM_HI_<LANG>.dat` (EN, FR, KO, TW, CN; none for JP) hold 120 to 125
members that are also in the base file, and contain no in-scope body, face or cut-in. `WF.dat` holds 529 members. The
database archive also holds `AnimeInfo.dat` (49 large multi-motion sheets, IDs 21001 to 29603; purpose **Unknown**) and
`TextureInfo.dat`.

**Body, pages and palettes.**

| Fact | Detail |
|---|---|
| Which file | `anm<sprite>` where sprite is the row's `+0x196` plus the unit's own offset (`+0x1DC`) |
| Humanoid bodies | One block with 3 to 7 motions, 9 or 10 texture pages of 512 × 512, 4 to 10 palettes |
| Monster bodies | 60 to 83 motions of their own. Toro and Kuro: 7 motions, 1 page, 1 palette. Des X (sprite 50): 11 pages of 32 × 32. Pringer X (210): one 512 × 560 page |
| Pixels | 8-bit palette indices (format byte `0x09` in a 16-byte image header `{format[4], width u16, height u16, u32, data offset}`); palettes are 256 × 4 bytes |
| Palette choice | The unit's colour variant byte when it is 0 or more and below the record's palette count; otherwise the cell's own palette field (index past the count falls back to 0). The row's `+0x294` equals `colour ID - sprite` for all 134 in-scope rows, and is always below the palette count. Only three cells (in sprites 460, 1190 and 2120) have a cell palette other than 0 |
| Alpha | Palette first bytes seen are 0, 127, 128 and 255; how the shader treats them is **Unknown** |

**Humanoids are two files.** A humanoid unit's owner is the shared record `anm00001` (the block with ID 1, 248 motions) with
the body file bound at key 1. A second set, `anm00000` block 2 (128 motions), exists but its cells do not fit the HD bodies;
an option byte (`0x1F`) picks which, and every start-up path sets it to the first (**From decomp**; whether a saved
configuration can restore the other value was not traced). A node that carries the "named node" flag (`0x100`) takes its
record from the binding table; if the binding is missing or disabled nothing is drawn. Key 21 is bound only for sprites 270 to 279
and key 31 never, so those nodes draw nothing on other bodies (**From decomp**). The idle stubs `1001`, `1007`, `11001` and
`11007` in the shared block are zero-length: each holds one named node (ID 1) whose key names the body's own idle `6001` / `6007`,
at an offset of (-3, -9) in `1001` and `11001`. A motion ID the owner lacks plays the record's first motion (index 0); for
Toro and Kuro that is an empty cell.

**Anm layout facts the draw run confirmed** (the game's draw routine `0x140439690` was run on every motion of the 42 in-scope
bodies and on the shared set bound to 26 humanoid bodies: 98,976 comparisons, 0 mismatches):

- Node rows are 16 bytes. Cell: 18 bytes `{texture, palette, unk, flags, x, y, w, h, unk8}`; flags low byte non-zero flips X, high
  byte flips Y; a cell with zero width or height queues nothing. Transform: 16 bytes `{x i16, y i16, pivot u16, u16, scale-x
  i16 (percent), scale-y i16, rotation i32 (degrees)}`. Colour: 20 bytes, five RGBA vertex colours (128 = neutral).
- Matrix = parent × translate × rotate × scale. A key whose flag low byte is non-zero blends toward the next key linearly
  (position, scale, rotation; pivots truncated to integers); other keys step. A node with no current key still draws its children.
- Control keys on the root: low flag byte 1 loop start, 2 jump back to the previous flag-1 key, 3 conditional back, 4 hold unless
  released, 5 end or hold for a duration. Time is Q16 and each step adds at most one tick.
- **Mesh nodes.** The word at node `+4` is 1 for 45 of 5,582 nodes in the 44 in-scope bodies (never another value but 0). Such a
  node draws its cell on four stored vertices instead of a pivoted rectangle: its transform's pivot index names a group of ten
  points in the pivot table; the first four are the vertices (TL, TR, BR, BL, no pivot subtraction), the fifth a centre, the rest
  0, and a cell flip negates the coordinates. Death 2120, Hugo 170, Des X (sprite 50) and Prinny Kurtis 190 use them in 37 motions.
  D5 keeps the same flag at node `+8` and its renderer (`0x1402F7C60`) reads five points the same way, so the D5 bank format holds
  a D4 mesh node as is (**From decomp**, D5 side; 28,040 of 31,835 vanilla D5 vertex groups use the same cyclic order).
- **Mirror.** A negative motion ID negates the owner's X scale (1/256 fixed point); a flip byte negates Y.
- **Attachments.** A key whose flag high byte is 1 spawns an attachment (2 despawns it) from `params[key.param]` = {motion ID, start,
  duration; -1 = endless} (**From decomp**; not run).

**Draw order** (**From decomp**; the queue and sort were read, the executor was not run). Each node's parts are queued in
post-order: children before their node, siblings in file order, so a later sibling is drawn over an earlier one. The queue is kept
sorted ascending by the cell's priority byte (`unk8` low byte; 0 on every drawn cell checked), stable among equals. Each entry
becomes a packet keyed by (layer, flag, submission counter) and the frame's packet array is sorted ascending and consumed in
order; the per-node z value is never read. D5's renderer paints a figure's sibling parts in the opposite order, so bodies whose
sibling parts overlap can show a different part on top. The depth, stencil and blend state of the final draw call was not
traced (**Unknown**). **Tick:** each main-loop iteration steps every actor once (a paused script freezes actors); the 16,666
microsecond constant in the loop only feeds a lag counter that skips drawing, never the actor clock. So one tick is 1/60 s only
when the game is presented at 60 Hz (**Unknown**: the present call and sync interval were not identified).

**Weapon mounts.** An equipped weapon is bound to exactly one key of its hand: main hand `{fist 10, sword 11, spear 12, bow 13,
gun 14, axe 15, staff 16}`, second hand 110 to 116 (key 17 is never bound); every other key of that hand is disabled. The
weapon's position comes from named nodes in the shared motions, so a motion that has no node for a key shows no weapon
(node 14 carries a weapon in 76 of the 248 shared motions, the other types in 175). **Example, motion 4047:** the shared
motion has no mount-14 node, so a gun user shows no weapon in it in D4; its two mount-114 tracks remain. A converted bank
that supplies mount 14 would be a D5 adaptation (**Verified offline**; **From decomp** for the binding table).

**Bodies D4 never finished.** Arsailles' bank (`anm00833`) is the D4 Succubus body (sprite 2140: 10 pages of 512 × 512, 67
cells, 64 animations, 10 palettes). D4 has no motion 153, so D5's Succubus motion 1531 has no D4 counterpart; that D5
motion draws 38 cells from a 1024 × 1024 page D4's atlas lacks (**Verified offline**).

## Open questions

- Map, object, music and sound-effect formats.
- What the voice categories mean in game.
- Whether D4's final draw call blends or depth-tests overlapping sibling parts (the packet order is read; the executor is not).
- The wall-clock length of a tick (present call and sync interval).
- The alpha meaning of palette entries 127 and 128, and the sampler.
- Which archive wins when a language overlay and the base file hold the same member (none of the in-scope bodies is in an overlay).
- How the Vita and PS3 releases differ from the PC files described here (the Vita copy was not opened).

## Sources

- 2026-11 faces, cut-ins and scenes: D5 Mod Kit development fork, `docs/D4_IMPORT.md` (face and cut-in sources) and `docs/D4_SCRIPTS.md`.
- D5 Mod Kit development fork (Sean13128): `docs/D4_IMPORT.md`, `docs/D4_EFFECTS.md`, `docs/D4_BODY_IMPORT.md`, `docs/D4_VOICES.md`, `d5lib/d4source.py`.
- 2026-11-24/25 source-format work (face and cut-in rules, body draw chain, mesh nodes, draw order, weapon mounts): this wiki's offline checks of the retail Complete+ PC files, including the game's own functions run in an emulator, and the D5 Mod Kit development fork's `docs/D4_MOTIONS.md` and `docs/D4_IMPORT.md`.
- This project's D4 → D5 porting handoff (2026-10).
- [Scarlet](https://github.com/xdanieldzd/Scarlet), [NisAnim](https://github.com/xdanieldzd/NisAnim) by xdanieldzd.
