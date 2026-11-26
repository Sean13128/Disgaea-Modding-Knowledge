# anm (2D sprite animation)

NIS's sprite animation format. **Used by:** Disgaea 4, Disgaea 5, Disgaea D2, Disgaea 1 Complete (its HD sprites are
Disgaea 5 anm files). Usually stored as `anmNNNNN.lzs` (NIS LZS around the file).

## Layout

- 32-byte file header: image-data offset − 0x10, image-data size, texture count, palette count, block count.
- Blocks (one animation set each), each with a 0x44-byte header and **ten tables**: animations, nodes, keys, palette
  info, texture info, cells, transforms, pivots, colours, params.
- 16-byte image headers (textures first, then palettes), then image data aligned to 0x80.
- **Image header (D5)**, 16 bytes: format (4 bytes: `09 01 01 01` 8-bit indexed, `00 01 01 01` 32-bit), u16 width,
  u16 height, u32 **byte-order marker**, u32 offset into the image data. The image data starts right after the last
  header. **The marker's low 16 bits must be 1:** with any other value D5 reads width, height and size byte-swapped
  (the big-endian form Disgaea D2 uses), so a converted D2 image that keeps its marker becomes many texture
  sub-resources, and enough of them freeze the game (**Verified in game**; see D5
  [modifiable](../games/mainline/disgaea-5-alliance-of-vengeance/modifiable.md)).
- Textures: 8-bit indexed (256-colour ARGB palettes) or 32-bit ARGB. 60 ticks per second.

**Control keys (D5):** a control node's key keeps its control code in the **low byte** of the key's `flags` u16.
Measured with the game's own animation-advance code (`0x1402F4F70`, build B): code 1 marks a loop start, code 5 holds
the pose, and code 0 means no control action, so an animation with the code in the wrong byte just keeps looping (a
converter that read the table big-endian made exactly that mistake and froze the Recruiter's preview). Other codes
exist (loop back, conditional loop, wait) but weren't measured here.

## Variants by game

| Game | Node row size | Notes |
|---|---|---|
| Disgaea 5 | 20 bytes | Full detail: [D5 assets](../games/mainline/disgaea-5-alliance-of-vengeance/assets.md) and D5 Mod Kit's [ANM.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/ANM.md) |
| Disgaea 1 Complete | 20 bytes | Same as D5; monsters use D5's action IDs |
| Disgaea 4 | 16 bytes | D5's two extra node words are absent |
| Disgaea D2 | 16 bytes | **Big-endian**; otherwise the same layout as D4. Table row sizes 4, 16, 12, 8, 12, 18, 16, 4, 20, 4. D4's and D2's shared humanoid file `anm00001` is nearly identical (248 tags and 285 rectangles in both) |
| Disgaea 3 (Vita) | 16 bytes | Little-endian; 16-byte rectangles (D5: 18). Several bodies per member |
| Disgaea 2 PC | 12 bytes | An older table set: 60-byte block header (length, twelve counts, eight table offsets); tracks 12, keys 12, palette refs 8, sheet refs 12, rectangles 16, transforms 16 bytes. Sheets mostly 4-bit (low nibble first) with 16-colour palettes stored blue first. Sprites are about 40 × 56 pixels (D5: about 100 × 170) |
| Disgaea PC (D1) | — | Not anm: **SOLA** containers of **SH** sprite files (4-bit sheets, BGRA palettes; standing = tags 1/7; tag 0 is a "No Data" sentinel; anchors stored negated), per pg_disatools |

Animation IDs end in 1 (front) or 7 (back); the engine mirrors the other two directions. Across the family, 6001 / 6007
are a humanoid's own standing loops, 7001+ its unique poses, and bodies that carry every action use 1 and 7 for standing.
The first track is drawn on top.

What the ids *mean* is not shared between games: the same number can be a different motion, and nothing in the files names
them. See [Motion ids and what they mean](motion-names.md).

**Disgaea 1 Complete draws its HD humanoids with Disgaea 5's animation module** (**Verified offline**, Switch v0
executable and the Steam build of D5). The humanoid character ids bind the owner to a shared block 1 with binding key 1 and
the body; block 1 is D5's resident block 1 renumbered (the same cell rectangles, 93 of 105 motions equal to a D5 resident
motion key for key). A body supplies only its textures, palettes and its own standing motions (0, 6001, 6007, some also
7001-7031). Any other HD character (monsters) plays its own body's motions. A converter can therefore put D5's resident
motions on a D1 Complete body and get what D1 Complete draws.

## How Disgaea 5 reads each field

Read from the loader, record builder and renderer of the Steam build (**B**); **From decomp** unless noted. Several
fields that tools show as one 16-bit number are really two bytes, or half of a 32-bit value, and a writer that treats
them as one number can change what the game does.

| Field | What the game reads | Site (build B) |
|---|---|---|
| Key `+2` | Byte `+2`: on a control (root) node the control command (1 loop mark, 2 loop, 3 conditional loop, 4 wait, 5 hold); on a drawing layer 0 = step, anything else = interpolate. Byte `+3`: 1 spawn / 2 despawn an attachment (params row = motion ID, start, duration), 3 / 4 / 5 Hermite position / rotation / scale (tangents from params), 6 morph the cell rectangle toward the next key's cell | `0x1402F5002`, `0x1402F91AC`, `0x1402F5750`, `0x1402F8251` |
| Cell `+4` | Byte `+4`: a per-draw attribute (meaning **Unknown**); byte `+5`: blend mode (2, 3, 4 as written, anything else 1). Stored as 512 / 768 / 1024 / 1280 when read as a u16 | `0x1402F8D39`, `0x1402F8D48` |
| Cell `+6` | Byte `+6` flip X, byte `+7` flip Y (u16 value 1 = flip X, 257 = both) | `0x1402F89A5`, `0x1402F89FA` |
| Cell `+16` | Low byte = the draw-depth group the game sorts by | `0x1402F8EDC`, sort `0x1400A31A0` |
| Node `+0..+3` | One u32: bits 0–23 an asset ID (or a binding key when bits 24–27 ≠ 0), bits 28–31 a draw mode | `0x1402F688E`, `0x1402F8DCE` |
| Node `+8..+11` | One i32: 1 = 10-point mesh | `0x1402F8722` |
| Transform `+4..+7` | One i32 pivot index | `0x1402F5A38` |
| Texture info width / height | Used for buffer size and UV scale | `0x140301514`, `0x1402F8DD4` |

**Palette-info colour count (a crash).** The attack set-up copies as many colours as a palette-info row's `i32 at +4` says
into a fixed 256-colour buffer, with no clamp (the copy loop is at `0x1402F9550`, build **B**; from `start_basic_attack`,
`0x140545C42`). Disgaea D2 stores that count as a big-endian i32 (256 = `00 00 01 00`); a converter that keeps the bytes makes
D5 read 0x01000000, about 16.8 million colours, and the loop overwrites roughly 64 MB of globals after the buffer (the
memory allocator's handle, the animation bank table, the effect arena). **Verified in game** (build B): a converted D2 monster
bank crashed on every attack, the writer was found with a software write watch (see
[automated testing](../guides/automated-testing-disgaea-5.md#finding-who-writes-a-memory-location)), and the same bank with
counts rewritten to D5's little-endian form (clamped to 256) ran six actions (three attacks, a hit taken, two specials) without a crash while the unchanged bank
crashed three of three times. In the clean game's 2,006 Japanese-archive banks every row's count is 16 to 256. A bank-checking tool
should reject any palette-info count outside 1..256. In the D2 table the same row reads `num_colors` 0 and `unk` 256 when read
byte for byte.

Key cell, colour and param indexes are signed (−1 = none). Node `+4` / `+6` weren't read by any function checked. A
palette-info row's `i32 at +4` *is* read (the colour count, below; corrected 2026-11-25: this page previously said palette-info
rows were not read).

**What gets drawn.** An animation's picture is node `root + 1` and its children. The root node is only read for its
control keys (loop, end); its own child and next links are never followed (builder `0x1402F5450`, build **B**). Every
native animation in the resident pack (5,484) has `root.child = −1`, `root.next = root + 1` and no siblings at
`root + 1`. An import that hung its drawing elsewhere played the wrong pose or drew nothing (**Verified in game**:
characters stood with their backs turned and the back idle drew nothing). A node whose asset ID is non-zero draws its
own cell only when that ID is the ID of a loaded block (a value of 0x10000 or more never is); with binding bits set it
needs a binding entry instead; its children draw either way (`0x1402F7C60`). Across all 2,006 Japanese-archive files
plus the resident pack, drawn nodes store (ID 0, mode 0) 68,019 times, (0, mode 1) 4,089 times, a binding 1,803 times,
and never an asset ID of 0x10000 or more (**Verified offline**).

**Idle motions.** The game picks a motion by ID and, when the ID is missing, falls back to the file's first animation
(usually the empty ID 0): the unit's tile then shows a shadow and an HP bar only (`0x1402F4BA0`, build **B**). A battle
body needs 1001 and 1007.

**Animation IDs above 32,767 never play.** The play call takes the ID as a signed 16-bit value (`0x14014A0E0`, build
**B**), and the Characters panel builds a face ID as `(signed 16-bit)(unit +0x2E36) + 10000`. A face stored at
10000 + a 30,000-range ID is never found (**Verified in game**: blank panel face while the list icon and the Status
bust drew).

**Round trip.** All 2,347 anm files of Steam build 3528766 (all five language archives) parse and re-serialize
byte-identical with the layout above; every byte is accounted for except 3,566 bytes of non-zero filler in 10 files
(block-tail padding in the resident and start-up packs, one image gap in `anm08225`) (**Verified offline**, D5 Mod Kit
development fork `tools/anm_roundtrip.py`).

## Disgaea D2 (PS3) in detail

Source: the seven big-endian NISPACK archives `Data/ANM_HI.dat` and `ANM_HI_1..6.dat` of the US disc (not obfuscated;
each member's LZS header is little-endian as on PC). 2,301 members, 2,056 names (add-on archives repeat some), 2,193
distinct contents. The file is laid out as above (32-byte header, 0x44-byte block headers, tables back to back, block
size 16-aligned, image data 0x80-aligned), everything big-endian. **Verified offline**: all 2,301 members round-trip
byte-identical; 6,990 bytes in 113 members are non-zero filler where the format pads.

| Row | D2 size | Differs from D5 |
|---|---|---|
| Node | 16 | `+0..+3` is D5's node u32 as a big-endian bitfield, top bit first: ID (24 bits, `+0..+2`), binding (`+3` high nibble), draw mode (`+3` low nibble). So D2 `0x0001` in the fourth byte pair is D5 draw mode 1, and it must be rewritten for D5: copied raw, mode 1 reads as asset ID 0x10000 and D5 skips the node's cell. `+4` is 0 in every file; `+6` (mesh) is 0 or 1; first key, key count, child, next at `+8..+14` |
| Key | 12 | `+2` and `+3` are bytes with D5's values |
| Cell | 18 | `+4`, `+5`, `+6`, `+7`, `+16`, `+17` are bytes. 16 bytes (no `+16` word) in `anm09006`, `anm09901`, `anm09903`, `anm09998` |
| Transform | 16 | The pivot index is the word at `+6`; `+4` is 0 in all 126,052 rows (D5's i32 stored big-endian) |
| Palette info | 8 | The colour count is one big-endian i32 at `+4`: rewrite it as D5's little-endian value (clamped to 256) or D5 overruns a buffer (see "Palette-info colour count" above) |
| Others | as D5 | Read big-endian |

Binding IDs used: D2 has 1, 10–17, 21, 31, 99, 111–116; D5 has 1, 10–17, 20, 21, 99, 111–116. Image headers: format
byte 9 (8-bit indexed, 9,603 sheets), 0 (ARGB32, 105) and **4 (DXT5, 48 sheets)**; the u32 at `+8` is `0x00010000`
(D5: 1). Palettes are ARGB32, 256 × 1 (13,354) or 256 × 4 (585). **Unknown:** what D2's asset IDs refer to; whether
mesh 1 means D5's 10-point mesh; cell `+17` (values 0, 18, 63, 150, 221, 240, 245; always 0 in D5).

**What a D5 bank can hold (D2 images that don't fit).** A scan of all 2,347 clean D5 banks found only two texture formats,
8-bit indexed (format 9, 3,402 textures) and 32-bit (format 0, 3,864), and all 11,849 palettes are 256 × 1 ARGB32: no D5
bank has a 256 × 4 palette or a DXT5 sheet (**Verified offline**, D5 Mod Kit development fork, 2026-11-16). Whether D5
would load either is **Unknown**: no source says so and it wasn't tried. A D2 → D5 conversion therefore cuts each palette to
its first 256 colours and must decode DXT5 sheets to ARGB32 or indexed before writing; the D2 reader keeps palettes whole
and DXT5 at 1 byte per pixel, and the cut is a separate step at the D5 boundary.

## Disgaea 4 shared humanoid block and missing pages

D4's shared humanoid block (`anm00001` block 0, 285 cells) indexes sheet pages 0–16 of whatever body it is bound to. A
9- or 10-page D4 body has no art there, so 144 of those cells point at missing pages. D5's own humanoid block (resident
block 1) draws only pages 0–2 and 8. A D4 → D5 conversion that copies those cells unchanged leaves them dangling, and
once the body is repacked onto more pages they draw unrelated art (whole extra poses inside attack frames, **Verified
offline** by measuring built files). Zero such cells (width = height = 0). A cell that runs past the edge of a page
that exists is harmless: vanilla D5 ships some (`anm00021`, `anm00031`).

## The bank's texture objects and the NULL-texture crash

The format-level part of how Disgaea 5 turns a loaded bank into drawable textures (**From decomp**, Steam build B; the
crash and its guard are **Verified in game**, see
[Mac and Linux](../guides/modding-on-mac-and-linux.md#the-rare-freeze-was-a-crash-2026-11-13)). The pool size and the
rule that a humanoid's `N + 2` bank must be resident are engine behaviour and live on the
[D5 functions page](../games/mainline/disgaea-5-alliance-of-vengeance/functions.md).

- A loaded bank is a **0xC0-byte record** (`anm_find_loaded_record`, `0x1403020C0`): `+0x10` palette count, `+0x12`
  texture count, `+0x68` the 12-byte texture headers, `+0x98` the texture-object table, `+0xA0` the palette-object table
  (8-byte pointers). A texture object has width and height at `+0x20` / `+0x22` and its format at `+0x24` (9 = 8-bit
  indexed).
- The record builder publishes a record's ID and its `+0x98` table **before** filling the table, one texture per ~1 ms
  slice; the package release zeroes `+0x98` / `+0xA0` and then frees the record array.
- The node emitters (`0x1402F6B10`, `0x1402F7C60`) queue a draw packet whenever a cell's texture index is below the
  record's texture count, **even if that texture pointer is NULL**; the flush (`0x1402F3650`) and a width/height read
  (`0x1402F7A4C`) assume a live record. A cell whose index is out of range never makes a packet, so bad cell data cannot
  cause the crash; a stale or unbuilt record can.
- A guard that skips NULL-texture packets hides the symptom only and does not cover a stale non-NULL pointer.

## Tools
D5 Mod Kit (`d5lib/anm.py`; reads D4 too), [NisAnim](https://github.com/xdanieldzd/NisAnim) (D4 PS3), the Disgaea D2 native port (`tools/d2_anm.py`).

## Sources
The field readings, D2 layout, round-trip counts, D4 page rule, bank-record layout and the D5 clean-bank scan come from this project's research (2026-11), published in the D5 Mod Kit development fork's `docs/ANM.md`, `docs/D4_BODY_IMPORT.md` and `docs/OUTFITS.md`. The resident-placeholder design credited on the D5 pages is Cryswar's (Overlord).
