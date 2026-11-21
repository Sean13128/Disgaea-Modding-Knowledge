# Disgaea 5 Complete — Data Tables

**Version:** Disgaea 5 Complete, PC (Steam app 803600). **Stored in:** `Data/Database/Database_0.dat`, an obfuscated
[NISPACK](../../../formats/nispack.md) archive with 90 member tables. Text is UTF-8 in five language slots: JP, EN, FR, CHN (zh-TW), KOR.

**Sources and evidence.** Table layouts come from D5 Mod Kit's table schemas (`d5lib/schemas/*.json`) and its
[DATABASE.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/DATABASE.md) and
[EXE_ANALYSIS.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/EXE_ANALYSIS.md) by eraser4521. All of it is
**Community-reported**. The Kit marks each field high, medium or low confidence; "high" means the author checked it
against the files and the executable. Those marks are kept below. Nothing on this page has been re-tested by this project.

## How the game loads tables

| Rule | Detail |
|---|---|
| Header | Row count at file offset 0 (the game reads it as `u16`, so at most 65,535 rows; the Kit writes `u32`). Records start at offset 4 |
| Record size | Fixed in the executable per table. The file must hold `4 + count × size` bytes; the game doesn't check |
| Storage | Every table is read whole onto the heap at startup. No fixed row limits |
| "Classic" tables (`char.dat`, `magic.dat`, `item.dat`, `HABIT.dat` …) | Sorted by ID with `qsort` at load, then binary-searched. File order doesn't matter; duplicate IDs give undefined results |
| `*DataBase.dat` tables and `Skill.dat` | Binary-searched **in file order**. Rows must stay sorted by ID |
| Checksums | None on any data file |
| Missing table | Fatal: the game can't start |

**Save safety.** Saves store each unit's cached `char.dat` *row index*, not only its ID, and the game doesn't recompute
it on load. Add new rows with IDs **above every existing ID**; inserting or deleting rows shifts indices and breaks
existing saves. Other save references (items, skills, flags) are by ID, but "append only, new IDs above the maximum" is
the safe rule for every sorted table. **Community-reported** (Kit EXE_ANALYSIS §5).

## Table layouts

| Layout | Tables | Shape |
|---|---|---|
| `fixed` | 69 | Count + fixed-size records |
| `pool` | 18 | Count + fixed records holding string pointers (`u32`, relative to offset 4) + a string pool. Each string is stored `\0` + UTF-8 + `\0` |
| `custom` | 3 | `name.dat` (name lists), `AttackMap.dat` (area shapes + cells), `comb.dat` (256 × 256 team-attack matrix) |

## All 90 tables

Every named field of every table: [data-reference.md](data-reference.md).

"Fields known" counts fields the Kit has named, out of all fields it splits the record into.

| Table | Layout | Record bytes | Key | Fields known | What it holds |
|---|---|---|---|---|---|
| `Anime_TableDataBase.dat` | fixed | 12 | `id` | 3/3 | Motion list of the character animation viewer (preview of a unit's sprite motions, prev/next browsing; |
| `AttackMap.dat` | custom | 8 | `id` | 4/4 | Area-of-effect / target shapes used by magic.dat attack_map. |
| `BattleEvent.dat` | fixed | 60 | `—` | 0/20 | *Not yet understood* |
| `char.dat` | fixed | 908 | `id` | 113/129 | Character / class master table (806 rows x 908 bytes). |
| `charhelp.dat` | fixed | 1428 | `id` | 27/27 | Character descriptions: 3 lines x 5 languages (92-byte slots) per help id, plus story-flag conditions selecting which variant is shown. |
| `charPersonal.dat` | fixed | 348 | `—` | 8/9 | Generic-class personalities (voice variants) offered at character creation: 3 rows per class voice group (26 human + 21 monster classes = 141 rows… |
| `CheatShopInformation.dat` | fixed | 4304 | `id` | 66/66 | Cheat Shop entries (24 rows): name, 2 summary lines (desc1/desc2) and up to 10 explanation lines, all 5-language fixed UTF-8 slots. |
| `comb.dat` | custom | 258 | `id` | 257/257 | Team-attack (combo) affinity matrix between character families. |
| `Curry.dat` | fixed | 1692 | `id` | 61/78 | Curry Shop menu (11 curries). |
| `DemoCheckDataBase.dat` | fixed | 12 | `id` | 3/3 | Demo pairing / check table (160 rows). |
| `DLC_Delivery_HelpDataBase.dat` | pool | 24 | `id` | 6/6 | Scenario summary text lines for the DLC delivery viewer (110 rows). |
| `DLC_DeliveryDataBase.dat` | pool | 120 | `id` | 30/30 | Pool-string variant of the DLC delivery list (45 rows; |
| `DLC_ShopDataBase.dat` | pool | 300 | `id` | 75/75 | Pool-string variant of the DLC shop list (26 rows): label, DLC content id, 7 five-language strings each preceded by its character count… |
| `dungeon.dat` | fixed | 336 | `id` | 28/41 | Stages/maps (story stages, challenge/carnage stages, post-game/DLC scenario maps, Trials, hubs, Item World rooms). |
| `EffectTableDataBase.dat` | fixed | 8 | `effect_id` | 2/2 | List of battle effect models: one row per Start/Effect/effectNNNN.obf file (exact 1:1 match, sorted), plus a percentage parameter. |
| `Ending.dat` | fixed | 1108 | `id` | 12/12 | Endings list (gallery / records). |
| `Enemy_Create_TableDataBase.dat` | fixed | 28 | `—` | 0/7 | *Not yet understood* |
| `Enemy_Parameters_TableDataBase` | fixed | 8 | `—` | 0/4 | *Not yet understood* |
| `EventCg.dat` | fixed | 1112 | `id` | 15/15 | Event CG gallery entries. |
| `EventViewer.dat` | fixed | 484 | `id` | 27/28 | Event (cutscene) replay viewer list. |
| `Feature.dat` | fixed | 448 | `—` | 7/8 | Character "feature" profile lines: random trivia text shown for units, keyed by (group, variant). |
| `FlagList_DebugDataBase.dat` | pool | 20 | `id` | 5/5 | Debug flag names (debug menu), separate id space. |
| `FlagList_DLCDataBase.dat` | pool | 20 | `id` | 5/5 | DLC ownership flag names. |
| `FlagListDataBase.dat` | pool | 20 | `id` | 5/5 | Named game flags (story/system/DLC/charamake...). |
| `GE.dat` | fixed | 808 | `id` | 12/12 | Geo Effects (Geo panel effects): 61 rows, id + 5-language name/description + magnitude value. |
| `Group.dat` | fixed | 1092 | `id` | 39/40 | Squads: name + 3 description lines, number of ranks, rank-up costs and member limits per rank. |
| `GroupHelp.dat` | fixed | 664 | `id` | 6/6 | Squad rank effect texts: one line (5 languages) per squad rank; |
| `HABIT.dat` | fixed | 1072 | `id` | 33/34 | Innocents / residents. |
| `HabitAffinity.dat` | fixed | 132 | `id` | 49/49 | Innocent combination table: for Innocent type <id> combined with the Innocent type of column j (columns ordered like the rows), the resulting… |
| `HOSPITAL.dat` | fixed | 20 | `id` | 5/6 | Hospital prize list (33 rows): an item is awarded when all non-zero requirements (HP healed, SP healed, revive count) are reached. |
| `item.dat` | fixed | 1120 | `id` | 38/43 | Items: weapons, armor, accessories and consumables (1364 rows x 1120 bytes). |
| `itemexchange.dat` | fixed | 12 | `id` | 3/5 | Point-threshold item rewards (52 rows): when a counter reaches 'points', 'item' is given. |
| `ItemGetSkillDataBase.dat` | fixed | 16 | `entry_id` | 4/4 | Skill Shop list: spells and generic evilities that can be bought/learned. |
| `ItemPersonalityCorrection.dat` | fixed | 132 | `id` | 1/129 | 20 rows x (id + 128 bytes). |
| `ItemWorld_ProbabilityDataBase.d` | fixed | 44 | `id` | 11/11 | Item World probability/parameter table: each id has 10 values (probably one per 10-floor band or rarity tier). |
| `ItemWorld_Room_CharDataDataBase` | pool | 80 | `id` | 18/20 | NPCs placed in Item World rooms (279 rows, pool strings). |
| `ItemWorld_RoomDataBase.dat` | fixed | 44 | `id` | 11/11 | Item World secret/small rooms (小部屋): room id -> map file, two map positions and the room flag. |
| `JobRank.dat` | fixed | 20 | `id` | 11/12 | Class rank (tier) chain for generic classes: one row per class tier (325 rows x 20 bytes; |
| `magic.dat` | fixed | 1140 | `id` | 81/85 | Skills / spells. |
| `Makai.dat` | fixed | 1380 | `id` | 30/40 | Named netherworlds (49): story worlds 1..14, class netherworlds 18..39 (featured class char id), Carnage Dimension (40), DLC/post-game worlds 41..49. |
| `Makai_Invader_UniqueDataBase.da` | pool | 96 | `—` | 10/34 | *Not yet understood* |
| `Makai_InvaderaDataBase.dat` | pool | 148 | `—` | 5/37 | *Not yet understood* |
| `MakaiEdit_ExteriorPartsDataBase` | pool | 92 | `id` | 19/23 | Netherworld Edit exterior parts (look of your pocket netherworld / research netherworlds). |
| `MakaiEdit_TownEditCategoryDataB` | pool | 60 | `id` | 12/17 | Pocket netherworld (base) edit: terrain block categories/shapes. |
| `MakaiName.dat` | fixed | 1864 | `id` | 31/31 | Word lists and description templates used to generate random netherworld names/descriptions (Nether Research). |
| `MakaiSkillDataBase.dat` | pool | 80 | `id` | 18/20 | Netherworld skills (effects active while a netherworld is dropped/called into battle). |
| `MapObjectTableDataBase.dat` | pool | 12 | `id` | 2/3 | Map 3D object list (id -> .obf model). |
| `MapPartShopItemDataBase.dat` | fixed | 12 | `id` | 3/3 | Base (Pocket Netherworld) edit shop: map parts for sale. |
| `music.dat` | fixed | 1048 | `track_id` | 15/17 | Music gallery (Jukebox) entries: one row per selectable track. |
| `name.dat` | custom | 12 | `—` | 3/3 | Random name lists for created/recruited generic characters. |
| `nameplate.dat` | fixed | 76 | `id` | 2/2 | Name plate table - stub. |
| `news.dat` | fixed | 2036 | `id` | 8/10 | Netherworld News ticker messages (261 rows) with a show/hide flag window (story progress). |
| `OverlordSkill.dat` | fixed | 2128 | `id` | 22/22 | Overload skills: id, 5-language name, three 5-language description lines, script id. |
| `pirate.dat` | fixed | 328 | `id` | 80/104 | Item World pirate crews (16 rows, JP-only text; |
| `Quest.dat` | fixed | 5636 | `id` | 83/94 | Quests (Quest shop quests ids 1..187 and resident request quests ids 2000..9502). |
| `QuestEnemy.dat` | fixed | 1208 | `id` | 136/260 | Extra enemy units injected into an existing map by a quest (Quest.quest_enemy_group -> group). |
| `RDFloorData.dat` | fixed | 24 | `—` | 0/12 | *Not yet understood* |
| `RMDUNGEON.dat` | fixed | 20 | `—` | 0/15 | *Not yet understood* |
| `room.dat` | fixed | 96 | `—` | 0/33 | *Not yet understood* |
| `saveicon.dat` | fixed | 200 | `id` | 10/12 | Selectable save-file icons (character portraits). |
| `ScreenPosDataBase.dat` | fixed | 116 | `id` | 27/29 | UI element screen positions per language (x,y,z,w,h); |
| `senator.dat` | fixed | 304 | `senator_id` | 9/25 | Dark Assembly senators: 258 rows = 43 parties x 6 senators (levels 5..220). |
| `senator2.dat` | fixed | 132 | `id` | 129/129 | Relationship/affinity matrix, 107 rows x 128 i8 columns. |
| `ship.dat` | fixed | 396 | `id` | 7/21 | Item World pirate ships (11 rows, JP-only). |
| `Skill.dat` | pool | 96 | `id` | 24/24 | Evilities. |
| `SkitFlagList.dat` | fixed | 44 | `id` | 22/23 | When a skit becomes available at the base: req_on = story flags that must be set (e.g. |
| `SkitStartCharSetting.dat` | fixed | 20 | `id` | 17/17 | Which main characters take part in each skit (16 boolean slots, 13..15 unused). |
| `SkitViewer.dat` | fixed | 484 | `id` | 27/28 | Skit replay viewer list (same record layout as EventViewer). |
| `Sound_BGM_0.dat` | fixed | 144 | `id` | 5/5 | Index/loop table for the music archive Data/Sound_BGM/Sound_BGM_0.dat. |
| `Sound_Msg_EN_0.dat` | fixed | 8 | `id` | 2/3 | EN dub story voice (dialogue) index for Sound_Msg_0.dat (msg_en/ members). |
| `Sound_Msg_JP_0.dat` | fixed | 8 | `id` | 2/3 | JP dub story voice (dialogue) index for Sound_Msg_0.dat (msg_jp/ members). |
| `Sound_SE_0.dat` | fixed | 16 | `id` | 5/6 | Sound effect index (per .vag in Sound_SE_0.dat archive). |
| `Sound_Voice_EN_0.dat` | fixed | 48 | `id` | 38/39 | EN dub battle/unit voice index for Sound_Voice_0.dat (voice_en/NNNNNN.dat packs). |
| `Sound_Voice_JP_0.dat` | fixed | 48 | `id` | 38/39 | JP dub battle/unit voice index for Sound_Voice_0.dat (voice_jp/NNNNNN.dat packs). |
| `SPC_Delivery.dat` | fixed | 6116 | `id` | 14/14 | Special delivery (DLC content viewer) list, full 5-language version (56 rows). |
| `SPC_Shop.dat` | fixed | 17688 | `id` | 37/37 | Special (DLC) shop list, full 5-language version (26 rows x 17688 bytes). |
| `StringListDataBase.dat` | pool | 44 | `id` | 11/11 | Generic localized UI/system string table (pool layout: id + 5 x (char count, string pointer)). |
| `SugorokuMapSetting.dat` | fixed | 244 | `—` | 5/66 | *Not yet understood* |
| `TextureDataBase.dat` | fixed | 124 | `id` | 31/31 | UI sprite atlas: rectangle of a UI texture (.txf in Start_*.dat/Texture*) per sprite id, with one rect set per language (75 rows differ: EN/FR… |
| `THIEF.dat` | fixed | 232 | `id` | 7/9 | Names of intangible things stolen when Steal takes something that is not an item (Dreams, Affection, Allowance ...), grouped by 'group' (11 =… |
| `Torture.dat` | fixed | 888 | `id` | 12/12 | Interrogation (torture room) methods: 94 rows, id/name/description + a small category value. |
| `TownEditMessageDataBase.dat` | pool | 76 | `id` | 19/19 | Lines spoken by units you assign as facility NPCs in the pocket netherworld (Netherworld Edit), per facility role x personality (x variant). |
| `TownEtcCenter.dat` | fixed | 52 | `id` | 10/15 | Misc interactive objects placed in the pocket netherworld base (13 rows): position, message id, flag window. |
| `TownFuncCharCenter.dat` | fixed | 384 | `id` | 18/25 | NPCs in the pocket netherworld base: facility shopkeepers, residents and story party members standing around. |
| `TownPartyCharCenter.dat` | fixed | 28 | `id` | 5/7 | Spots in the pocket netherworld where roster (party) characters can be placed/stand. |
| `TownTreasureCenter.dat` | fixed | 36 | `id` | 8/9 | Treasure chests in the pocket netherworld base, 3 per chapter, visible in a story-flag window. |
| `Trophy.dat` | fixed | 8768 | `id` | 28/30 | Trophies/achievements list (51 rows). |
| `WishDataBase.dat` | pool | 128 | `id` | 30/32 | Dark Assembly bills ("wishes", 議題): 164 rows. |
| `WorldMapHelpDataBase.dat` | pool | 64 | `—` | 15/16 | *Not yet understood* |
| `zukan.dat` | fixed | 21288 | `id` | 282/282 | Character encyclopedia (Records/zukan), 94 entries (story + generic classes + DLC guests). |

Names over 31 characters are stored cut short (for example `ItemWorld_ProbabilityDataBase.d`). The game still finds them, because archive lookup is a prefix match (see [INDEX.md](INDEX.md)).

**Shipped row counts** (Kit EXE_ANALYSIS): `char.dat` 806 · `magic.dat` 583 · `item.dat` 1,364 · `Skill.dat` 796 ·
`HABIT.dat` 110 · `JobRank.dat` 325 · `charhelp.dat` 334 · `charPersonal.dat` 141 · `Quest.dat` 516 · `Feature.dat` 752 ·
`zukan.dat` 94 · `music.dat` 76 · `Trophy.dat` 51 · `StringListDataBase.dat` 3,899 · `TextureDataBase.dat` 3,173 ·
`Anime_TableDataBase.dat` 284.

> The Kit's EXE_ANALYSIS labels `HABIT.dat` "(evilities)". Its schema, with field-level evidence, shows `HABIT.dat` is
> **Innocents**; evilities are in `Skill.dat`. This page follows the schema.

## How the main tables link

```
char.dat (characters, class ranks)
 ├─ learn1..16_skill ─────────► magic.dat (skills, spells)
 ├─ evility1..2 ──────────────► Skill.dat (evilities)
 ├─ overload_id ──────────────► OverlordSkill.dat (Overloads)
 ├─ help_id ──────────────────► charhelp.dat (descriptions)
 ├─ sprite_id ────────────────► anmNNNNN.lzs (sprites, in ANM_HI_*.dat)
 ├─ magichange_weapon ────────► item.dat
 └─ voice_id ─────────────────► Sound_Voice_*_0.dat (voice lines)
JobRank.dat (rank chain) ─────► char.dat ids
magic.dat attack_map ─────────► AttackMap.dat (area shapes)
item.dat unique_innocent ─────► HABIT.dat (innocents)
item.dat use_skill ───────────► magic.dat
comb.dat (team-attack rates) ─► char.dat ids
```

## `char.dat` — characters and classes

908-byte records, key `id` at `+0xD0`. One row per unique character form, per **generic class rank** (six rows per
class, IDs `family×10 + 0..5`, e.g. Warrior 1010–1015), per NPC and per map object.

| Offset | Field | Type | Meaning | Kit conf. |
|---|---|---|---|---|
| 0x00 | immunity_flags | i32 | Bitmask, set on some bosses | low |
| 0x04–0x12 | base_HP … base_SPD | 8 × i16 | Base stats, order HP SP ATK DEF INT RES HIT SPD. The game loads them **signed** (unit initializer `0x140218590`, signed load at `0x1402186A2`): 32,767 works, but 32,768 and up (sign bit set) gave absurd 10,000,000-level stats on a new recruit (**Verified in game**, Character Forge test, 2026-10) | high |
| 0x14–0x50 | learn1..16_skill | 16 × u32 | Learnset: `magic.dat` IDs | high |
| 0x54–0x72 | learn1..16_level | 16 × u16 | Level each skill is learned (10000 = never / special) | high |
| 0x74 | learn_count | u16 | Learn slots in use (0–16) | high |
| 0xB8 / 0xBC | evility1 / evility2 | u32 | Innate evilities (`Skill.dat` IDs) | high |
| 0xC0 / 0xC4 | evility3 / evility4 | u32 | Always 0; presumed further slots | low |
| 0xC8 | evility_count | u16 | How many of evility1..4 the engine reads | high |
| 0xCC | overload_id | u32 | `OverlordSkill.dat` ID † | disputed |
| 0xD0 | id | u32 | Character ID (key) | high |
| 0xD4 | sprite_id | u16 | Base `anmNNNNN` number; all ranks of a class share it. The unit set-up reads it **sign-extended** (`0x14021BE96`), so keep it below 32,768 | high |
| 0xD6 | size_radius | u16 | Grows with body size (Prinny 5, humans 6, UFO 21) | low |
| 0xD8 / 0xDA | height / height2 | u16 | Unit height; second value for tall heads or ears | medium |
| 0xDC | help_id | u16 | `charhelp.dat` ID | high |
| 0xDE | color_id | u16 | Palette variant; for classes `sprite_id + rank` | medium |
| 0xE2 | MOV | u16 | Move range | high |
| 0xE4 | move_type | u16 | 0 normal, 1 flying | low |
| 0xE6 | attack_range | u16 | Probably normal-attack range | low |
| 0xE8 / 0xEA / 0xF0 | hl_value / exp_value / mana_value | u16 | Rewards when defeated | medium |
| 0xF2 | dlc_group | u16 | 100 base game; 110–150 DLC packs | medium |
| 0xF4 | magichange_weapon | u16 | `item.dat` ID it becomes when Magichanging | high |
| 0xF6 | voice_id | u16 | Voice set (see [assets.md](assets.md)) † | disputed |
| 0xF8 | name | 5 × 52 B | Name per language | high |
| 0x1FC | title | 5 × 68 B | Title / rank name | high |
| 0x350–0x357 | apt_HP … apt_SPD | 8 × u8 | Aptitude % per stat | high |
| 0x358–0x368 | mastery_fist … mastery_monster_mag | 9 × u16 | Weapon-mastery aptitude per weapon type. The unit initializer copies only the **low byte** of each, so values above 255 don't work (same test) | high |
| 0x36A | mastery_armor | u16 | Tenth mastery value | low |
| 0x36C–0x36F | res_fire / wind / ice / star | 4 × i8 | Elemental resistance % | high/medium |
| 0x370–0x377 | wres_fist … wres_monster | 8 × i8 | Weapon-type resistance % † | disputed |
| 0x378 | kind_gender | u8 | Tens: 0 unique, 1 generic human, 2 generic monster. Ones: gender | high |
| 0x379 | tier | u8 | Class rank 0–5 (higher on enemy-only variants). Also used as the sprite colour variant † | disputed |
| 0x37C | JMP | u8 | Jump | high |
| 0x37D | throw | u8 | Throw distance | medium |
| 0x37E | counter | u8 | Counter count | medium |
| 0x380 | crit | u8 | Probably base critical rate. The unit set-up copies `+0x37D` to one unit field and `+0x380` to two; the stat recalculation clamps the first to **0–20** and the second (with a ×5 term) to **0–100**, which fits throw distance and a percentage. No code that uses the crit value was found, so this stays low confidence | low |
| 0x383 | magichange_type | u8 | Weapon type of the Magichange weapon | high |
| 0x386 | body_type | u8 | 0 humanoid, 1 monster type, 2 object | medium |
| 0x38A | is_dummy_slot | u8 | 1 = reserved empty class family ("Human Dummy" 1270–1355, "Monster Dummy" 2220–2335) | medium |

16 more fields are unnamed (`unk_`/`pad_`).

**ID ranges** (Kit CLASSES.md, EXE_ANALYSIS):

| Range | Use |
|---|---|
| 10–80 | Story cast (hard-coded in places) |
| < 1000 | Unique characters. The game treats every ID ≥ 1000 as a generic class |
| 1000–1310 | Generic human classes (the game's own "is generic" test) |
| 1270–1355 | "Human Dummy" families: reserved, empty, six rows each |
| 2010–2300 | Generic monster classes (the game's own test) |
| 2220–2335 | "Monster Dummy" families |
| 2400–2610 | Corpses, bottle mail, bosses |
| max shipped | 9001 |

† **Disputed** (2026-11; fields marked † here and in the `magic.dat` table below): an unpublished static review of the game's code reads these fields differently from the D5
Mod Kit schema this table follows (no consumer found, or a different use). Its evidence draws on material this wiki
can't publish yet, so the meanings above are kept but shouldn't be relied on until checked in game.

## `JobRank.dat` — class rank chain

20-byte records, one per class rank (325 rows: humans 1010–1265, monsters 2010–2215, unused dummy chains 9930–9995). A
new generic class needs six rows.

| Offset | Field | Type | Meaning | Kit conf. |
|---|---|---|---|---|
| 0x00 | id | u32 | `char.dat` ID of this rank | high |
| 0x04 | mastery_exp | u32 | Class-mastery EXP for this rank (base × 1, 4, 16, 64, 256, 4096) | medium |
| 0x08 | next_id | u16 | Next rank's ID; rank 6 wraps to rank 1 † | disputed |
| 0x0A–0x11 | bonus_HP … bonus_SPD | 8 × u8 | Permanent stat bonus for reaching this rank | medium |

## `magic.dat` — skills and spells

1,140-byte records (583 rows). Learnsets live in `char.dat`; this table holds what each skill does.

| Offset | Field | Type | Meaning | Kit conf. |
|---|---|---|---|---|
| 0x00 | id | u32 | Skill ID. 1–22 system commands; 101–607 weapon skills (hundreds digit = weapon); 3xxxx combo skills | high |
| 0x06 | sp_cost | u16 | Base SP cost | high |
| 0x08 | learn_level | u16 | Weapon skills: weapon mastery level needed | medium |
| 0x0A | rank | u16 | Tier within its family | medium |
| 0x0C | desc | 5 × 148 B | Description | high |
| 0x2F0 | name | 5 × 48 B | Name | high |
| 0x3E0–0x3E5 | effect1..6_type | 6 × u8 | Effect codes; slot 1 is the main effect (1 = damage/heal) | high/medium |
| 0x3E6–0x3F0 | effect1..6_value | 6 × i16 | Effect values (damage: power modifier %) | medium |
| 0x3F2 | skill_type | u8 | Skill class | high |
| 0x3F3 | moves_units | u8 | 1 = moves the user or target | medium |
| 0x3F4 | weapon_type | u8 | Weapon / mastery | high |
| 0x3F5 | element | u8 | 1 Fire, 2 Wind, 3 Ice, 4 Star | high |
| 0x3F6 | formula | u8 | Damage formula class | high |
| 0x3F7 | motion_type | u8 | User pose (weapon pose, Magichange) | medium |
| 0x3F8–0x41C | exp_lv1..10 | 10 × u32 | Skill EXP per level | medium |
| 0x420–0x432 | power_lv1..10 | 10 × i16 | Main magnitude per skill level | medium |
| 0x435 | range | u8 | Range in panels (99 = whole map) | medium |
| 0x438 | attack_map | u16 | `AttackMap.dat` shape (0 = none) | high |
| 0x43A / 0x43B | height_up / height_down | u8 | Vertical reach † (the up / down order may be reversed) | disputed |
| 0x43D | blow_distance | u8 | Knock-back distance | medium |
| 0x441 | tower_units | u8 | Stacked units needed (tower skills) | medium |
| 0x442–0x446 | partner1..3 | 3 × u16 | Combo partners (`char.dat` IDs) | high |
| 0x44C | partner_count | u8 | Partners used | high |
| 0x44E | skill_group | u8 | List group (0 magic, 1–6 weapons, 7 unique/combo…) | medium |
| 0x450 | anim_id | u16 | Skill animation sequence ID (65535 = none) † | disputed |
| 0x454–0x460 | user1..4 | 4 × u32 | Owning character / class | high |
| 0x464 | user_count | u16 | Owners used | high |
| 0x466 | version_group | u16 | 100 base; 110–150 DLC | low |
| 0x46C | base_skill | u32 | Skill this one derives from | high |
| 0x470 | dual_skill | u32 | Magichange: ID of the Dual version | high |

Several flag bytes (`0x434`, `0x436`, `0x43C`, `0x440`, `0x468`) are named but not understood.

## `Skill.dat` — evilities

96-byte records (796 rows), `pool` layout with 10 string pointers. Kept sorted by ID.

| Offset | Field | Type | Meaning | Kit conf. |
|---|---|---|---|---|
| 0x00 | id | u32 | Evility ID. 10–410 character evilities (grouped per character); 1010–1242 generic human classes … | high |
| 0x04 | name | strptr | Name | high |
| 0x18 | slot_cost | u32 | Slots when equipped (0 = innate) | high |
| 0x1C | desc | strptr | Description; `%d` filled from value1/value2 | high |
| 0x30 | kind | u32 | Unique or normal | high |
| 0x34 | effect_group | u32 | Internal effect family 0–44 | medium |
| 0x38 / 0x3C | value1 / value2 | u32 | Effect values | high |
| 0x40 | source | u32 | Where it comes from | medium |
| 0x44 / 0x48 | category / subcategory | u32 | Display grouping | medium / low |
| 0x4C | mana_cost | u32 | Mana to learn (0 = can't be learned) | high |
| 0x50 | shop_rank | u32 | Probably shop unlock tier | low |
| 0x54 | owner | u32 | Owning character / class | high |
| 0x58 | class_order | u32 | Generic classes: learn order 1–5, 6 = class unique | medium |
| 0x5C | version_group | u32 | 100 base; 110–150 DLC | low |

An evility's *behavior* is in the executable, keyed by ID: rows only carry names and numbers. See [modifiable.md](modifiable.md).

## `item.dat` — items

1,120-byte records (1,364 rows). Price and the eight stat bonuses are 64-bit.

| Offset | Field | Type | Meaning | Kit conf. |
|---|---|---|---|---|
| 0x00 | price | u64 | Shop price (HL) | high |
| 0x08–0x40 | HP … SPD | 8 × i64 | Stat bonuses | high |
| 0x48 | id | u32 | Item ID (weapons 101+, +10000 high-rank copies, armor 901+, consumables 2001+, DLC 5101+) | high |
| 0x4C | icon | u16 | Menu icon | medium |
| 0x4E | category | u16 | Sub-category | high |
| 0x50 | MOV | u16 | Move bonus | high |
| 0x52 | CNT | u16 | Counter bonus | high |
| 0x54 | unique_innocent | u16 | `HABIT.dat` innocent always attached | high |
| 0x56 | weapon_anm | u16 | Weapon sprite (`anmNNNNN`) | high |
| 0x58 | not_in_shop | u8 | 1 = not sold | medium |
| 0x59 | name | 5 × 64 B | Name | high |
| 0x199 | desc | 5 × 136 B | Description | high |
| 0x446 | rank | u8 | Item rank (1–40; 101–140 high-rank) | high |
| 0x448 | RNG | u8 | Attack or use range | high |
| 0x449 | JMP | u8 | Jump bonus | high |
| 0x44B | CRT | u8 | Critical rate % | high |
| 0x450 | use_skill | u32 | `magic.dat` skill run when used | high |
| 0x458 | char_exclusive | u8 | 1 = character-specific weapon | medium |
| 0x459 | equip_type | u8 | Equipment class | high |
| 0x45A | sort_order | u16 | List order (0xFFFF = hidden) | medium |

## Save fields added 2026-11-20

Fields in the unit record that mods and tools touch, found in the D5 Mod Kit development fork's work (**From decomp**
unless marked; layouts continue the table in [functions](functions.md#unit-record-in-the-game-state)):

- Title text `+0xBFC` (0x44 bytes) is **not saved**: it is rebuilt from the `char.dat` row on every load
  ([functions](functions.md#unit-record-new-fields)). Mana `+0x2DF8` (u32).
- Values copied from the class row when a unit is made (growth `+0x2DA0`, base stats `+0x2D18`, aptitudes `+0x3078`,
  mastery `+0x2DAE`, movement `+0x2E1A`–`+0x2E22`, resistances `+0x2E12`–`+0x2E15`): table on the
  [outfit page](mods/d5modkit-outfits.md#own-values-and-the-title-bug-2026-11-18).
- Item store in the state: 2,000 blocks of `0x258` bytes at `+0x482FD8`, a u16 count at `+0x5A7F58`; the New Game+ cast
  table of 64 serials at `+0x8810A0`. See [character-bank.md](character-bank.md).
- A save holding a sub-class `+0x35AC` is read as a signed word ([functions](functions.md#sub-class-list-seen-in-play-and-the-current-sub-class-panel)).

## Open questions

- 6 tables have no named fields at all: `BattleEvent.dat`, `Enemy_Create_TableDataBase.dat`, `Enemy_Parameters_TableDataBase`, `RDFloorData.dat`, `RMDUNGEON.dat` and `room.dat`. `SugorokuMapSetting.dat`, `Makai_InvaderaDataBase.dat` and `ItemPersonalityCorrection.dat` are mostly unknown too.
- Several flag bytes in `magic.dat` and `char.dat`.
- `nameplate.dat` ships as an 80-byte stub, but the executable expects 0x16C-byte records. What reads it?
- `FlagList_DebugDataBase.dat`, `FlagListDataBase.dat`, `FlagList_DLCDataBase.dat`, `DLC_ShopDataBase.dat` and `DLC_DeliveryDataBase.dat` are in the archive but never referenced by the executable (Kit EXE_ANALYSIS).

## Sources

- D5 Mod Kit by eraser4521 (MIT): [DATABASE.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/DATABASE.md), [EXE_ANALYSIS.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/EXE_ANALYSIS.md), [CLASSES.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/CLASSES.md), and the table schemas in `d5lib/schemas/`.
- [D5tools](https://github.com/BTAxis/D5tools) by BTAxis: an earlier partial `database_0.scheme`.
