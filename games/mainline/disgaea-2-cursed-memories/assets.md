# Disgaea 2: Cursed Memories — Assets

| Asset | Format | Where |
|---|---|---|
| Faces | `SWF<body>.TXP` | `DATA.DAT` → `WFPACK_PC.DAT` |
| Illustrations | ANM | `DATA.DAT` → `CUTINPACK.DAT` |
| Movies | Ogg media (likely Theora) | `mov00.ogm`, `mov01.ogm` |
| Battle sprites | Sprite sheets in a layout different from Disgaea PC's SH (10-field header, 9 data blocks including colour tables and image infos; only partly understood) | `.dat` sprite archives; layout sketch: [Sprite sheet 2](https://github.com/ProgSys/pg_disatools/wiki/Sprite-sheet-2) (pg_disatools wiki), and the D1 format on [Disgaea PC containers](../../../formats/disgaea-pc-containers.md#sh-sprite-sheet) |

TXP is the texture format of NIS's PSP-era games (Disgaea 2 PSP, Disgaea Infinite); [Scarlet](https://github.com/xdanieldzd/Scarlet) reads it.

## Compatibility with other games

| To | Asset | Works? | Evidence |
|---|---|---|---|
| **Disgaea 5** | Class sprites | Needs scaling: D2's sprites are low-resolution. Recommended: whole-number pixel scaling to D5 figure height, not AI upscaling, which invents detail and blurs the palette rows | **Unknown** (planned) |
