# Disgaea D2: A Brighter Darkness — Data Tables

**Version:** PS3 BLUS31313, update 1.40. **Stored in:** `USRDIR/Data/START_4.dat` (NISPACK). **Evidence:**
**Community-reported** from the D5 Mod Kit development fork's D2 reader, which checks counts and sizes and was
cross-checked row by row. "high" = self-evident and holds for every row; "medium" = consistent across all rows but read
from the data's shape, not from code.

Tables: big-endian `u16` count, two unused bytes, then rows. Text is UTF-8.

## Tables mapped so far

| Table | Rows | Row bytes | Holds |
|---|---|---|---|
| `char.dat` | 558 | 676 (`0x2A4`) | Characters and class ranks |
| `magic.dat` | 386 | 296 (`0x128`) | Skills and spells |
| `mskill.dat` | 227 | 232 (`0xE8`) | Evilities |
| `charhelp.dat` | 256 | 278 | Descriptions |

Older patch levels (`START.dat`, `START_1.dat`, `START_2.dat`) hold different versions of `magic.dat`, `mskill.dat` and
`charhelp.dat`.

## `char.dat`

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x004 | base | 8 × u16 | Level-1 stats (Warrior 29/6/16/16/6/8/12/8) | medium |
| 0x014 | learn_skill | 16 × u16 | Learnset: `magic.dat` IDs | high |
| 0x034 | learn_level | 16 × u16 | Levels | high |
| 0x094 | weapon_skill | 16 × u16 | Weapon skills: `magic.dat` IDs (Warrior 201–203 = sword 1–3) | medium |
| 0x194 | id | u16 | Class / character ID (the game's code reads `row + 0x194` as the class ID) | high |
| 0x196 | body | u16 | The row's renderer resource; equals `+0x1BC` on 434 rows | high |
| 0x19C | help_id | u16 | `charhelp.dat` ID (539 of 558 resolve; the rest are dummies) | high |
| 0x1BC | body_variants | 3 × u16 | The game reads `+0x1BC` / `+0x1BE` as primary / alternate resources | medium |
| 0x1FC | name | 52 B | | high |
| 0x230 | title | 52 B | | high |
| 0x274 | aptitudes | 8 × u8 | | high |
| 0x28B | palette | u8 | 0–5 (class ranks 0–5 on ranks 0–5; Virunga 5) | high ([code](#battle-fields-in-the-chardat-row)) |
| 0x19E | face selector | u16 | Picks the face atlas, row and (with rank) column; see [assets](assets.md#cut-ins-and-face-icons) | medium |
| 0x1B8 | voice key | u16 | Key into the voice selector ([Voices](#voices)) | high |
| 0x28E | voice override | u8 | Non-zero replaces the variant argument by its byte − 1 | high |
| 0x28F | visual type | u8 | 0 humanoid (242 rows), 1 monster (237), 2 prop (79) | high |

The battle fields (movement, resistances, aptitudes) are listed [below](#battle-fields-in-the-chardat-row).

Stat order is HP SP ATK DEF INT RES HIT SPD.

### Battle fields in the `char.dat` row

**From decomp** (the unit builder at `0x10E77C`, clamp helpers `0x1077E4`..`0x1078A4` and the status screen at
`0x25D998`, 1.40), measured over all 558 rows, **Verified offline** against a community character guide where noted.
Offsets are within the 676-byte row. All values are single bytes unless a width is given.

| Offset | Field | What the code does | Check |
|---|---|---|---|
| +0x000 | immunity mask (i32) | Copied to unit `+0x1160`. Xenolith `0x0FFF`; Pringer Z and Baal Slave `0x0FFFFFFF`; 0 for the rest. **Bit meanings not decoded** (Disgaea 5 uses bits 0 to 6, 0 to 13 or all, so the bit order can't be assumed equal) | code + data |
| +0x1A2 (u16) | MOV | Unit `+0x1170`; clamp 1 to 31; first on the status screen | code |
| +0x1A4 (u16) | flying flag, 0 or 1 | Unit `+0x117E`. 1 on the angels, Succubus, Mothman, Death, Shark Dragon, Sea Angel, Virunga, Lilliel, Desco, Gig, Metallia. Disgaea 5's equivalent also has a value 2 (Bits) never seen in D2 | code + data |
| +0x1A6 (u16) | innate attack range of monsters (1 for most) | Unit `+0x1180`; third on the status screen | code |
| +0x1A8, +0x1AA, +0x1B0 (u16) | HL, EXP and Mana reward bases | **Inferred** by pattern only (no code reader) | **Unknown** |
| +0x274..+0x27B | aptitudes HP to SPD | Unit `+0x1514` | code |
| +0x27C..+0x282 | weapon aptitudes: fist, sword, spear, bow, gun, axe, staff | Unit `+0x1146`; monsters hold 0 | code + data |
| +0x283 | an eighth weapon value (casters 15 to 16, fighters 2) | Not copied; unit gets a constant 10 at `+0x114E` | **Unknown** |
| +0x285 / +0x286 / +0x287 (signed) | resistance percent: **Fire, Wind, Ice** | Unit `+0x1168`..`+0x116A`; the damage code at `0xF5524` picks `+0x116B + element − 1` for spell element 1, 2, 3 and multiplies by `100 − value` (positive resists). There is no Star byte | code, plus the guide below |
| +0x289 | kind and gender | Tens digit 0 unique, 1 human class, 2 monster class; ones digit gender (Laharl 1, Warrior 11, Lantern 21, Barbara 2) | code |
| +0x28C | constant 10 on every row | Unit `+0x117C` | code + data |
| +0x28D | JMP (Laharl 20) | Unit `+0x116E`; clamp 10 to 99; equipment bonus × 5; second on the status screen | code |
| +0x290 | throw distance | Clamp 1 to 9; meaning inferred from clamp and screen order | code, inferred |
| +0x291 | counter count | Clamp 0 to 9; inferred | code, inferred |
| +0x292 | critical rate | Clamp 0 to 99; equipment bonus × 5; the byte is the percent the status screen shows (Laharl 6) | code, inferred |
| +0x298 | 1 on uniques, 2 on classes | **Inferred**; no code reader | **Unknown** |

The status screen lists MOV, JMP, range, throw, counter, crit in that order. The magic table's element byte is at
`magic.dat +0x111`: Fire 1, Wind 2, Ice 3, Star 4 (the base spells 1001, 1101, 1201, 1301; measured over all 386
rows). Disgaea 5's own rows for the old story characters (Laharl, Etna, Flonne, Fuka, Valvatorez, Petta, Desco) are
identical to D2's in every field compared.

**Cross-check against a community guide** (**Community-reported**: [Disgaea D2: A Brighter Darkness Character
Guide](https://gamefaqs.gamespot.com/ps3/687861-disgaea-d2-a-brighter-darkness/faqs/68086) by Guilu, 67 characters
and classes compared by name; read 2026-11-23). The guide's numbers equal the row for: throw 42 of 42 (monsters have no
throw), counter 67 of 67, crit 67 of 67 (class rows inside the guide's per-rank range), JMP 67 of 67, flying 67 of 67,
MOV 66 of 67 (Lanzarote: row 5, guide 4), monster range 25 of 25, and the Fire / Wind / Ice order on 65 of 66 (the
miss is Valvatorez, where Disgaea 5's own row equals the table, so the guide entry is the outlier). The guide's weapon
grade letters map to disjoint byte ranges: S 25, A 20, B 15 to 18, C 10 to 14, D 5 to 9, E 2 to 4.

**Not mapped:** `+0x1A0` (u16, 3 to 6) is read as a multiplier by `0x18F50`; `+0x1AC`, `+0x1AE` (6 to 14, 10 to 18),
`+0x1B2`..`+0x1BA`, `+0x28A`, `+0x293`..`+0x296`, `+0x29A`..`+0x2A3` have no confirmed meaning (`+0x293` and `+0x29A`
track weapon type and class kind). The row holds no evilities, size, height, armor or monster weapon aptitude, and no
weapon resistances.

**Growth:** when a unit is created, the game takes each base stat word's **low byte** as that stat's initial growth
seed (`func_00109FF0`, 1.40), and copies the eight aptitude bytes (`+0x274`) into the unit at `+0x1514`
(`func_0010E77C`) (**From decomp**: the native port's recompiled code). The full growth formula isn't established.

### Looking up rows, and what limits adding one

(**From decomp**: the native port's recompiled 1.40 and 1.00 code; nothing here was tested by adding a row.)

- **The lookup is count-driven.** The table binder stores `{count, rows = file + 4}` (`func_0034FE34`) and the lookup
  (`func_0034F8D8`) rejects ID 0, walks `count` rows comparing `+0x194`, and returns the row index or −1. There's no
  fixed 558-row array or clamp in either. Duplicate IDs: the first match wins.
- **An unknown ID is not checked later.** When a unit is refreshed (`func_00020CAC`) the game looks up its class ID
  (unit `+0x1158`) and caches the row index as a u16 at `+0x115C`; a miss stores `0xFFFF`, and `func_00015E18` then
  reads `rows + index × 0x2A4` with no bounds check. Giving a unit an ID with no row reads outside the table.
- **Dummy rows:** 66 rows are named dummies: unique 650–700 (step 10), generic human 1230–1275 and generic monster
  2210–2255. They're filled-in rows, and none is proven unused; 2235 is the only one with no raw reference elsewhere in
  the `START` tables.
- **IDs span 10–9001** with 8,434 gaps; the gaps aren't free slots.

**Other limits the game has** (separate from how many rows `char.dat` has):

| What | Limit | Where |
|---|---|---|
| Party roster | **128** in total: free slots = 128 − two counters at save root `+0x1507EC` / `+0x1507EE` | `func_0002E218` |
| Saved units | Records at root `+0x598` + slot × `0x1A60` (identity at `+0x1202`); the party lookup `func_001074C8` reads the count at `+0x1507EC`. The field leader is read from a **different** root (`func_0002E9B8`, `+0x150AA6`) | `func_0001AF60` |
| Battle actors | 160 descriptors (`0x13C` bytes each) | `func_00015490` |
| Sprite loading | Four descriptor pools of 138 / 1,047 / 1,000 / 50 (2,235 in all, `0x24` bytes each) | `func_00180F74`, loads in `func_00181438` |
| Second visual key (`+0x19E`) | Must be in a fixed list of 870 keys built into the executable (869 distinct); a miss gives −1, cached at unit `+0x118C` | `func_000548D4`, search `func_0010B8B0` |

### Which picture a unit uses

- **Sprite file:** `anm` number = row `+0x196` (signed) **+ 10,000**, plus the unit's colour byte (`+0x1183`) when it
  applies; the loader names it `anm%05d.lzs` or `anm%05d.dat` (`func_00109350`). Etna (ID 30): `anm00030.lzs` is her
  body sheet (nine 512 × 512 pages and five palettes), `anm10030.lzs` her portrait parts.
- **Colour byte `+0x1183`** drives the body, the face and the status illustration together (the Change Colors menu
  sets it).
- **Body selector `+0x117A`** (signed): `func_00109E44` picks the body resource at row `+0x1BC + 2 × selector`, so
  rows with a second resource at `+0x1BE` (for example Rosalin 410/411, Priere 520/521) can switch bodies. Menu code
  (`func_0027B448`, `func_0027B644`) writes it, next to a scan of 74-byte rows keyed by class family with a selector at
  `+4`, which matches `charPersonal.dat`'s row size. The selector has other consumers, so it isn't purely cosmetic.
- After a load, `func_00067C28` resets a unit's cached primary body from row `+0x1BC` without reading the selector,
  so an alternate body has to be re-applied after loading.
- **Cached body pair** at unit `+0x11D8` / `+0x11DA` (Etna: 30, 31) is stored in the save, separately from the
  selector, so a save edit has to change both. Unit `+0x11FC` holds the class family (class ID ÷ 10).
  `func_0027B1E0` scans the 74-byte personality rows (group u16 at `+2`, selector byte at `+4`) for
  `group × 10 + selector` equal to the cached body.
- The renderer (`func_00029804`) also resolves the body through row `+0x196`, so `+0x196` feeds both body and
  portrait paths, not only portraits.
- **An appended visual row works:** a cloned `char.dat` row (559 rows in every table variant) used only as a visual
  alias with its own sprite (`anm00900.lzs`) loaded and drew Etna in battle while her gameplay class stayed 30
  (**Verified in game**, D2 native port, 2026-10). That's a renderer-only addition, not a new playable class.

**Which `anm` file is which picture (Etna, 30):**

| File | Holds |
|---|---|
| `anm00030.lzs` | Battle and field body (ten pages, five palettes) |
| `anm00031.lzs` | Secondary full-body poses (three pages) |
| `anm10030.lzs` | The large **Status screen** illustration (one page, two palettes), confirmed by recolouring it in game |
| `anm30030.lzs` | A separate 768 × 512 full-body illustration page; where it shows isn't confirmed |
| `anm40030.lzs` | A compound container (blocks 10 / 30 / 50, 29 pages), not one face |

The small battle HUD face and list icons come from a face atlas (`wf_unique1` in the base `START.dat`), not from the
body sprite's palette. An anm file's own resource ID sits at offset 36 of the decompressed file; `anm00001.lzs` (shared
motions) has 12 resource blocks. The seven `ANM_HI` archives hold 1,966 / 126 / 27 / 31 / 95 / 29 / 27 members.

## Evilities

Evilities **aren't listed per row** in `char.dat`. `mskill.dat` holds three evility IDs per class family base
(1010–1210 humans, 2010–2200 monsters, step 10) and one or two per unique character (10, 30/31, 70/71, 80/81 …).
The ID numbering is the only link the files show (**inferred**, with a known blind spot for alternate unique rows).

**`mskill.dat` row (2026-11, Verified offline from bytes and text; the game's formatter wasn't run):** `+0x08` ID (u16,
big-endian), `+0x0C` value (u16, the description's first `%d`), **`+0x0E` value 2 (u8, the second `%d`, nearly always
the stated maximum)**, name from `+0x0F`, description from `+0x5F`. 29 of the 227 rows have a two-number description
and all 29 have a non-zero value 2 (examples: 10 / 200, 5 / 100).

More on `mskill.dat` and neighbours (2026-11, **Verified offline**): every row's first 8 bytes are zero; `+0xE3` is 100
on all 227 rows; `+0xE4` takes 32 distinct values and seems to group evilities by kind of effect (a hypothesis; no
dispatcher was traced). **No table encodes "angel" or "flat"** (the conditions of evilities 140 and 550): a per-byte
and per-bit census of `char.dat` (558 rows × 676 bytes) finds no field that isolates the angel characters. **Updated
2026-11-17:** both groups are hard-coded in the executable (below), which is why no table holds them. `charPersonal.dat` is 130 rows × 74 bytes (ID,
category byte, name) of generic traits, and nothing in `char.dat` points at it. `char.dat` `+0x1B0` is a selector for
the body-animation IDs at `+0x1BC`; `+0x2A0..+0x2A3` are per-row flags (unique, object, alternate palette).

### Angel and flat groups (evilities 140 and 550)

**From decomp** (the decrypted executable, disc and update 1.40 traced separately and agreeing, 2026-11-17).

- **Angel (140, Limitless Love).** A unit counts as an angel only when its class ID ÷ 10 is 117 or 118: the 12 generic
  rows, Male Angel 1170–1175 and Female Angel 1180–1185. **No named character qualifies**: Flonne, Pure Flonne, Sicily,
  Virunga, Artina and Lilliel are not angels to this evility, and neither is Item King (3543, which shares the Female
  Angel body). The angel gets +50% (mskill value) **per other same-side battle actor holding evility 140**, searched
  over the whole field; the angel itself is excluded. The same check runs in the stat pass and the status-screen
  breakdown, and no other code gates evility 140 on unit type.
- **Flat (550, Flat Alliance).** The Nisa holder gets +5% per **other** unit in the party roster that passes a "flat"
  test (the roster array is at save root `+0x598`, stride `0x1A60`, count at `+0x1507EC`). The test returns true for:
  - named IDs 30, 40 (Etna), 70 (Sicily), 150, 270, 310, 320, 4305 (Pleinair), 340, 350, 440, 470, 500, 550;
  - Flonne 50, **only while game flag `0x8C4` is clear** (that flag shows her as Pure Flonne; when it is set the game
    copies row 60's name, title and body/help fields into row 50; flags `0x8C5` and `0x8C3` do the same for Laharl
    10 ← row 20 and Etna 30 ← row 40);
  - eight class families by ID ÷ 10: Valkyrie 102, Witch 104, Thief 109, Archer 111, Shaman 120, Slumber Cat 206,
    Alraune 210, Sea Angel 215 (all six ranks each);
  - any NPC row whose `char.dat` `+0x196` (body / family) is 1020, 1040, 1090, 1110, 1200, 2060, 2100 or 2150.

  That is 62 playable rows plus 23 NPC and shop rows; Pure Flonne, the angels and every other family are not flat.
  If unit byte `+0x1188` is non-zero the count runs over a second unit table instead (probably the other side); what
  that means for Nisa is **Unknown**.
- Not traced: which stats the angel bonus feeds, and which units the field search counts as present (see
  [functions](functions.md)).

### Signature evilities read from the executable

**From decomp** (disc executable, 2026-11-13 to 11-14). Numbers are the `mskill.dat` value bytes; addresses are on
[functions.md](functions.md). The evility descriptions aren't reproduced here.

| ID | Name | What the code does |
|---:|---|---|
| 300 | Heavy Stance | A pure predicate: the "can be lifted" test returns false for a holder, and a second candidate predicate also skips units holding 580. Nothing reads 300 as a stat or reads a value for it |
| 1210 | Rapid Movement | In the defender's evasion pass, after the hit percent is clamped to 1..100, subtracts value 2 × panels moved this turn (unit `+0x522`), the product capped at 64. Misty Haze (1150) multiplies instead of subtracting. D5's Rapid Movement (1060) has the same order and cap but number 3 |
| 320 | Acceleration Mascot | +1 to the move stat (character data `+0x1171`) for each **other** unit on the same side holding it, anywhere on the field; holders don't count themselves, and holders stack |
| 420 | Healing Barrier | Every unit on a side with a holder (the holder included) recovers 2 % of max HP at **each step** of a walk, immediately, times the number of **other** holders, plus 2 % if the unit holds it itself |
| 480 | God of Destruction | At the end of the holder's turn, the number of victims is a random 1..5 (value 5 is the maximum); victims are distinct hostile units; the action is a dedicated skill row |
| 570 | Brave Battle Princess | Each holder's own count of defeats it made (u16 at its character data `+0x19CE`, incremented on the attacker when a target is defeated) is summed over the **other** same-side holders, × 10, capped at 200, and added to the attack bonus. A holder's own count does not help herself |
| 80 | The Artifact | **Unknown**: no call reads evility 80 or its value through the accessors every other evility uses, so where the effect acts, and whether it is the holder's or the attacker's HP, wasn't found |

## Voices

**Status (2026-11-14): Verified offline.** Static call-site maps of the executable, plus battle runs of the native
port's recompiled build with the selector logged (about 600 picks in 12 runs, nothing heard). They replace the
2026-11-10 column labels, several of which were guesses from matching recordings against Disgaea 5.

- **Storage:** `SndPakVoiceEN_N.pak` / `SndPakVoiceJP_N.pak`: big-endian `VAGp` PS-ADPCM, 22,050 Hz. Both dubs; the
  English dub has fewer lines (4,739 routed clips against 8,631).
- **Selection:** the executable holds a selector table of **540 rows × 19 columns**, keyed by (category, voice key,
  generic variant). The voice key is `char.dat` row `+0x1B8`. A generic class has three variants.
- **Three ways a line plays:** the selector wrapper (27 native call sites, all located), the script voice commands (803
  literal calls, slots 0–10 only, plus 6 with a variable operand), and a direct-play command that names one clip and
  skips the selector (339 calls in 80+ events; for example one unique's finishing line).
- **Category 0 is battle.** What each column was seen doing:

| Column | Situation | Basis |
|---:|---|---|
| 0 | Normal attack, counter | Seen with 5+ voice keys; skill scripts use it too |
| 1 | Skill cue (second cue of a special) | Seen with 4 keys, only inside special skills |
| 2 | Unit deployment (every dispatch) **and** a spell cast at a hostile target | Deployment seen with 7 keys; spell with 3, weak on keys |
| 3 | Support attack (the supporter plays 3, then 0) | Seen with 4 keys, launched by the support-attack event |
| 4 | Skill cue (late cue of a special) | Seen with 3 keys |
| 5 | Heal-type spell cast at an ally | Seen with 2 keys; HP rose each time |
| 6 / 7 | Lift / throw (and two release variants) | Seen as controls every run |
| 8 | One cinematic event only | No native caller |
| 9 | Skill dash cue, and the first step of a walk after a height change | Seen with 2 player keys; walk step only for an enemy |
| 10 | Level up (cast-time and EXP-gain paths), and the reaction to being the target of an ally's spell | Level up with 1 key; reaction with 3 keys |
| 11–17 | Nothing in category 0 asks for them | They occur only as category 1 and 2 columns |
| 18 | Whole-bank preview | Menu and unit screens |

- **Disgaea D2 voices neither damage, defend nor death in battle.** Damage taken, KO of either side and defend
  produced no selector pick and no direct play in any run, and no native caller exists for them. Evade (a visible
  MISS) was never reached, so it is **Unknown**. D5 reused some of D2's recordings as its own damage and death lines
  ([D5 assets](../../mainline/disgaea-5-alliance-of-vengeance/assets.md#voice-situations)).
- **Category 1 columns 13–17 and category 2 columns 11 / 12** are requested only by pad-polling interaction screens and
  a paired-actor state machine (a signed value at `+0x6DA` of a menu actor picks among the five mood buckets). They
  never fired in battle, and their purpose is **Unknown**; they can't be reached from a save without the warp the
  tests used, because loading a save with DLC content shows an "unauthorized additional content" refusal in the port.
- The join "which skills call which column, by skill kind" can't separate the columns: it can't reproduce the four
  columns proven natively, because those are played by engine code, not by skill scripts. Don't use it as evidence.
- Not covered: nothing was heard, slots 4 and 9 beyond skill cues, and whether slots 2 and 5 depend on target side or
  spell element (only Heal, Fire and enemy casts were seen).
- **Silence** (**Verified offline**, rule proven with a test): no selector row exists for voice key 300 (Pringer Z) or
  580 (the Baal series, including Baal Slave), nor for 93 other non-dummy rows; 153 `char.dat` rows in all have no
  selector key (66 of them dummies, plus props, Pringer X and Z and the Baal series). Voice key 9999 has no `char.dat`
  row. Nothing plays when no row matches or the list is empty. Category 1 IDs have Japanese routes but **no English
  route**, so the English dub is silent for them (8,581 category-1 list entries have no English route).
- **Update 1.40 changes 48 English clips** (voice banks 7, 21, 160 and 161), which affects Barbara and Xenolith's
  category-2 IDs; which column each replaced clip serves wasn't traced.
- The call chain, route tables and addresses are on [functions.md](functions.md#sprite-animation-and-cut-in-code-140-addresses-2026-11-24-to-11-25).
  Columns 1, 3, 4 and 8 have no direct native caller and are reached only from scripts (opcode `0x4632`); what each
  caller does stays inferred from the logged runs above (**Unknown** as proof).

## Game state in memory (1.40)

The native port's Cheats menu and Item Editor read and write the live game state, using the game's own helper
functions (**Verified in game** by the port; fields checked against the item constructor `func_00062CD0` and recalc
`func_000619CC`).

**Limits:** 999 item slots in the bag/warehouse pool (at save root `+0xDD518`; the game's own count at `+0x13EE08`),
4 equipment slots per unit (at character `+0x10`), 6 innocents per item, up to 128 party units and 255 skills per unit.

**Item record** (0x190 bytes, big-endian):

| Offset | Field |
|---|---|
| `0x04 + 8k` (k < 6) | Innocent k: `u32 level`, `u16 type`, `u8 variant`, `u8 subdued` |
| 0x34 | `u32` derived value (recalculated) |
| 0x38 | 8 × `s64` stat totals (recalculated) |
| 0x78 | 8 × `s64` base stats |
| 0xB8 | Item ID |
| 0xBA | Level |
| 0xBC | Item World floor limit (29 / 59 / 99) |
| 0xC0 | Grade + 1 |
| 0xCC | Counter |
| 0xD2 | Rarity |
| 0xD3 | Type |
| 0xD4 | Icon |
| 0xD5 | Innocent slots (grade + 4) |
| 0xD6 / 0xD7 | Move / Jump |
| 0xD8 | Rank |
| 0xD9 | Range |
| 0xDC | Critical |
| 0xDF | Grade, from rarity (`func_00054AF8`): 0–7 Legendary, 8–31 Rare, 32 and up Common |
| 0xF1 | Name (48 bytes) |

Stat order: HP, SP, ATK, DEF, INT, RES, HIT, SPD. Item icons are 32 × 32 cells of `item.txf` (ARGB1555, big-endian) in `START.dat`.

## Open questions
- The rest of `magic.dat` and `mskill.dat`.
- Item, map and shop tables.
- What changed between patch levels.
- The immunity mask's bit order; the meaning of the unmapped `char.dat` bytes above; what HL, EXP and Mana bytes really are.

## Sources
2026-11-20 to 11-25 additions (battle fields, community cross-check, voice silence): the D5 Mod Kit development fork's reads of the decrypted 1.40 executable and `START_4.dat`, and the community guide cited above.
D5 Mod Kit development fork (Sean13128): `d5lib/dd2source.py`; (reader code not published; its results are the tables on this page) this project's DD2 source-pack research (2026-10).
2026-11 additions (voice selector, `mskill.dat` values): D5 Mod Kit development fork `docs/SOURCE_VOICES.md`, `docs/EVILITIES.md`.
2026-11-12 to 11-20 additions (voice columns, angel and flat groups, signature evility rules): the D5 Mod Kit development fork's offline reads of the decrypted executable (angel / flat members from `char.dat` of the verified `START_4.dat`) and its logged runs of the native port's recompiled build. **From decomp** / **Verified offline** as labelled.
