# Textures: TXF, TX2, TXP, NLTX, NMT

| Format | Used by | Notes |
|---|---|---|
| **TXF** | Disgaea 5 (UI; inside OBF models), Disgaea 4 PS3, Disgaea D2 (faces, behind `txf\0` + LZS), Disgaea 3 Vita | 16-byte **big-endian** header: format bytes (`00 01 01 01` = 32 bpp), width, height, …, pixel byte count. Some D5 `.txf` files are DDS |
| **TX2** | Disgaea PC, Phantom Brave PS2, other NIS games | 16-byte header; types 0 DXT1, 1 DXT4, 2 DXT5, 3 BGRA, 4 font, 16 / 256 paletted ([details](disgaea-pc-containers.md#tx2)). pg_disatools converts to TGA / PNM |
| **TXP** | Disgaea 2 (PSP and PC faces), Disgaea Infinite, Z.H.P. | 16-byte header: width, height, colour count (16 or 256), unknown, palette width, palette height (= number of palettes), PSP-swizzle flag, unknown. Then palettes (ABGR, 4 bytes per colour), then 4- or 8-bit indexed pixels (swizzled on PSP when the flag is set) |
| **NLTX** | Disgaea 4 (faces), Disgaea 1 Complete (story pictures), Disgaea 6, 7 | D6/D7: magic `NMPLTEX1`, YKCMP data at 128, RGBA / BC1 / BC3 / BC7. D4 faces use an older NLTX header with YKCMP type 7 (zlib) and BC7 |
| **NMT** (`nismultitexform`) | Disgaea 4 Vita; Disgaea 4 PC effect textures (the D4 PC reader at `0x1404516D0` uses the same field offsets as Scarlet's Vita reader; inside OBF models the file follows a 16-byte magic) | Magic `nismultitexform\0` (16 bytes), four u32 (sizes), pixel format at 0x20 (`02` ARGB8888, `0A` 4-bit indexed, `0E` 8-bit indexed), palette count at 0x25, width at 0x26, height at 0x28, stride at 0x2C, pixels from 0x30; then palettes and pixels. One file can carry several palettes |

Layouts for TXP and NMT: [Scarlet](https://github.com/xdanieldzd/Scarlet) source (`TXP.cs`, `NMT.cs`; MIT) (**Community-reported**).

## Disgaea 4 effect textures (NMT inside OBF models)

What the D4 special-attack catalogue showed about the textures in D4's effect models, found while converting them for D5
(**Verified offline**, D5 Mod Kit development fork `docs/D4_EFFECTS.md`, 2026-11-19; pixels of every converted palette
frame compared one by one).

- Indexed 4-bit and 8-bit surfaces have power-of-two sides from 16 to 512 and **2 to 16 palettes** (palette frames).
  Sizes seen include 64 × 32, 128 × 64, 256 × 128, 512 × 256, 512 × 512 and 64 × 128, up to 10 palettes.
- A model's material records are **60 or 72 bytes**; D5's own effect models carry 932 of the 72-byte kind.
- A material can **step through several texture entries**: byte `0x23` is the entry count and mode byte `0x22` is 0 or 2.
  D5's own models use this encoding about 1,500 times. A texture attribute byte (byte 2 of an entry) is the entry's display
  time in such a material.
- Disgaea 5 drew converted effects with 2 and 16 palette frames and single-palette animated entries (**Verified in game**,
  a pilot of ten attacks); palette frames inside animated entries, and the larger sizes, were not recorded live
  (**Unknown**).
- **Two 32-bit ("color32") textures end 16 bytes short** in the effects of two D4 attacks (effect models 148 and 205): the
  wrapper says pixels = width × height × 4, but the entry stops 16 bytes before pixels plus the 32-byte footer, so the footer
  would overlap the last 16 pixel bytes. How D4's own reader (`0x1404516D0`) treats them is not traced, so the real layout is
  **Unknown**; no pixels were guessed, and those two effects were not converted (**Verified offline**).

## Tools
[Scarlet](https://github.com/xdanieldzd/Scarlet), [pg_disatools](https://github.com/ProgSys/pg_disatools) (TX2), D5 Mod Kit (`d5lib/txf.py`; `d5lib/d4effects.py` for D4 effects).
