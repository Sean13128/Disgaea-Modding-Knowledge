# NMB / NMA (Disgaea 6 and 7 models and animations)

**Used by:** Disgaea 6 (`data/model.dat`) and Disgaea 7 (`data/model_1_00.dat`): models `.nmbm`, model sets `.nmbs`,
animations `.nmba`; textures `.nltx`.

**Evidence:** this project's Character Forge readers (October 2026). Every retained sample parsed: 334 models (70 D6,
264 D7) and 780 animations (276 D6, 504 D7); textured rest poses and walk/attack frames were rendered and inspected.
**Verified offline**. Not a complete game census, and native lighting and rotation conventions aren't confirmed.

## Containers
- Members are stored raw, or behind a 12-byte wrapper with `YKCMP_V1` compression (type 4 LZ77, types 8/9 LZ4).
- **Chunked files:** a file header, then chunks with size-checked bodies, ending in an `EOFC` record (models can repeat
  it; animations can end with zero padding). The container length can exclude an outer 32-byte `EOFC`.

## Models (`.nmbm`)

| Chunk | Holds |
|---|---|
| `MESH` | Planar vertex streams: position, colour, normal/tangent, half-float UV, four bone weights + joint indices; 16- or 32-bit triangle indices. Record `+8` = first `CLUS` index, `+12` = cluster count. Facial shapes: descriptor/group counts at header `+32`/`+36`, name table at `+56`; 12-byte descriptors and groups, 40-byte deltas (global vertex index + nine floats) |
| `CLUS` | Clusters, 20 bytes: cluster ID, node ID, **material ID**, index count, index offset. Records start at the body-relative header `+4` pointer, count at `+28` |
| `MATE` | Materials from body `+0x24`, stride 0x30; property-group offset at record `+0x18`; sampler records carry a string offset |
| `NODE` | Skeleton: parents, deform indices, scale / position / quaternions. Deform records 0xE0 bytes (authored 4 × 4 inverse-bind matrix at `+160`, stored transposed), other nodes 0xA0. Names and full paths are record-relative offsets at `+0x10` / `+0x14` |

## Animations (`.nmba`)

| Chunk | Holds |
|---|---|
| `TIME` | Sample times |
| `TRSA` | Per-node tracks (node ID = low 16 bits; upper bits are flags 0/2/3/5). Format 3: dense keys, four floats each (scale 0x0E, rotation 0x0F, translation 0x10). Format 0: **scalar curves**, ten channels per node (scale 0–2, Euler rotation 3–5, translation 6–8, visibility 12); 12-byte channel header, 28-byte keys (flags, frame, tangent in/out, weight in/out, value) |
| `BLSA` | Expression (blend-shape) weights |

Visibility (channel 0x12) multiplies down the node hierarchy.

## Textures (`.nltx`)
128-byte header, then the `YKCMP_V1` payload (from this project's Character Forge reader 
texture code; **Verified offline** on the retained samples):

| Offset | Size | Field |
|---|---|---|
| 0x00 | 8 | Magic `NMPLTEX1` |
| 0x14 | 1 | Format kind: 1 RGBA, 3 BC3, 6 BC7; 2 = decided by the next field |
| 0x16 | 2 | For kind 2: bits per 4 × 4 block, 512 RGBA, 128 BC3, 64 BC1 |
| 0x18 / 0x1C | 4 / 4 | Width / height |
| 0x2C / 0x30 / 0x34 | 4 × 3 | Decoded size, packed size, payload offset (128; offset + packed = file size) |
| 0x38, 0x39 | 1, 1 | Non-zero means a **tiled** layout (not handled by these readers) |
| 0x80 | — | `YKCMP_V1` block: mode at `0x88` (4 = LZ77, otherwise LZ4), compressed size `0x8C`, decoded size `0x90`, data from `0x94` |

Base levels are e.g. 2048² or 1024²; map textures carry a full mip chain after the top level. Block formats decode
to BGRA. See [textures](textures.md).

## Open questions
- Each node's rotation order (the readers assume an FBX-style order from the flags; unconfirmed).
- The rest of the chunk types.

## Sources
This project's Character Forge research notes (ART-SOURCES, "Modern" sections, 2026-10-11). (reader code not published; its results are the tables on this page)
