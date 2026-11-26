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
| **Disgaea 4 Complete+** | `ANM_HI.dat` groups `10000 + body` (e.g. 390 × 1248), six rank colours per class (cut-ins are per colour, faces per class) | `WF.dat`, `swf<body>.nltx` (256 × 256): an older NLTX header, YKCMP type 7 (zlib), BC7 pixels | **Face:** the class face for every rank and NPC row (colour ID rounded down to tens); the colour ID itself only for IDs below 1000 with their own face. **Cut-in:** `10000 + sprite`, plus the unit's colour byte for class and NPC rows. Names are zero-padded: `swf0070`, not `swf70` ([rules](../games/mainline/disgaea-4-a-promise-unforgotten/assets.md#faces-and-cut-ins-which-picture-a-character-gets-2026-11-offline)) |
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
- **Disgaea 4 face rule, corrected 2026-11-25.** An earlier version of this page said a D4 character's face varies with its
  colour variant ("colour variant per palette"). The game's own selection function returns the class face for all six rank rows
  of a class and for NPC rows borrowing a class (Armor Knight rank 1, ID 1081, shows `swf1080`; Chief 1154 shows `swf1150`);
  `WF.dat` does hold recoloured faces (`swf1081` and so on), but no code path requests them for those rows. The cut-in is the
  reverse: it adds the colour byte, so each rank has its own cut-in member (**Verified offline**: the game's functions run in an
  emulator over the real character table, 154,613 face cases and 957 rows × colour bytes 0 to 6 for cut-ins, no mismatches).
  A cut-in file is one 512 × 1920 indexed page with one palette; its motion 8000 draws cell 1 (390 × 1248).
- **Disgaea 1 Complete cut-in rule (From decomp, Switch v0).** The skill cut-in function (`0x7100056838`) selects HD block
  *hd block(sprite) + palette + 1* (animation 8091, scale about 0.254) from the character row's sprite and palette fields;
  23 HD rows have no such block (cutscene rows, Jn:Revamp, Carter). Dark Flonne's row has sprite 3 and palette 0, so the rule
  gives Flonne's cut-in (block 31); block 32 is a separately authored Dark Flonne picture that the rule does not reach, and how
  the game reaches it was not traced (**Unknown**). The face key `swf%04d` (sprite + palette; special IDs 2300, 2301 and 2302,
  2918 and 3190) was reproduced by running the game's function in an emulator (**Verified offline**).
- A face icon isn't a cut-in: D6/D7 select them separately.
- All of these keep transparency; D4's illustrations have a plain white background instead.

## Sources
This project's Character Forge artwork notes and readers (2026-10); (reader code not published; its results are the tables on this page) D5 Mod Kit; game pages linked above.
