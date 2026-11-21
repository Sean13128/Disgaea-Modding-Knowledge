# Disgaea 4 — Data Tables

**Version:** Disgaea 4 Complete+ PC. **Stored in:** `Data/DATA/<LANG>/DATABASE_<LANG>.dat` (NSAC v2), one per language.
Each table is `u32 count` + fixed-size rows.

**Sources and evidence:** layouts come from the D4 → D5 import work in the D5 Mod Kit development fork
(`d5lib/d4source.py`, `docs/D4_IMPORT.md`) and this project's research. **Community-reported.** The reader's own
confidence marks are kept: "high" means the value matches the Disgaea wiki and stays consistent across all classes;
"medium" means consistent across classes, with the meaning taken from D5's matching field. Only the tables an importer
needs are mapped so far.

## Tables mapped so far

| Table | Row bytes | Holds |
|---|---|---|
| `char.dat` | 684 (`0x2AC`) | Characters and class ranks (957 rows in the EN database) |
| `mskill.dat` | 212 | Evilities |
| `magic.dat` | 356 | Skills and spells |
| `charhelp.dat` | 386 | Descriptions: `u16 id` + three 128-byte lines |
| `charPersonal.dat` | 70 | Generic-class personalities |
| `MinistryMap.dat` | 186 (`0xBA`) | The Cabinet (Netherworld ministry posts): 14 rows; row ID at `+0`, an event word at `+0x22`, the **evility the post grants** at `+0xB2` (row 0, Netherworld President, grants 500 Level Spurt) |

Row counts in the EN database: `char.dat` 957, `mskill.dat` 1,230, `MinistryMap.dat` 14. The archive has **73 members**; the others aren't documented yet. None is obviously a stat-growth table, so
where D4 keeps growth rules is **Unknown**.

Complete+ values can differ from earlier editions (e.g. the Masked Hero's ATK/HIT aptitudes are 90/90 in Complete+,
80/80 in an older copy). Read the edition you target.

## `char.dat` — characters and classes

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x004 | base stats | 8 × u16 | Order **HP SP ATK DEF INT SPD HIT RES** (D5 swaps RES and SPD) | high |
| 0x014 | learn_skill | 16 × u16 | Learnset: `magic.dat` IDs | high |
| 0x034 | learn_level | 16 × u16 | Level for each | high |
| 0x094 | weapon_skills | 16 × u16 | Weapon skills: code − 10000 = `magic.dat` ID (10301 → 301 Impaler Drop) | medium |
| 0x114 | weapon_skill_thresholds | 16 × u16 | Parallel learning thresholds (unit unknown) | medium |
| 0x194 | id | u16 | Character ID | high |
| 0x196 | sprite | u16 | Art sprite number | high |
| 0x19A | height | u16 | | medium |
| 0x19E | help_id | u16 | `charhelp.dat` ID | medium |
| 0x1A0 | color_id | u16 | | medium |
| 0x1A2 | grade | i16 (3–9) | The EXP-curve weight: `FUN_1400E5CA0` = cumulative EXP[min(level, 10000)] × this (D5's counterpart is `char.dat` `+224`). **From decomp** | medium |
| 0x1A4 | MOV | u16 | Thief 6, Ninja 5, Armor Knight 3 | medium |
| 0x1A6 | move_type | u16 | 1 for flyers | medium |
| 0x1A8 | attack_range | u16 | | medium |
| 0x1AA / 0x1AC | hl_value / exp_value | u16 | Rewards when defeated. Kill EXP (`FUN_1400F9750`) = curve delta × the class row's `+0x1AC` × **row 0's** `+0x1A2` / 50 | medium |
| 0x1B0 | rank_bonus | u16 | 10 + 3 per rank | medium |
| 0x1B2 | mana_value | u16 | 100, 85, 70 … per rank. `FUN_1400F6210` = level × this / 100, clamped 5–999 (10 when a flag is off) | medium |
| 0x1B4 | innate_evility | u16 | `mskill.dat` ID (Armor Knight: 392 Aegis). A class has one innate for all six ranks | high |
| 0x1B8 | magichange | u16 | | medium |
| 0x1BA | body | u16 | **Voice-group base**, not necessarily the art sprite | high |
| 0x1BE | evility_list | 16 × u16 | Evilities learnable at this rank (grows with rank) | high |
| 0x1DE | evility_flags | 16 × u16 | 1 per used entry | medium |
| 0x1FE | name | 64 B | | high |
| 0x23E | title | 64 B | | high |
| 0x27E | aptitudes | 8 × u8 | Same order as base stats | high |
| 0x28E–0x290 | res_fire / wind / ice | 3 × i8 | D4 has three elements (no Star) | medium |
| 0x292 | kind_gender | u8 | Same codes as D5: 11/12 human male/female, 21/22 monster | high |
| 0x294 | tier | u8 | Class rank | high |
| 0x296 | JMP | u8 | Ninja 35, Thief/Archer 25, Armor Knight 15 | high |
| 0x298 | body_type | u8 | 0 humanoid, 1 monster. **This byte, not gender, sprite or voice group, is what the game tests** (predicate `0x1401AEF10`; it drives model choice, body restrictions and the Magichange path). Some low-ID uniques that look humanoid are monsters by this byte | high |
| 0x299 | throw | u8 | | medium |
| 0x29A | counter | u8 | | medium |
| 0x29B | category | u8 | | medium |
| 0x29D | crit | u8 | Grows with rank | medium |

**Rows that aren't playable characters:** IDs **930–980** share one stat block, sprite 9000, voice group 980, no skills
and the generic innate evility 81 (Killing Hand); their titles read "WF". They include story forms such as Young
Valvatorez (950) and Young Artina (960). **990** (Genjuro Kazamatsuri) has no skills or evilities. Asagi has two rows:
**150** (innate 122 Heroine's Dream) and **160** (own sprite and voice, innate 828 Bullet Set, skills 10161–10163),
with the same base stats. Treat these as story or scene definitions, not recruitable units (**Community-reported**,
D4 expansion research).

Compared with D5 ([D5 data.md](../disgaea-5-alliance-of-vengeance/data.md)): IDs are 16-bit, there are no per-class
weapon mastery values and no weapon-type resistances, and evilities learnable per rank are listed in the row itself.

**More `char.dat` facts (2026-11, Verified offline):** `weapon_skills` `+0x094` (the per-character weapon forte lives
here), `magichange` `+0x1B8`, `kind_gender` `+0x292` (values of 10 and above occur, such as 21; D5 uses the value
mod 10; 13 with sprite 9000 marks story-only forms with no body, rows 930–980), `body_type` `+0x298` (0 humanoid,
1 monster, independent of `kind_gender`). A look is a body (`sprite`) plus a colour bank: **the bank is
`color_id % 10`**, so recolours are rows that reuse a body with another colour ID, and faces are keyed by colour ID.
A class is six rows (rank 0–5) with the same sprite and colour. There are 45 class families (25 humanoid, 20 monster).
Monster bodies carry 62–83 motions of their own; humanoid bodies 3–11, with the shared block supplying the rest.

**Weapon forte, 2026-11 (Verified offline).** The hundreds digit of a `weapon_skills` entry (code − 10000 = `magic.dat`
ID) names the weapon family: 1xx fist, 2xx sword, 3xx spear, 4xx bow, 5xx gun, 6xx axe, 7xx attack magic and 8xx
heal/support (both staff), 9xx tower skills. The set of families a row holds is its forte (Valvatorez 10: sword and
spear; Fenrich 20: fist and axe; Emizel 30: staff and bow; Sister Artina 500: staff only; Nemo 410: none).
`weapon_skill_thresholds` (`+0x114`) is the parallel learn threshold. A group written `108xx` is staff heal/support,
**not** a monster weapon (an earlier reading said so); monster rows have an empty list. The only per-row resistances are
the three at `+0x28E`; there are no weapon masteries or weapon resistances, so any mastery letter on a ported
character is the receiving game's template.

**Story and NPC rows that borrow another row's art (2026-11, Verified offline).** A look is a body plus a colour bank, so
a row can be a separate character on someone else's drawing. Examples measured against the original data: 990
(Genjuro) and 5414 (Fuka's alternate) use rows 10 and 40; 5403 uses Des X's body 50 in bank 4; Senator Kotaro (4300),
Task Force Member (5300), Alien (5303) and Senator Kijit (4400) use the Armor Knight (1080), Bouncer (1140), Android
(1200) and Wood Golem (2160) bodies; Aiko, Gracie, Erika, Duke, Sleepytime and the Office Worker (4310, 4340, 4330,
4350, 4320, 5302) use bodies 1060, 1010, 1040, 1070, 1100, 1100; Zommie V (3307) uses body 2080. Senators 4370 and 4380
(bodies 1180, 1120) have no sprite pack in any game this project holds.

## `mskill.dat` — evilities

| Offset | Field | Type | Meaning |
|---|---|---|---|
| 0x00 | mana | u32 | Mana cost |
| 0x08 | id | u16 | Evility ID |
| 0x0D | value | u8 | The number the description's `%d` shows (Aegis 30, Advance Guard 80, Bloody Battle 5). Every one of the 432 rows with a `%d` has it |
| 0x0E | | u8 | Second base byte the game copies into a unit's evility record with `+0x0D` |
| 0xCF | restriction | u8 | Copied into the unit's evility record (Aegis: 100) |
| … | name, description | text | |

The game reads more parameters at `+0x0D + index` (**From decomp**, see [functions.md](functions.md)). As in D5, *what*
an evility does is in the executable.

## `magic.dat` — skills

| Offset | Field | Type |
|---|---|---|
| 0x28 | id | u32 |
| 0x38 | description | text |
| 0xB8 | name | text |

The rest of the row isn't mapped.

Skill effect codes with no Disgaea 5 `magic.dat` equivalent (2026-11, **Verified offline**): 74 always counter, 81
aptitude up, 83 flying for 3 turns, 88 and 89 team-attack effects, 90, 121 extra move, 124 heal by fallen allies. Area
shape 1800 is a 3 × 3 with the centre kind 1 and eight cells of kind 2; what kind 2 means in D4 is **Unknown** (D5 reads
it as "the caster ends here"). Some learn lists name IDs with no table row (20071 / 20072, 20211 / 20212, 10344).

## Sound tables

`Data/SOUND/SoundData_Voice_<LANG>.dat`: 12-byte header `{u32 row size (64), u32 count, u32 0}`, then rows
`{u32 line, u32 group, f32 gain, s32 playback-rate input, 12 × u32 category flags}`. A generic class has three voice
groups (its first ID, +1, +2), one per personality. Japanese clips exist in separate JP sound tables and archives,
but there is no Japanese character database: both languages use the EN rows' voice group (`char.dat` `+0x1BA`). A few
characters' voice groups (for example 5405 and 610) appear in neither language's table.

**Silent characters (2026-11, Verified offline).** Politan (row 610, voice group 610) and Pringer X (row 210, groups 5405
and 210) have **no voice in D4**. Both dubs' tables hold 2,446 rows in 203 groups, group 610, 5405 and 210 have 0 rows
(a control, group 330, has 12), and each dub's `voice.dat` holds 2,446 members with none under those groups, so table and
archive agree one to one. Story-message voice (`SoundData_Msg_*.dat`, 8,974 rows keyed by message ID with no character
field) was not opened, so lines in story scenes are **Unknown**. An import that gives them a donor voice invents one.

## Voice situations

Each line row has 12 category flags; a line can play in situation *n* when flag *n* is set. Scripts play voices with
commands `4630` (by line) and `4632` (by actor), where `operand // 10000` is the situation. Meanings, from which code and
scripts use each one (**From decomp** + script survey; not measured in game):

| Situation | Role | Evidence |
|---:|---|---|
| 0 | Attack | Basic-attack script uses it |
| 1 | Special-attack shout A | Script-only; in every skill choreography family |
| 2 | Elemental cast | Skill wrappers |
| 3 | Special-attack shout B | Used only by skill choreographies and battle-event scripts |
| 4 | Cast release / healing cast | Skill wrappers |
| 5 | Lift | Carry code |
| 6 | Throw | Throw code |
| 7 | Move / landing | Post-hop code |
| 8 | Cutscene lines (not a skill shout) | 18 of 20 uses are cutscenes |
| 9 | Misc events | |
| 10 | Level-up | Code reading level / EXP |
| 11 | Menu / Recruiter preview | Flagged on every line |

**Mapping to D5's situations** (used by the D4 → D5 import): D4 0 → D5 0 (attack), 2 → 4 (elemental cast), 4 → 9 (cast
release / healing), 5 → 10 (lift), 6 → 11 (throw), 7 → 13 (move / landing), and the two shout slots 1 → D5 1 or 2 and
3 → D5 6 (D5 picks a shout situation per skill; its weapon-skill scripts name 0, 1, 2, 4, 6, 11 and 13). D4 has no
voice for damage, KO or idle (it plays a sound effect), and its level-up (10) and menu (11) situations have no
established D5 slot, so an imported unit stays silent there. Situation 11 holds a character's whole bank for the Recruiter
preview, so it isn't a battle trigger. Each row's gain field is a percentage (÷ 100). Of 1,232 imported lines, 30 have
identical English and Japanese recordings (mostly monster sounds).

**D4 has no voice for damage or KO**: it plays a sound effect there. Selector `0x140467400`; the arms are dispatched by
`0x14014F760`, which is also the script voice command's dispatcher.

## Open questions

- Every table besides the five above.
- The rest of `magic.dat`: effects, range, area, animation.
- What the voice category flags mean in game terms.

## Sources

- D5 Mod Kit development fork (Sean13128): `d5lib/d4source.py`, `docs/D4_IMPORT.md`, `docs/D4_VOICES.md`. (reader code not published; its results are the tables on this page)
- This project's D4 → D5 porting research (2026-10).
- 2026-11 additions (EXP / mana formulas): this project's research, D5 Mod Kit development fork `docs/IMPORT_YIELD.md`.
