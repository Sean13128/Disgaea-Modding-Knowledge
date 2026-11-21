# NIS LZS

Compression. **Used by:** Disgaea 4, Disgaea 5, Disgaea D2, Disgaea 1 Complete (sprite `anmNNNNN.lzs` files, D5 maps);
Scarlet lists it for Disgaea 4 PS3/Vita.

## Layout (Disgaea 5)

| Offset | Size | Field |
|---|---|---|
| 0x00 | 4 | Extension tag: `dat\0` (data) or `pac\0` (map package) |
| 0x04 | 4 | Uncompressed size |
| 0x08 | 4 | Compressed size |
| 0x0C | 4 | Marker byte value |

Then a byte stream: `marker, marker` = a literal marker byte; `marker, distance (adjusted by one if above the marker),
count` = a back-reference (distance ≤ 255); anything else = a literal.

D2's face textures use the same scheme behind a `txf\0` tag. **Disgaea D2 stores the header's sizes little-endian**
even though the rest of its data is big-endian; its decoder (`func_001840E0`, 1.40) is otherwise the same: escape byte,
distance adjustment, overlapping copies (**From decomp**, the D2 native port's recompiled code).

## Tools
D5 Mod Kit (`d5lib/lzs.py`, compressor in `lzs_compress.py`).
