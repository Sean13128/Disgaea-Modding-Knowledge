# FAD and FANM (Disgaea 6 and 7 2D art)

FAD is the container Disgaea 6 Complete and Disgaea 7 use for 2D illustration and UI animation art: a header, a
resource table (animations and other named resources), a texture table, a name pool, then the texture payloads.
FANM is the animation resource inside it that lays body and expression parts out on screen, used for Disgaea 7's
story pictures ("BU"). Little-endian.

**Used by:** Disgaea 6 Complete (`data/data.dat`, members ending in `.fad`), Disgaea 7 (`data.dat`; story pictures in
`anmdat_bu_1_00_share.fad`). Language copies exist per locale (`/en/`, `_en.fad`). Disgaea 1 Complete also has files with
the `.fad` name (`anmdat.fad` in `SUBDATA.DAT`, holding its HD faces); that one is a much simpler container,
[described below](#disgaea-1-complete-anmdatfad-the-hd-faces). It is not known to share the `0x6A` header above.
**Evidence:** **Verified offline** against both games' installed PC archives (Character Forge's reader, which renders
standing pictures and story pictures from them; October 2026). Fields not used by that reader are left unnamed.

## Header (64 bytes, 16 × u32)

| Word | Meaning |
|---|---|
| 0 | Version, `0x6A` |
| 1, 2, 4, 5 | Counts of the four resource groups; together they're the number of 32-byte resource entries |
| 3 | Texture count |
| 9 | Offset of the texture payloads (also the end of the name pool) |
| 12 | Offset of the name pool |

After the header: the **resource table** (32 bytes per resource), then the **texture table** (32 bytes per texture),
then the name pool, then the texture payloads.

## Resource entry (32 bytes)

| Offset | Size | Field |
|---|---|---|
| 0x00 | 8 | Name, ASCII, zero-padded (Disgaea 7 story pictures: `BU_nnnnn`) |
| 0x08 | 4 | Size |
| 0x0C | 4 | Kind (2 = FANM animation) |
| 0x10 | 8 | Offset |
| 0x18 | 8 | — |

## Texture entry (32 bytes)

| Offset | Size | Field |
|---|---|---|
| 0x00 | 8 | — |
| 0x08 | 8 | Length of the texture payload |
| 0x10 | 4 | Offset of the payload |
| 0x14 | 4 | Offset of its name in the name pool (Shift-JIS, zero-terminated, e.g. `xxxxNN.tga`) |
| 0x18 | 4 | Width |
| 0x1C | 4 | Height |

Each payload is a 64-byte wrapper followed by an ordinary `NMPLTEX1` texture ([NLTX](textures.md)); the NLTX's own
size field (at its `+0x30`) plus its 128-byte header gives its length. Width and height in the table match the NLTX.
The PC files store a trimmed atlas, while FANM crops still assume the power-of-two size; the area outside is clear.

## FANM (animation resource)

| Offset | Size | Field |
|---|---|---|
| 0x0C | 4 | Type, 2 |
| 0x28 | 4 × u16 | Counts: sequences, frames, keys, parts |
| 0x38 | 4 × u32 | Offsets of the sequence (16-byte rows), frame (16), key (32) and part (160) tables |

- **Sequence row:** ID, frame count, first frame, first key, —, key count (u16s), then a u32.
- **Key row:** child and sibling key indices (u16, `0xFFFF` = none) at `+0x08`, so keys form a tree; part count and
  first part (u32 each) at `+0x0C`.
- **Part row (160 bytes):** texture index (s32; negative = no picture; subtract the FAD's resource count to index the
  texture table), then at `+0x10` eleven floats: source x, y, width, height, pivot x, y, position x, y, scale x, y
  (negative = mirrored), angle.

A still picture is the first frame of a sequence: each part's crop is scaled about its pivot and placed at its
position; parents' scale and mirroring apply to their children.

## Where the games find their pictures

- **Disgaea 6:** standing illustrations are named after the model: a six-digit `xxxxNN.tga` whose first four digits
  are the character's model family (several expressions per character).
- **Disgaea 7:** `model.dat` gives each model its rig (`rig_nnnnnn_…`); `character.dat` points at a model; `anime.dat`
  rows named `ANIME_ID_BU_…` name a `BU_nnnnn` resource; `bu.dat` rows give an anime ID and a sequence number. That
  chain leads from a character to its story-picture sequences in `anmdat_bu_1_00_share.fad`.
- **Faces (both):** `icon.dat` rows `ICON_ID_WF_BATTLE_…` point at a `texture.dat` row (an `.nltx` atlas) and hold a
  crop rectangle per language (English is the second); a character's face ID + 10,000 is its icon row.

## Disgaea 1 Complete: `anmdat.fad` (the HD faces)

`SUBDATA.DAT` on the Switch build holds a member `anmdat.fad` (22,532,352 bytes in the build checked) with **268 face
textures** named `swf####`. The PS4 build holds a member `zz_anmdat.fad` with the same 268 names in a different pixel
layout, which is not decoded. **Evidence: Verified offline** (D5 Mod Kit development fork, 2026-11-18; the Switch file,
decoded and checked against the game's face-key rule, a render of all 268, and a rebuild of 59 packs).

Entry, little-endian:

| Offset | Size | Field |
|---|---|---|
| 0x00 | 8 | Name `swf####` plus a zero byte |
| 0x08 | 4 | Entry size |
| 0x0C | 4 | 0 |
| 0x10 | 4 | Packed size |
| 0x14 | 4 | Raw size (66,560) |
| 0x18 | 2 × 2 | Width, height (256 × 256 in all 268) |
| 0x1C | 2 × 2 | Colours (256), bits (8) |
| 0x20 | 16 | Unread here |
| 0x30 | | `YKCMP_V1` type 4 (Yukes LZ) stream: see [YKCMP](ykcmp.md) |

The decompressed data is **1,024 bytes of RGBA palette** (alpha 255 throughout) followed by **65,536 linear 8-bit
indices**. Every face is a full 256 × 256 head-and-shoulders frame on opaque white (262 of 268 have a white corner pixel).
The face for a character is looked up by `swf%04d` of the character row's sprite ID plus its palette (**From decomp**,
Switch v0), with four exceptions in the key. This is the set that matches D1 Complete's redrawn battle bodies; the older
`start.dat` member 73 is a PC-era set (see [portraits and faces](portraits-and-faces.md)).

## Tools

Character Forge (unpublished) reads FAD, FANM and the face chain. [Makai Kit](https://github.com/HybridEidolon/makaikit)
reads the database tables named above.
