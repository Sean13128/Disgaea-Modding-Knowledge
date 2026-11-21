# NLSD (sound)

Sound file. **Used by:** Disgaea 4 (voices, `.nlsd`), Disgaea 6 and 7 (sound and voices, e.g. `data/sound/*.nlsd`).

## Disgaea 6 / 7 layout

From Makai Kit's `nlsd` reader (**Verified offline**: source read). Little-endian, 0x18-byte header:

| Offset | Size | Field |
|---|---|---|
| 0x00 | 4 | Format: **5 or 7** (the reader rejects others) |
| 0x04 | 4 | Total data size |
| 0x08 | 2 | Sample rate |
| 0x0A | 1 | Stereo (0 or 1) |
| 0x0B | 1 | 0 |
| 0x0C | 4 | Sample count |
| 0x10 | 4 | Loop-start offset ("middle") |
| 0x14 | 4 | Loop-end offset ("end") |

Data starts at 0x18 and splits into up to three sections: an intro (`0x18` … middle), a loop body (middle … end) and an
outro (end … total). What codecs formats 5 and 7 are is **Unknown** here.

## Disgaea 4 variant

D4's `.nlsd` voice files are a **28-byte** header followed by a standard RIFF (WAV) file in MS-ADPCM, mono, with a
sample rate per line (about 22 kHz) (**Community-reported**: D5 Mod Kit development fork). So D4's NLSD differs from D6/D7's.

## Tools
[Makai Kit](https://github.com/HybridEidolon/makaikit) (`nlsd`), D5 Mod Kit development fork (D4 voices).
