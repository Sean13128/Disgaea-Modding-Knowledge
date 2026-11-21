# Disgaea 4 — Assets

**Evidence:** **Community-reported** from the D4 → D5 import work (D5 Mod Kit development fork) unless noted.

## Formats

| Asset | Format | Where | Notes |
|---|---|---|---|
| **Sprites** | `anmNNNNN.lzs`: NIS LZS around an anm file | `Data/DATA/ANM_HI.dat` (NSAC) | Same layout as D5's anm (32-byte header, blocks with a 0x44 header and ten tables) except node rows are **16 bytes** instead of 20. Palettes are 256 colours. Ten palettes per class |
| **Illustrations / cut-ins** | anm groups `10000 + body` | `ANM_HI.dat` | Plain white background (the importer makes it transparent). 740 members across the cast and classes; each group's first cell is the picture |
| **Face icons** | `swf<NNNN>`: 256 × 256 BC7 textures (`NMPLTEX1` wrapper, YKCMP_V1 zlib) | `Data/DATA/WF.dat` | 529 faces. Name is the picture's resource ID zero-padded to four digits (`swf0070`, `swf1220`) |
| **Effects** | OBF v2 models with legacy `nismultitexform` textures | `Data/DATA/<LANG>/EFFECT.dat`, banks `effect%03d.lzs` | Textures are indexed 4-bit (low nibble first), indexed 8-bit or BGRA 32-bit, some with several palettes |
| **Voices** <a id="voices"></a> | `.nlsd`: 28-byte header + RIFF MS-ADPCM mono, sample rate per line (about 22 kHz) | `Data/SOUND/<LANG>/voice.dat` (FAFS), members `en/<group>/voice_<line>.nlsd` | Line table in `SoundData_Voice_<LANG>.dat` ([data.md](data.md#sound-tables)) |
| **Music, sound effects** | Archives | `Data/SOUND/bgm.dat`, `se.dat` | Not documented |
| **Maps, objects** | Archives | `MAP.dat`, `MAPDATA.dat`, `OBJ.dat` | Not documented |
| **PS3 / Vita** | TXF (PS3), NMT (Vita), NSAC, NIS LZS | | Readable with [Scarlet](https://github.com/xdanieldzd/Scarlet); sprites and TXF with [NisAnim](https://github.com/xdanieldzd/NisAnim) (PS3) |

Timing: sprite animations run at 60 ticks per second, as in D5.

**Finding a character's sprite:** use the `char.dat` **`sprite`** column, not the character or class ID. Most rows
match (class 1190 → `anm01190`), but not all: Des X (ID 180) is drawn from `anm00050` and God's Part (230) from
`anm02040`; reading `anm00180` / `anm00230` finds only 3 motions instead of 76 and 62. Generic humanoids also share a
common motion block (`anm00001`, block 0) that their own bank adds to. Toro (240) and Kuro (250) have only seven
motions in D4's files. (**Verified offline**: read from the files, D5 Mod Kit fork animation work, 2026-10.)

## Compatibility with other games

| To | Asset | Works? | What it takes | Evidence |
|---|---|---|---|---|
| **Disgaea 5** | Class and character sprites | Yes | Convert the 16-byte node rows; put D4's complete poses into D5's template animations. D5 animations sometimes draw body parts on separate tracks, so only one main body track is kept per animation | **Community-reported**: 45 generic classes and 26 unique characters converted; installed and playtested 2026-10 |
| **Disgaea 5** | Palettes | Yes | D4's ten palettes map colour for colour onto D5's ten | same |
| **Disgaea 5** | Idle animation timing and weapon grip | Yes | Re-clock D4's idle frames (the Armor Knight's loop is 153 ticks) | **Verified offline (Kit)** |
| **Disgaea 5** | Voices | Yes, adapted | Convert to D5's format; map D4's voice categories to D5's situations (only six mapped so far) | **Community-reported** |
| **Disgaea 5** | Effects | Partly | Outer model converts; textures need re-encoding to D5's TXF. Rendering not yet accepted | **Verified offline (Kit)** for pixels only |
| **Disgaea 5** | Skill animations | Partly (2026-11) | 77 of 80 adapted specials convert to D5 scenes offline, with audio, draw layers and some backdrops left out and 98 prop or event-sprite actors drawn invisible. D4 scenes use 42 commands whose operands differ from D5's, and some mechanics (Lift Catch's carry) need D5 equivalents. See [functions.md](functions.md#d4-special-attack-scenes-as-a-source-2026-11) | **Verified offline**; 6 of 10 cast attacks played and matched their timelines (**Verified in game**, in D5 build B) |

**Where faces and cut-ins come from (2026-11, Verified offline).** A full read of `WF.dat` and `ANM_HI.dat` gives 529
faces and 740 cut-in members with no decryption step, and matches an earlier independent extraction pixel for pixel. A
named character's screen art is not always its own row: rows 180 and 220 use the pictures of resource IDs 54 and 74, and
an NPC row uses its source row's colour key (5301 uses 1154, 5305 uses 1014, 5388 uses 1184). Face and cut-in IDs are
therefore taken from the resource ID in the picture's name, not from the character row.

## Open questions

- Map, object, music and sound-effect formats.
- What the voice categories mean in game.

## Sources

- 2026-11 faces, cut-ins and scenes: D5 Mod Kit development fork, `docs/D4_IMPORT.md` (face and cut-in sources) and `docs/D4_SCRIPTS.md`.
- D5 Mod Kit development fork (Sean13128): `docs/D4_IMPORT.md`, `docs/D4_EFFECTS.md`, `docs/D4_BODY_IMPORT.md`, `docs/D4_VOICES.md`, `d5lib/d4source.py`.
- This project's D4 → D5 porting handoff (2026-10).
- [Scarlet](https://github.com/xdanieldzd/Scarlet), [NisAnim](https://github.com/xdanieldzd/NisAnim) by xdanieldzd.
