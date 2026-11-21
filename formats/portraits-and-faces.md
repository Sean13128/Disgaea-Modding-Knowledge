# Portraits, cut-ins and face icons, by game

Where each game keeps its character art outside the battle sprites, and how a character finds its own.

**Evidence:** **Verified offline** by the readers of this project's Character Forge and D5 Mod Kit (each row was decoded
from the original files); identity rules are as observed, not traced in every case.

| Game | Illustrations / cut-ins | Face icons | How a character picks its art |
|---|---|---|---|
| **Disgaea PC** | `DATA.DAT` `BU` / `NEW_BU` story pictures (TX2) | `SUBDATA.DAT` → `START.DAT` member 61 (64 × 64) | |
| **Disgaea 1 Complete** | anm pack (`start.dat` member 98): 336 × 1080 cut-ins; `DATA.DAT` `new_bu` (NLTX) story pictures | **HD faces:** `SUBDATA.DAT` member `anmdat.fad`, 268 `swf####` textures, 256 × 256 ([FAD](fad.md#disgaea-1-complete-anmdatfad-the-hd-faces)). `start.dat` member 73 is an older PC-era set, not these | Cut-in block = HD body + 1 + palette ([D1 assets](../games/mainline/disgaea-1-hour-of-darkness/assets.md)). Face: `swf%04d` of sprite + palette from the character row, four exceptions |
| **Disgaea 2 PC** | `DATA.DAT` → `CUTINPACK.DAT` (ANM; e.g. 512 × 1024) | `DATA.DAT` → `WFPACK_PC.DAT`, `SWF<body>.TXP` (256 × 256) | By body number. Members are IMY-compressed, addressed by chunk ranges |
| **Disgaea 3** (Vita) | `CI.dat` (382 ANM members) | `START.dat` → `WF.dat` (145 textures) | `char.dat` `+0x19C` portrait, `+0x19E` colour |
| **Disgaea 4 Complete+** | `ANM_HI.dat` groups `10000 + body` (e.g. 390 × 1248), ten colour variants per class | `WF.dat`, `swf<body>.nltx` (256 × 256): an older NLTX header, YKCMP type 7 (zlib), BC7 pixels | By body; colour variant per palette. Names are zero-padded: `swf0070`, not `swf70` |
| **Disgaea D2** | `ANM_HI*.dat` illustration groups | `START*.dat` (96 × 96 cells; compressed TXF) | `char.dat` `+0x19E` picks the face bank; `+0x196` picks the status art, which can differ from the drawn body |
| **Disgaea 5 Complete** | anm files `10000 + sprite_id` | Face bank 9017 (204 portraits) in UI textures | `char.dat` `sprite_id` (+ colour variant for generics) |
| **Disgaea 6 Complete** | `data/data.dat` (FAFULLFS) → FAD texture tables; six-digit illustration IDs match the model's four-digit character family (e.g. five expressions) | 338 × 338 faces | Model → character; `windowFaceID` |
| **Disgaea 7** | `data.dat` tables chain model → character → BU anime resource → named BU sequence; FAD / FANM parts layer body and expressions | `windowFaceID` → `icon` table crop → `texture` | As left |
| **Disgaea RPG** | `images/chara` `ci` | `images/chara` `face` | Unity bundles by character ID |

## Notes
- **Disgaea 1 Complete's start.dat member 73 is not its HD face set.** It holds 277 IDs (the older PC-era faces, the
  Disgaea PC 64 × 64 pixel faces redrawn at 256); the 268 faces in `anmdat.fad` are the redrawn ones, and only they
  match bodies redesigned for D1 Complete (the reaper, the rifle demon, the skull dragon) (**Verified offline**, 2026-11-18).
  Earlier versions of this page named member 73 as D1 Complete's face icons; corrected.
- **Disgaea 4:** a full pass over `WF.dat` and `ANM_HI.dat` gave **529 faces** (256 × 256) and **740 cut-in members**,
  with no errors; `WF.dat` is language-neutral. Some IDs have no face member (1251 to 1259 in the build checked)
  (**Verified offline**, 2026-11-19).
- A face icon isn't a cut-in: D6/D7 select them separately.
- All of these keep transparency; D4's illustrations have a plain white background instead.

## Sources
This project's Character Forge artwork notes and readers (2026-10); (reader code not published; its results are the tables on this page) D5 Mod Kit; game pages linked above.
