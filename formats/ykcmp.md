# YKCMP_V1

Compression. **Used by:** Disgaea 5 Complete PC (data and saves), Disgaea PC and Disgaea 2 PC (saves), Disgaea 6 and 7
(Makai Kit reads it); the D1/D2 save thread calls it common to NIS games.

## Header (20 bytes, little-endian)

| Offset | Size | Field |
|---|---|---|
| 0x00 | 8 | Magic `"YKCMP_V1"` |
| 0x08 | 4 | Encoding |
| 0x0C | 4 | Compressed size |
| 0x10 | 4 | Uncompressed size |

The 20-byte header explains the D1/D2 save note that the compressed-size field "includes 20 header bytes".

## Encodings

| Value | Method | Source |
|---|---|---|
| 4 | NIS LZ77 ("Yukes LZ") | D5 Mod Kit, Makai Kit |
| 7 | zlib | D5 Mod Kit (Disgaea 4 face textures) |
| 8, 9 | LZ4 | Makai Kit (D6/D7) |

## Uses

- Disgaea PC / 2 PC saves: XOR over the whole file, then `YKCMP_V1`.
- Disgaea 5 saves: a nibble-obfuscated header, then u64 XOR, then a type 4 stream ([D5 INDEX](../games/mainline/disgaea-5-alliance-of-vengeance/INDEX.md#save-files)).

- Disgaea 1 Complete HD faces: each `swf####` entry of `SUBDATA.DAT`'s `anmdat.fad` holds a type 4 stream at entry + 48
  ([FAD](fad.md#disgaea-1-complete-anmdatfad-the-hd-faces); **Verified offline**, D5 Mod Kit development fork, 2026-11-18).
- Disgaea 4 face textures (`WF.dat`, `swf<N>.nltx`) are type 7 (zlib) with BC7 pixels ([textures](textures.md)).

## Tools
D5 Mod Kit (`d5lib/ykcmp.py`), [Makai Kit](https://github.com/HybridEidolon/makaikit) (`ykcmp`), HenryEx's QuickBMS save script ([ZenHAX](https://www.zenhax.com/viewtopic.php@t=4698.html)).
