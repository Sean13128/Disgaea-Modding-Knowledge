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

## Compatibility with other games

| Direction | Asset | Works? | Evidence |
|---|---|---|---|
| D2 → **Disgaea 5** | Class and monster sprites | Yes (HD) | **Community-reported**: one class (Shark Dragon) and one monster (Xenolith) built and installed as a test |
| **Disgaea RPG** → D2 | Costumes | Yes, in the native port | **Verified in game** (port): two Etna outfits |

## Sources
D5 Mod Kit development fork `d5lib/dd2source.py`; [Disgaea D2 native port](https://github.com/Sean13128/disgaea-d2-recomp) `tools/d2_anm.py`, `tools/d2_face_atlas.py`; this project's prior-art assessment (2026-10-26).
