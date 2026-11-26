# Disgaea D2: A Brighter Darkness — Assets

| Asset | Format | Where |
|---|---|---|
| Sprites | `anmNNNNN.lzs`: NIS LZS around an anm file with the same ten tables as D5's (row sizes 4, 16, 12, 8, 12, 18, 16, 4, 20, 4: 16-byte node rows) | `ANM_HI*.dat` |
| Face textures | `txf\0` + LZS around a TXF: format 0, ARGB8888, big-endian width/height/depth | |
| Illustrations | anm resources (e.g. `anm10030.lzs`) | `ANM_HI*.dat` |
| Voices | `SndPakVoice` banks | `SOUND/` |

## Sprite banks and what Disgaea 5 can hold (2026-11-16, Verified offline)

Of the 2,301 D2 sprite members the D5 Mod Kit development fork reads, 2,249 come out the same as before its reader fix,
4 changed (the 16-byte effect cell rows are now read 16 apart, as they are stored), and 48 hold **DXT5-compressed
sheets** (1 byte per pixel). Palettes in D2 banks are kept whole (256 × 4 entries).

A scan of all 2,347 clean Disgaea 5 banks finds only texture formats 9 (3,402) and 0 (3,864) and only 256 × 1 ARGB32
palettes (11,849). **Whether Disgaea 5 loads a 256 × 4 palette or a DXT5 sheet is Unknown** (not tested, and no source
says it does); the converter therefore cuts a palette to its first 256 colours and refuses a DXT5 texture with a
clear message, so those 48 members need decoding to indexed or ARGB32 first. The importers' class hybrids were
byte-identical before and after the change (10 checked).

## Sprite banks for a character

Reading status as on [functions.md](functions.md#sprite-animation-and-cut-in-code-140-addresses-2026-11-24-to-11-25):
**Verified offline** where proven, **From decomp** (reading) otherwise. Facts from the 1.40 executable and `START_4.dat`.

- **Which bank:** a character's sprite file is `anm<body + offset>.lzs` (name format `anm%05d.lzs`). The offset is 0 for
  most callers, 1 for some scene or state values, 4 for an optional extra bank; offsets 2 and 3 are never requested
  through the body loader. A character's rank rows share a body bank (438 rows have a body different from their ID).
  Packs are searched last-registered first, so `ANM_HI_6` beats `ANM_HI_1` beats the base `ANM_HI.dat`: 218 of 2,056
  names occur in more than one pack and 151 of those differ in content (winners: `ANM_HI_4` 53, `_1` 30, `_6` 20,
  `_5` 17, `_2` 16, `_3` 15). For example Lilliel's and Alex's art differ between the base copy and the winner in
  every file compared. All 2,056 members parse.
- **Row type byte (`char.dat +0x28F`):** 0 humanoid (242 rows), 1 monster (237), 2 prop (79). A humanoid row uses
  the **shared humanoid record**, `anm00001` block 1 (248 motions, ids such as 0, 1, 2, 4, 7, 8, 11 and 1001 upward),
  and its own bank supplies only the pose parts (for Barbara, bank 100 holds motions 0, 6001 and 6007 and nine 512 × 512
  sheets). A monster row keeps its own motion set (Lantern 61 motions, Pringer Z 65, Baal Slave 66). Props use a
  third path that isn't traced. (**From decomp**, reading.)
- **Why "missing" motions:** for each humanoid pack, 109 to 113 of the 247 shared motion IDs (for example 2071, 2077,
  3071, 3077, 5031 and 10001 upward) exist in the source with cell keys and named parts, but the pack's bank has no clip
  for them. That is a gap in the pack's own bank, not in the reader. What the game does when a motion ID is absent is
  not traced (Disgaea 4 falls back to the first motion).
- **Motion IDs** are base × 10 + a facing digit (1, 2, 4, 7, 8; negative for mirrored); the digit rule is proven for
  the two selector forms on functions.md, the meaning of each base is an inference. Same ID does not mean same motion
  across games (identical timing and cell pattern for 1 of 60 D5/D2 pairs). Only 1 of 544 distinct D2 humanoid cells
  equals a Disgaea 5 cell, so, unlike Disgaea 1 Complete, there is no cell-for-cell bridge to name D2 poses from
  Disgaea RPG's sprite names (see [Disgaea RPG assets](../disgaea-rpg/assets.md#sprite-names)).
- **Container and cell format** (**Verified offline**, all 2,056 members parse): big-endian 32-byte header, blocks with
  a `0x44` header and ten tables, image descriptors of 12 bytes, image data aligned to `0x80`. Texture format 9 is
  8-bit indexed (linear), palettes are ARGB32 (Barbara has 4, Virunga 9, Lantern 9, Shark Dragon 10). Key rows are 12
  bytes, cells 18 (16 in four effect banks). Key control byte `+2`: 1 loop start, 2 loop back, 5 end; byte `+3` = 1
  spawns the animation the key's parameter names (body motion 6001 loops 0 to 150). The meaning of the cell bytes
  (`+4` palette bank, `+5` blend, `+6`/`+7` flips, `+16` depth group) is a **reading** from the draw code, not run.
- **Palette choice** (**From decomp**, reading): per drawn cell the index is the actor's override (set from row
  `+0x28B`, class ranks 0 to 5, or the unit's colour byte `+0x1183`), otherwise the cell's own palette; an index at or
  past the block's palette count becomes 0. Virunga (palette 5) is the one in-scope unique whose row selects a
  non-zero palette.

### Palette counts and Disgaea 5

D2 banks store the palette-info count at row `+4` as a big-endian 32-bit value (`00 00 01 00` for 256 colours).
Disgaea 5 reads the same four bytes in its own order, as 16,777,216. D5's palette copy has no clamp, so a D2 bank
dropped unchanged into a Disgaea 5 pack overruns a fixed 256-colour buffer and crashes the game on the unit's first
action (details: [functions.md](functions.md#special-attack-scenes)). Rewriting the count to 256 is the whole fix
(**Verified in game**, D5 exe `f789ddb7`, control crashed 3 of 3, fixed packs ran on ten classes). A scan of the clean
Disgaea 5 banks found every count between 16 and 256.

## Cut-ins and face icons

**Verified offline** (cut-in rule proven against the recompiled game function over 3,906 cases) unless marked.

- **Cut-in strip:** bank `anm<10000 + body>` for unique IDs up to 999, and `anm<10000 + body + palette byte>` for every
  other ID (class rows and IDs above 999). One 128 × 544 texture; the game draws a cell **125 × 544 at x = 1**
  (motion 8000), so the border columns are never drawn. The bank holds motions 0, 8000, 8010, 8011, 8020, 8030, 8031
  and 8100 (expressions). Class strips exist per rank (`12020` to `12025`). A missing file is skipped. Virunga's strip is
  `10140` (no palette added for a unique). 23 rows have no strip, all of them dummies.
- **Proportions:** 125 × 544 is aspect 0.230. Disgaea 5's cut-in textures are 336 × 1080 (0.311) and fill the whole
  texture (opaque on all 14 sampled); Disgaea RPG's cut-in is 260 × 836 and Disgaea 4's cell 390 × 1248, which already
  match D5's proportions. A D2 strip therefore needs fitting, not a plain resize, to avoid a 35% horizontal stretch.
  How the D5 screens draw a narrower strip in a 336-wide texture hasn't been looked at in game (**Unknown**).
- **Face icon:** row `+0x19E` is a selector; the game picks an atlas (`0x149C88`) and a row from tables in the
  executable (`0x149CF0` to `0x149E38`), the column is the rank, and the cell is 96 × 96 (`0x149E80`, `0x15AD84`)
  (**From decomp**, reading). The atlas files are `wf_unique1`, `wf_unique2`, `wf_human`, `wf_monster` and
  `wf_other` (`.lzs` in `START.dat`, `txf` ARGB32, 384 or 864 wide); that the selector binds to these names is an
  inference (the names appear in neither executable).
- **Texture formats beyond 9:** 48 members hold DXT5 sheets (format 4). None is a character body or cut-in bank. Whether
  Disgaea 5 loads DXT5 or a 256 × 4 palette is **Unknown**, as above.

## Compatibility with other games

| Direction | Asset | Works? | Evidence |
|---|---|---|---|
| D2 → **Disgaea 5** | Class and monster sprites | Yes (HD), if the palette counts are rewritten ([above](#palette-counts-and-disgaea-5)) | **Community-reported**: one class (Shark Dragon) and one monster (Xenolith) built and installed as a test; an unrewritten D2 monster bank crashes D5 (**Verified in game**) |
| **Disgaea RPG** → D2 | Costumes | Yes, in the native port | **Verified in game** (port): two Etna outfits |

## Sources
2026-11-20 to 11-25 additions (sprite banks, cut-ins, palette counts): the D5 Mod Kit development fork's source-format work on the 1.40 executable and its live capture of the palette overrun.
D5 Mod Kit development fork `d5lib/dd2source.py`; [Disgaea D2 native port](https://github.com/Sean13128/disgaea-d2-recomp) `tools/d2_anm.py`, `tools/d2_face_atlas.py`; this project's prior-art assessment (2026-10-26).
