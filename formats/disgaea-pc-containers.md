# Disgaea PC containers: PSPFS, OLA, COLA, SOLA, IMY, KM3, MPP, MPD, GEO, SH

The file formats of **Disgaea PC** (Steam, 2016; based on the PSP release). Most were worked out by ProgSys for
pg_disatools; the tables here are short summaries, and each heading links to the full write-up on the
[pg_disatools wiki](https://github.com/ProgSys/pg_disatools/wiki). Everything is little-endian.

**Used by:** Disgaea PC. Several also appear in Disgaea 2 PC (a variant sprite sheet, "SH2") and in older NIS games.
**Evidence:** **Community-reported** (pg_disatools wiki and source, read 2026-11-04). pg_disatools reads and writes
all of these, so they're well tested in practice.

## How the files nest

```
DATA.DAT, SUBDATA.DAT            PSPFS_V1 archives (named entries)
└─ START.DAT, SCRIPT.DAT, FACE.DAT, ANM*.DAT
                                 compressed offset-list archives (IMY chunks); unpacked: offset-list archives (unnamed)
   ├─ table files (CHAR_E.DAT, DUNGEON.DAT, MAGIC.DAT …)
   ├─ TALK.DAT, SCRIPT.DAT       scripts (see the game's scripts page)
   ├─ ANM00.DAT …                sprite offset-list archives (SOLA) of .SH sprite sheets
   ├─ MP*.MPP                    map textures and models
   └─ EFFECT.KM3                 effect models
```

Two rules hold throughout, and they matter for modding:

- **Position, not name, finds a file.** Inside offset-list archives nothing is named, and even in PSPFS the game
  mostly goes by position. Padding entries (`DUMMY.DAT`) keep positions stable. Don't delete or reorder entries.
- **Compressed members can be stored uncompressed.** The game accepts a decompressed `START.DAT` in place of the
  compressed one, so tools decompress once and never recompress.

## [PSPFS_V1](https://github.com/ProgSys/pg_disatools/wiki/PSPFS_V1)

Top-level archive (`DATA.DAT`, `SUBDATA.DAT`).

| Offset | Size | Field |
|---|---|---|
| 0x00 | 8 | Magic `PSPFS_V1` |
| 0x08 | 4 | File count |
| 0x0C | 4 | Zero |
| 0x10 | count × 52 | Entries: 40-byte upper-case name (space padded), u32 decompressed size (0 = stored), u32 size, u32 offset |

Most `.ogg` music files can be moved out of the archive and the game still finds them. The PSP version of the format (Phantom Brave PSP, read by
[Scarlet](https://github.com/xdanieldzd/Scarlet)) has the same header but **32-byte entries**: 24-byte name, u32 size,
u32 offset, with no decompressed-size field. Not the same layout as
Disgaea 1 Complete's [`PS_FS_V1`](ps_fs_v1.md) (64-bit fields, 64-byte entries).

## [Offset-list archive (OLA)](https://github.com/ProgSys/pg_disatools/wiki/Offset-list-archive)

`u32 count`, then `count` u32 offsets, then the files back to back. A file's size is the gap to the next offset.
**Exception:** in `SCRIPT.DAT` the offsets don't count the header.

## [Compressed offset-list archive (COLA)](https://github.com/ProgSys/pg_disatools/wiki/Compressed-offset-list-archive)

Like an OLA with one more header field: `u32 count`, `u32 chunk size` (bytes each chunk unpacks to), then offsets to
IMY chunks. Every chunk unpacks to the chunk size except the last; the chunks together make the archive. (This
project's scan of `START.DAT` found a 131,072-byte chunk size.)

## [IMY](https://github.com/ProgSys/pg_disatools/wiki/IMY)

Lossless compression of one chunk. Header: magic `IMY\0`, offsets to the compressed data and to a look-up value,
a compression type, then zeros. Type 8 is a short-oriented scheme: a list of instruction bytes, then data; each
instruction either copies shorts from the data or repeats shorts from already-unpacked output, looking back by a
distance from a four-entry table. Type 0 is used for RGBA images such as fonts and isn't documented yet.

## [Sprite offset-list archive (SOLA)](https://github.com/ProgSys/pg_disatools/wiki/Sprite-offset-list-archive)

An OLA of `.SH` sprite sheets with a **u16 sprite ID per file** after the offsets, so sprites are found by ID. The game
keeps a few loader "buckets": `ANM00.DAT` stays loaded in the first, other `ANM*.DAT` archives load into the free ones,
and a sprite lookup searches the buckets for the ID. Each loaded sprite is pre-processed into a fixed 0x1200-byte record.
New sprites can be added, but the game's data buffer has a fixed size (see the game's
[modifiable page](../games/mainline/disgaea-1-hour-of-darkness/modifiable.md)).

## [SH sprite sheet](https://github.com/ProgSys/pg_disatools/wiki/Sprite-sheet)

One character's or object's 2D art and animations, in eight parts: header with counts and four pointers;
**animations** (start keyframe + animation ID; an animation ends where the next begins); **layers** (runs of cutouts
drawn in order); sheet descriptions (sizes are matched to powers of two); **keyframes**; **cutouts** (rectangles on a
sheet); **colour tables**; and the sheet images as palette indices (4-bit, 16 colours by default).

- Weapon sheets have no animations: one indexed image and colour tables. Where a weapon sits and how big it is is set
  **in each character's sprite**, not in the weapon file, so an HD weapon also needs every character's weapon placement
  changed. Weapon shapes are often one image with different colour tables, where some colours are transparent.
- Disgaea 2 PC uses a related layout ([Sprite sheet 2](https://github.com/ProgSys/pg_disatools/wiki/Sprite-sheet-2)).

## [KM3](https://github.com/ProgSys/pg_disatools/wiki/KM3)

Only `EFFECT.KM3`: the spell and attack effect models (explosions, glows, flames, lightning). Header `u32 count`,
`u32 header size`, then a table of **sizes** (not offsets), then u16 IDs, zero-padded to 16 bytes; each entry is a
complete GEO file on a 16-byte boundary. IDs are sparse (1–160 with 32 gaps), so removing one doesn't renumber the
rest. Sizes plus header add up exactly to the file size, which tells a KM3 apart from an OLA.

## [MPP](https://github.com/ProgSys/pg_disatools/wiki/MPP)

Textures and models for one map. Header: texture count, has-normal-maps flag, model count, unknown, total size; then
offsets; then files in a fixed order with no names: colour textures (TX2), normal maps (only in the HD `…NEW` files),
models (GEO).

## [MPD map](https://github.com/ProgSys/pg_disatools/wiki/Map-file)

The map layout: **chunks** (position, up to 32 placed objects from the MPP with transforms and texture scrolling, and
up to 16 event tiles), then all the tiles in one list. Each tile has base-colour and decal texture coordinates. The only
known event tile ID is 1, the teleporter spawn. Earlier work:
[rustedlogic MPD notes](https://disgaea.rustedlogic.net/MPD_format), [DisgaeaMap](https://github.com/xdanieldzd/DisgaeaMap).

## [GEO](https://github.com/ProgSys/pg_disatools/wiki/GEO)

3D models as a scene graph: transforms (also used as keyframes), alpha keyframes (for simple particle effects), scene
nodes with children, meshes, surfaces and embedded textures. Embedded textures are an older format or a TX2 variant;
each one's size field is 16-bit, so **every embedded texture but the last is limited to 65,535 bytes**. In Blender, a
scale of 6 fits a default cube to one tile.

## [TX2](https://github.com/ProgSys/pg_disatools/wiki/Textures)

16-byte header: width, height, type, log2 width, log2 height, colour-table size, colour-table count, a constant. Types:
0 DXT1, 1 DXT4, 2 DXT5, 3 BGRA, 4 font (BGRA plus a fixed 16-entry table), 16 paletted (≤ 16 colours, most sprites),
256 paletted (≤ 256). See also [textures](textures.md).

## Tools
[pg_disatools](https://github.com/ProgSys/pg_disatools): File Manager (steps into nested archives, decompresses),
Sprite Sheet Editor, TX2 Editor, Map Editor.

## Sources
[pg_disatools wiki](https://github.com/ProgSys/pg_disatools/wiki) and source by ProgSys (LGPL-3.0 / MIT), read
2026-11-04. Earlier talk-script work credited there to Krisan Thyme's toolpack.
