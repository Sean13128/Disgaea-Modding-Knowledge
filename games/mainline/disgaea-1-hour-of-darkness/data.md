# Disgaea: Hour of Darkness — Data Tables

**Versions:** Disgaea PC and Disgaea 1 Complete. **Evidence:** **Community-reported** from the D5 Mod Kit development
fork's D1 readers (which cross-check the two editions against each other) and pg_disatools' published structures.

D1 has **no evilities**: they arrived in later games. Classes and characters only have stats, skills and spells.

## Where tables are

| Edition | Location |
|---|---|
| Disgaea PC | `SUBDATA.DAT` → `START.DAT` (PSPFS, IMY chunks). `magic.dat` is member 41 |
| Disgaea 1 Complete | `SUBDATA.DAT` → `start.dat`. `magic.dat` is member 40; the character table is member 43 |

pg_disatools edits these PC tables: `DUNGEON`, `HOSPITAL`, `CHAR_C`, `CHAR_K`, `MUSICSHOP`, `MAGIC`, `HABIT`,
`CHARHELP`. Its format definitions are extendable files under `resources/dataFiles`.

## `magic.dat` — skills and spells

285 rows in both editions. PC rows are 172 bytes; Complete rows are 180 bytes (wider text fields). The numeric block is
the same in both.

| Field | Type | Meaning |
|---|---|---|
| id | u16 | Skill ID |
| level | u16 | Weapon-mastery tier needed; 0 for spells and unique skills |
| power | u16 | Raw table units (not a percentage) |
| sp | u16 | SP cost |
| name, description | Shift-JIS text | PC: `name[33]`, `description[113]` |
| effect parameters | 5 × u8 | The first steps by 2 per spell tier |
| weapon type | u8 | 1 fist … 6 axe; 0 for spells and uniques |
| element | u8 | 0 none, 1 fire, 2 wind, 3 ice, 4 star |
| category | u8 | 2 physical, 3 attack magic, 4 heal, 5/6 support |
| weapon type (alt) | u8 | Equal to weapon type in every row |
| range | u8 | 0 for all common weapon skills and spells; 2–7 for 13 area uniques |
| pattern | u8 | **Target-panel pattern number**; 0 = the spell rule (area comes from the spell level). See below |
| up, down | u8 | Vertical reach (6–48) |
| effect codes | 5 × u8 | |

**Where a special's reach lives (2026-11).** In the Complete (180-byte) row: power `+0x04`, SP `+0x06`, range `+0xAB`
(used only by cursor-placed shapes), pattern `+0xAC`, height up / down `+0xAD` / `+0xAE` (same units as D5's).
No data file holds the shapes: at start-up the executable **builds 82 lists of target panels in code** (Disgaea PC
`dis1_st.exe` `FUN_004430d0`; D1 Complete Switch `FUN_7100012ea4`), and a row with pattern `p` uses list `p − 1`. Running
that builder in an emulator gives the same 82 shapes on PC and Switch (**Verified offline**). A cell is (x sideways, y
forward, kind): 1 = hit, 2 = the caster ends here, 3 = the cursor panel, the same idea as D5's `AttackMap`. Most shapes
are placed from the caster; 13 are cursor shapes and use the range byte. Readers of the lists: PC `FUN_0044a3b0` /
`FUN_004cd5c0`, Complete `FUN_710001d338` / `FUN_71000afde0` (**From decomp**).

**Power doesn't convert to D5 by formula.** D1 (PC `FUN_0046fdc0` special, `FUN_0046f670` normal): a special's attack
value is `(ATK + power × soft(level + 100) / 100) / 20` against `ATK / 30` for a normal hit, so every special carries a
built-in 1.5×. D5 uses `(100 + effect1_value) / 100`. A conversion model built from the two was tested on the 29
physical moves both games share and rejected (mean error 54–180 points, against 24 for simply taking the D5
counterpart's power) (**Verified offline**).

**Comparing D1 skills with Disgaea 5's** (from mapping the D1 Complete monster abilities onto D5 ones by effect;
**Verified offline**, checked on the 69 abilities that have the same name in both games, D5 Mod Kit fork, 2026-10):

- **Element** codes are the same in both games.
- **Category → D5 formula:** physical 2, magic 3, heal 4 match D5's formula field directly (29/29 physical and 5/5
  heal same-name rows agree).
- **Effect codes** mostly agree: 11 ATK, 12 DEF, 21 poison, 22 sleep, 24 amnesia, 25 charm. **13 and 14 are the
  other way round** in the two games, and drain is 102 in D1 but 104 in D5. Code 23 looks like D5's paralysis but no
  same-name pair proves it.
- D1 monster skill lines are four tiers, like D5's monster lines (IDs 12xxx / 22xxx, last digit 1–4).

### D1 Complete monster skills mapped onto Disgaea 5

Each D1 Complete monster class ability matched to the D5 monster skill with the same **effect** (element, power class,
effect codes, tier in the class line; D5 candidates limited to skills owned by D5 monster classes). Names often differ,
so this is by effect, not by name. Range and area can't be compared (D1 doesn't store them), so check those in game.
**Verified offline** (table comparison, D5 Mod Kit fork, 2026-10-26): 48 abilities, 39 exact, 9 similar.

| D1 class | D1 skill | D5 skill | Verdict |
|---|---|---|---|
| Beast | 2151 Stinger Strike | 12141 Hip Attack | similar |
| Beast | 2152 Beast Rend | 12042 Moon Shine Slash | exact |
| Beast | 2153 Devour | 12043 Carrot Funnel | exact |
| Beast | 2154 Beast Ray | 22052 Fairy King's Rage | exact |
| Cyclops | 3601 Magnetic Wave | 12191 Smoking Haze | exact |
| Cyclops | 3602 Magnetic Blast | 22042 Moon Child | exact |
| Cyclops | 3603 Magnetic Field | 12203 Icy Flame Shot | exact |
| Cyclops | 3604 Magnetic Flash | 12044 Gold Moon Judgment | exact |
| Dragon | 3701 Bloody Talons | 12011 Prinny Barrage | exact |
| Dragon | 3722 Fire Breath | 12182 Blazing Breath | similar |
| Dragon | 3702 Ice Breath | 12132 Water Guillotine | similar |
| Dragon | 3703 Blue Nova | 22113 Ice Death Sprout | exact |
| Dragon | 3723 Red Nova | 12183 Spiral Flame | exact |
| Dragon | 3724 Dragonic Flare | 12184 Flame God's Dawn | exact |
| Dragon | 3714 Dragonic Freeze | 12134 Niflheim | exact |
| Dragon | 3704 Dragonic Furor | 12114 Petal Concerto | exact |
| Gargoyle | 2071 Minimize | 12101 Eryngi Dance | exact |
| Gargoyle | 2072 Soul Eater | 22052 Fairy King's Rage | similar |
| Gargoyle | 2073 Cannonball | 12013 Pringer Beam | exact |
| Gargoyle | 2074 Eruption | 22052 Fairy King's Rage | exact |
| Ghost | 2011 Pixie Magic | 22052 Fairy King's Rage | similar |
| Ghost | 2012 Shock Magic | 22192 Imperial Fox Witch | exact |
| Ghost | 2013 Bomb Magic | 22051 Flying Flame Shot | exact |
| Ghost | 2014 Ice Magic | 22131 Cold Rain | exact |
| Golem | 2021 Golem Dunk | 12011 Prinny Barrage | exact |
| Golem | 2022 Golem Cannon | 12012 Prinny Bomb | exact |
| Golem | 2023 Psionic Wave | 22053 Flying Spore Shot | similar |
| Golem | 2024 Golem Smash | 12174 Hyper Suction | exact |
| Killer Armor | 2051 Power Slice | 12011 Prinny Barrage | exact |
| Killer Armor | 2052 Lightning Slash | 22042 Moon Child | exact |
| Killer Armor | 2053 Gigantic Slash | 12013 Pringer Beam | similar |
| Killer Armor | 2054 Blade Frenzy | 12014 Prinny Fusillade | exact |
| Nightmare | 2081 Fire Dance | 22052 Fairy King's Rage | exact |
| Nightmare | 2082 Ice Dance | 22131 Cold Rain | exact |
| Nightmare | 2083 Light Dance | 22191 Fox Spirit Dance | exact |
| Nightmare | 2084 Fire & Ice | 22052 Fairy King's Rage | exact |
| Pumpkin | 2061 Jackknife | 12011 Prinny Barrage | exact |
| Pumpkin | 2062 Jack the Ripper | 12102 Erynger Punch | similar |
| Pumpkin | 2063 Jack Rush | 12103 Mushroom Spin | exact |
| Pumpkin | 2064 Jackpot | 12104 Hyphae Infinity | exact |
| Serpent | 3501 Flap Dance | 12011 Prinny Barrage | exact |
| Serpent | 3502 Quadra Nosedive | 12192 Murder Stone Story | exact |
| Serpent | 3503 Death Drop | 12143 Thunderbolt | exact |
| Serpent | 3504 Supernova | 12014 Prinny Fusillade | similar |
| Vampire | 2181 Chaos Fire | 22052 Fairy King's Rage | exact |
| Vampire | 2182 Chaos Swarm | 12042 Moon Shine Slash | exact |
| Vampire | 2183 Chaos Force | 12013 Pringer Beam | exact |
| Vampire | 2184 Chaos Plasma | 22191 Fox Spirit Dance | exact |

## Character table (Disgaea PC)

`START.DAT` member 44, `CHAR_E.DAT` (`CHAR_C` / `CHAR_K` are the same table for other languages, with different text
encoding): `u32 count`, `u32 count` again, then **363 rows of 216 bytes**. Offsets below are worked out from
pg_disatools' structure (`PG_DisaDataCharacter`) and its wiki page; the stats offsets agree with this project's own
reader. Text is Shift-JIS; full-width Latin needs normalizing.

| Offset | Field | Type | Notes |
|---|---|---|---|
| 0x00 | name | 33 B | |
| 0x21 | title | 27 B | |
| 0x3C | type | u8 | 0 static (cutscene / scenery), 3 usable in battle, 4 Prinny, 6 Prism Red (doll) |
| 0x3F | gender | u8 | |
| 0x40 | skill count | u8 | |
| 0x41 | palette | u8 | Colour variant |
| 0x42 | learn speed | u8 | Probably skill EXP per use |
| 0x43 | jump | u8 | |
| 0x44 | weapon mastery | 8 × u8 | Fist, sword, spear, bow, gun, axe, staff, (unused) |
| 0x4C | aptitudes | 8 × u8 | ×10. Order **HP SP ATK DEF INT SPD HIT RES** |
| 0x54 | immunity | u16 | Bit field |
| 0x56 | id | u16 | Character ID |
| 0x58 | sprite / creation row | u16 | Groups classes in the Create Character screen (males and females apart, all mages together) |
| 0x5A | shadow size | u16 | ÷ 100 = shadow scale (the shadow is 8 × scale wide) |
| 0x5E | throw range | u16 | |
| 0x60 | help | u16 | `CHARHELP.DAT` ID, or 0 |
| 0x62 | base stats | 8 × u16 | Same order as aptitudes |
| 0x72 | EXP scale | u16 | Multiplies the level EXP table (see [functions](functions.md#levels-and-stats)) |
| 0x74 | movement | u16 | |
| 0x76 | movement type | u16 | 0 normal, 1 flying, 2 warping |
| 0x84 | mana cost scale | u16 | Creation cost = base rank cost × scale / 10 |
| 0x86 | counter | u16 | |
| 0x88 | skills | 20 × u16 | `MAGIC.DAT` IDs |
| 0xB0 | skill levels | 20 × u16 | Level each is learned |

The rest are unknown. RES and SPD are swapped compared to Disgaea 5's order. A community spreadsheet dump of this and
other tables is linked from the [pg_disatools wiki](https://github.com/ProgSys/pg_disatools/wiki/Table-file).

## Other Disgaea PC tables

All in `START.DAT`: a count header, then fixed rows, zero-padded at the end (**Community-reported**,
pg_disatools wiki and the Data Editor's definition files in the v1.2.1 release, `resources/script/disa/defFiles/`).
Unless noted, the header is the count twice.

| Table | Rows (original) | Row | Fields |
|---|---|---|---|
| `DUNGEON.DAT` | 154 | 40 B | 34-byte name + 0; bonus rank (end-of-map bonus modifier); map ID (`MP<id>.MPP`; the teleport menu's category is ID / 100 − 1); **unlock flag** (u16): a game-state bit, 3 bits for the bit and 13 for the byte; the map shows in the teleport list once that bit is set (pg_disatools' runtime code reads it as a game-state filter; the wiki's "script ID" is an older guess). Edit it to reach unused maps or add new ones; pg_disatools' custom campaigns use category `0x8000` |
| `HOSPITAL.DAT` | 33 | 16 B | Index; deaths needed; reward item ID (u32); HP healed (u32); SP healed (u32) |
| `NAME.DAT` | — | — | Random names: four u16 word counts, a u16 offset per word, then Shift-JIS names |
| `MUSICSHOP.DAT` | | 148 B | u64 price; u16 ID; u16 song (the game reads the music ID at `+0x0A`); 20-byte name + 0; 112-byte title + 0; u16 options. Header is a single u32 count |
| `HABIT.DAT` (innocents / specialists) | | 64 B | 34-byte name; 22 × u8 that the Data Editor labels as an ID plus one value per item category (fist, sword, spear, bow, gun, axe, staff, monster weapon, armor, belt, shoes, orb, glasses, muscle, "Ex", "Sy" and a few unlabelled), shaded 0–20; u16; u16 type; u32 ("script ID?"). The labels don't line up one-to-one with the fields, so treat the order as provisional |
| `MITEM.DAT` (items) | | 184 B | 32-byte name + 0; 114-byte description + 2 zero; u8 rank; u8 range; u16 jump; u8 icon index (cell in `item.tx2`); u8 type; u8 icon size; 2 × u16 unknown; u32 base price; HP SP ATK DEF INT SPD HIT RES (u16 each); u16 ID; u16 unknown |
| `WISH.DAT` (Dark Assembly bills) | | 164 B | u16 unknown; u8 ID; u8 required rank; u16 unknown; 44-byte title + 0; 112-byte description + 0 |
| `GE.DAT` (Geo effects) | | 148 B | 32-byte title + 0; 112-byte description + 0; u16 ID |
| `GEOCUBE.DAT` | | 152 B | 3 × u16 unknown; 32-byte title + 0; 112-byte description + 0. Single u32 count |
| `THIEF.DAT` | | 36 B | 28-byte name + 0; u16 type; 0; u32 unknown |
| `ZUKAN.DAT` (character encyclopedia) | | 736 B | u16 ID; eight description lines (122, then 86-byte lines). Single u32 count |
| `CHARHELP.DAT` | | 116 B | u16 ID; 114-byte description |
| `MENU_LIST.DAT` | | — | u16 count, then variable-length strings, each followed by 4 zero bytes |

**Music** isn't a table: 44 tracks (`BGM001`–`033`, `SONG001`–`008`, `songJungle`) and their **loop points** are
hard-coded in the executable; the `.ogg` files have no loop tags. The wiki describes name + loop rows at `0x00531870`;
pg_disatools' newer runtime code uses two 16-byte tables, `0x00545A50` for IDs below 100 and `0x0053EE98` for IDs
from 101, each `{s16 file index, s16 base volume (0–0x7FFF), s32 loop start, s32 loop end, s8 loop mode}`. Music 0 and
100 stop playback (`0x0046A830`); played tracks are marked as heard at `0x0065E360`.

## Character table (Disgaea 1 Complete)

`start.dat` member 43: 8-byte header, **363 rows of 224 bytes**, 8 trailing bytes.

| Offset | Field | Type | Notes |
|---|---|---|---|
| 0x00 | name | Shift-JIS | |
| 0x21 | title | Shift-JIS | |
| 0x45 | (class type?) | u8 | Values 0/1/2/4/6/8; meaning **Unknown** |
| 0x49 | palette | u8 | Verified against 287 rows. This is the palette a unique character really wears: art cut from colour 0 looks wrong for the 13 or so uniques whose byte is above 0 (Prism Blue: body 2610, palette 1) |
| 0x54 | aptitudes | 8 × u8 | ×10 |
| 0x6A | base stats | 8 × u16 | |
| 0x5E | id | u16 | |
| 0x60 | sprite | u16 | **Legacy** sprite ID. The five story leads have sprites 1–5 but are drawn by HD blocks 10–50; no table for this mapping was found |

**Dark Flonne (2026-11, Verified offline).** Her row (3060, index 279) has sprite 3 and palette byte `+0x49` = **0**, the
same as Flonne (3) and Flonne S. Detective (3180); no row with sprite 3 has palette 2. Byte `+0x48` differs (Flonne 3,
Dark Flonne 2) but is constant inside every family checked, so it is not a palette. HD block 30 has four palettes
(blue ribbon, green, red bow, ghost); the red-bow one matches her face and her own cut-in, block 32. How the game picks
her colours is still untraced.

**Laharl's mother (2026-11, Verified offline).** Row 361 (ID 6701, sprite 3510, palette 0) has stats and aptitudes but no
skills or evility. Block 3510 is a prop body: one 128 × 256 texture and six one-frame animations (IDs 0, 1, 2, 4, 7, 8),
the same pattern as the Receptionist (3600). There is no face for 3510, no cut-in and no `new_bu` bust among the 124.
Forty voice clips are named for her (`en_a_amom001`, `en_h_hmom001…025` and special variants; the speaker was not
checked by listening). The Disgaea RPG story art shows the same woman (matched by name, not by a shared file).

Rows include 61 cutscene poses (IDs 5000–6001), Mid-Boss repeats (2510/2520/2530), six "Vassal" monsters (3070–3120)
and four rows with no HD sprite (2917 Dimension Phone, 3200–3202).

**Also (2026-11, Verified offline):** the Complete skill table is `start.dat` member 40. The character table stores no
per-weapon forte (its nine-byte block is stat growth). Physical SP costs are about twice Disgaea 5's for the same move.
Cannons (2300–2302) and shop NPCs (2910–2912) have no skills.

## Face portraits (Disgaea 1 Complete)

**Correction (2026-11-18).** An earlier version of this page called `start.dat` member 73 the game's face set. It is an
older one. The HD faces are in **`SUBDATA.DAT`, member `anmdat.fad`** (Switch; the PS4 copy has the same 268 names in
`zz_anmdat.fad`, stored in a different pixel layout that was not decoded).

**`anmdat.fad`** (**Verified offline**, Switch v0): 22,532,352 bytes, **268 textures named `swf####`**. Each entry is the
name and a NUL, then `u32 entry size`, `u32 0`, `u32 packed size`, `u32 raw size` (66,560), `u16 256`, `u16 256`,
`u16 256` (colours), `u16 8` (bits); a `YKCMP_V1` type-4 stream starts 48 bytes into the entry (see
[ykcmp](../../../formats/ykcmp.md)). Decoded, it is 1,024 palette bytes (RGBA, alpha 255) then 65,536 linear 8-bit
indices: a full-frame head-and-shoulders on white, 256 × 256 (262 of 268 have a white corner pixel).

**The game's key** (**From decomp**, Switch v0): `FUN_7100006af8` formats `sprintf("swf%04d", sprite + palette)` from the
character-table row (sprite `+0x60`, palette `+0x49`, or the unit's own palette), with special IDs 2300 → 2300,
2301 and 2302 → 2301, 2918 → 2230 and 3190 → 3600. The status draw code (`FUN_71000840a8`, `FUN_7100089844`) looks the
name up under the `"wf"` texture group; the format string is used by four functions. With this key the set covers every
class colour and every unique the importer reads (all 59 buildable rows have a face; a class's colour `c` is
`body + c`). Dark Flonne (3060) takes Flonne's sprite 3 and so `swf0003`; `swf5390` (the fallen-angel drawing) is the
closer-looking one but not what the key gives. Maderas (2550) gets the generic Nosferatu face `swf2180`.

**`start.dat` member 73** (**Verified offline**) is a table of 365 spans: `u32 count`, then `count + 1` u32 offsets
measured from byte 4, then `count` u16 sprite IDs. Each span is a 16-byte header (u16 width, u16 height, kind 3) and raw
RGBA on an **opaque white background**. 272 spans are 256 × 256, 4 are 64 × 64 icons, 89 are empty. It holds 277 IDs
against `anmdat.fad`'s 268 (the FAD adds 2230; member 73 adds 2016, 2141–2145, 3610 and 3620–3622). Its lookup
(`FUN_7100048d2c`, loaded by `FUN_710005f8a0`) keys on the row's sprite only and returns the palette separately. The
art is the older face set: the Disgaea PC 64 × 64 pixel faces redrawn at 256, same poses, and the picture fills only a
region about 160 pixels square at the top of the cell, so used as-is in another game it shows as a small face in a white
box (**Verified in game**, in Disgaea 5).

**Why the old set mismatches.** The member-73 table is keyed to the *classic* sprite IDs, so where the HD pack redrew a
family the face no longer matches the body (**Verified offline**, classic sprites decoded for comparison): ID 2080 is a
hooded reaper against the HD horned skull reaper, 2100 a multi-eyed cyclops against the HD blue rifle demon, 2150 a lion
against the HD skull dragon (2030 and 2040 also). `anmdat.fad` has none of these mismatches and also fixes hair colours
(Sardia green, Serra grey, Antoire olive, Dark Durian green). Nothing else in the archives is a face set: all 107
`start.dat` members, every `DATA.DAT` name and the `SUBDATA.DAT` names were checked.

**Open:** whether the list and battle panels ever read the class colour faces for colours 1–5 (live checks showed colour
0 only). `swf0006` is a brown-haired woman with no character row among the 45 imported rows; it is probably Laharl's
mother's face (**Unknown**: not matched to a row).

## Voices (Disgaea 1 Complete and Disgaea PC)

The game voices only what a script asks for: script opcode `0x87` picks one of **four lists** of clip records per
character (66 characters). List 0 is the attack / skill shout (509 of 632 script voice calls); lists 1 and 2 are called
only inside skill choreographies (75 and 46 calls); **list 3 (2 calls) is the cast line of heal and support spells**:
both calls sit in shared sub-events that skill events call (`2000015` from the Heal, Espoir, Braveheart, Shield and
Magic lines, and `4009997`, the +50,000 variant of the skill event that `7100020794` picks when an Options flag is set);
the offensive-spell helper plays list 1. **No list and no clip is a damage or death line**. Five characters carry a clip
only in list 3 (Gordon, Jennifer, Vulcanus, Thursday, Dark Flonne); only Dark Flonne learns a spell that plays it
(Mega Heal). The Heal line and the support lines play different voice categories on the caster in D5 (**Verified
offline**, D5 scripts: Heal 9, Espoir and the support lines 8). The opcode is read at `7100098370` (**From decomp**,
Switch v0); the join over the Switch `script.dat` frames 3,816 of 3,822 events. Monsters name one shared sound
effect in all four lists. Some characters alias another's clips (Vyers uses Mid-Boss's, Dark Flonne uses Flonne's).
Audio: Disgaea PC `DATA.DAT` → `VOICE01/02[_EN].PBD` (DSARC FL) → Ogg Vorbis 16 kHz. Clip names are identical in the
PC, Switch (`.nlsd` type 8) and PS4 (`.nlsd` wrapping `VAGp` version 0x20001) editions. 15 of the characters this
project imported have clips (5–11 each). **Verified offline**.

## Class facts from the community (Disgaea 1 Complete)

Disgaea 1 Complete has no evilities. Player-written class pages give: the Reaper line (ranks Nightmare, Shade, Lich,
Chernobog, Death, Thanatos) is immune to ailments from rank 5 (**Community-reported**: [Disgaea Wiki, Reaper (Disgaea 1
Complete)](https://disgaea.fandom.com/wiki/Reaper_(Disgaea_1_Complete)), community editors, read 2026-11-19); the Serpent
line (Mizuchi, Sea Dragon, Rahab, Leviathan, Midgardsorm) is immune from rank 4 (**Community-reported**:
[Disgaea Wiki, Serpent](https://disgaea.fandom.com/wiki/Serpent_(Disgaea)), read 2026-11-19). These are not read from
the game files.

## Open questions

- What Complete's extra 8 bytes per character row hold.
- What decides Dark Flonne's colours (not `+0x49`, which is 0, and not `+0x48`).
- The unknown fields above; what `HABIT`'s 22 values mean exactly; `RMAPS` (random maps).

## Sources

- D5 Mod Kit development fork (Sean13128): `d5lib/d1skill.py`; (reader code not published; its results are the tables on this page) this project's D1C source reader report (2026-11-02).
- [pg_disatools](https://github.com/ProgSys/pg_disatools) by ProgSys (`PG_DisaDataMagic`, `PG_DisaDataCharacter`) and its [wiki](https://github.com/ProgSys/pg_disatools/wiki) (CHAR.DAT, DUNGEON.DAT, HOSPITAL.DAT, NAME.DAT, BGM), read 2026-11-04.
- 2026-11-18 correction (HD faces in `anmdat.fad`) and later additions: the fork's `docs/IMPORT_GATES.md` and this project's D1 Complete face, voice-list and roster reports.
- 2026-11 additions (pattern lists, power formulas, face table, voice lists): this project's research, published in the D5 Mod Kit development fork's `docs/D1_SPECIALS.md`, `docs/IMPORT_GATES.md` and `docs/SOURCE_VOICES.md`.
