# Disgaea 5 Complete — Full Table Reference

Every field D5 Mod Kit has named, for all 90 tables in `Database_0.dat`. Generated from the Kit's table schemas
(`d5lib/schemas/*.json`, MIT, by eraser4521). **Community-reported**; the *Conf.* column is the Kit's own confidence
(high = checked against the files and the executable). Fields the Kit hasn't understood (`unk_*`, `pad_*`) are
counted but not listed. Text fields come in five language slots (JP, EN, FR, CHN, KOR); only the first is listed.

For how the game loads these tables, and the most-used tables in detail, see [data.md](data.md).

## Contents

[`Anime_TableDataBase.dat`](#anime_tabledatabasedat) · [`AttackMap.dat`](#attackmapdat) · [`BattleEvent.dat`](#battleeventdat) · [`char.dat`](#chardat) · [`charhelp.dat`](#charhelpdat) · [`charPersonal.dat`](#charpersonaldat) · [`CheatShopInformation.dat`](#cheatshopinformationdat) · [`comb.dat`](#combdat) · [`Curry.dat`](#currydat) · [`DemoCheckDataBase.dat`](#democheckdatabasedat) · [`DLC_Delivery_HelpDataBase.dat`](#dlc_delivery_helpdatabasedat) · [`DLC_DeliveryDataBase.dat`](#dlc_deliverydatabasedat) · [`DLC_ShopDataBase.dat`](#dlc_shopdatabasedat) · [`dungeon.dat`](#dungeondat) · [`EffectTableDataBase.dat`](#effecttabledatabasedat) · [`Ending.dat`](#endingdat) · [`Enemy_Create_TableDataBase.dat`](#enemy_create_tabledatabasedat) · [`Enemy_Parameters_TableDataBase`](#enemy_parameters_tabledatabase) · [`EventCg.dat`](#eventcgdat) · [`EventViewer.dat`](#eventviewerdat) · [`Feature.dat`](#featuredat) · [`FlagList_DebugDataBase.dat`](#flaglist_debugdatabasedat) · [`FlagList_DLCDataBase.dat`](#flaglist_dlcdatabasedat) · [`FlagListDataBase.dat`](#flaglistdatabasedat) · [`GE.dat`](#gedat) · [`Group.dat`](#groupdat) · [`GroupHelp.dat`](#grouphelpdat) · [`HABIT.dat`](#habitdat) · [`HabitAffinity.dat`](#habitaffinitydat) · [`HOSPITAL.dat`](#hospitaldat) · [`item.dat`](#itemdat) · [`itemexchange.dat`](#itemexchangedat) · [`ItemGetSkillDataBase.dat`](#itemgetskilldatabasedat) · [`ItemPersonalityCorrection.dat`](#itempersonalitycorrectiondat) · [`ItemWorld_ProbabilityDataBase.d`](#itemworld_probabilitydatabased) · [`ItemWorld_Room_CharDataDataBase`](#itemworld_room_chardatadatabase) · [`ItemWorld_RoomDataBase.dat`](#itemworld_roomdatabasedat) · [`JobRank.dat`](#jobrankdat) · [`magic.dat`](#magicdat) · [`Makai.dat`](#makaidat) · [`Makai_Invader_UniqueDataBase.da`](#makai_invader_uniquedatabaseda) · [`Makai_InvaderaDataBase.dat`](#makai_invaderadatabasedat) · [`MakaiEdit_ExteriorPartsDataBase`](#makaiedit_exteriorpartsdatabase) · [`MakaiEdit_TownEditCategoryDataB`](#makaiedit_towneditcategorydatab) · [`MakaiName.dat`](#makainamedat) · [`MakaiSkillDataBase.dat`](#makaiskilldatabasedat) · [`MapObjectTableDataBase.dat`](#mapobjecttabledatabasedat) · [`MapPartShopItemDataBase.dat`](#mappartshopitemdatabasedat) · [`music.dat`](#musicdat) · [`name.dat`](#namedat) · [`nameplate.dat`](#nameplatedat) · [`news.dat`](#newsdat) · [`OverlordSkill.dat`](#overlordskilldat) · [`pirate.dat`](#piratedat) · [`Quest.dat`](#questdat) · [`QuestEnemy.dat`](#questenemydat) · [`RDFloorData.dat`](#rdfloordatadat) · [`RMDUNGEON.dat`](#rmdungeondat) · [`room.dat`](#roomdat) · [`saveicon.dat`](#saveicondat) · [`ScreenPosDataBase.dat`](#screenposdatabasedat) · [`senator.dat`](#senatordat) · [`senator2.dat`](#senator2dat) · [`ship.dat`](#shipdat) · [`Skill.dat`](#skilldat) · [`SkitFlagList.dat`](#skitflaglistdat) · [`SkitStartCharSetting.dat`](#skitstartcharsettingdat) · [`SkitViewer.dat`](#skitviewerdat) · [`Sound_BGM_0.dat`](#sound_bgm_0dat) · [`Sound_Msg_EN_0.dat`](#sound_msg_en_0dat) · [`Sound_Msg_JP_0.dat`](#sound_msg_jp_0dat) · [`Sound_SE_0.dat`](#sound_se_0dat) · [`Sound_Voice_EN_0.dat`](#sound_voice_en_0dat) · [`Sound_Voice_JP_0.dat`](#sound_voice_jp_0dat) · [`SPC_Delivery.dat`](#spc_deliverydat) · [`SPC_Shop.dat`](#spc_shopdat) · [`StringListDataBase.dat`](#stringlistdatabasedat) · [`SugorokuMapSetting.dat`](#sugorokumapsettingdat) · [`TextureDataBase.dat`](#texturedatabasedat) · [`THIEF.dat`](#thiefdat) · [`Torture.dat`](#torturedat) · [`TownEditMessageDataBase.dat`](#towneditmessagedatabasedat) · [`TownEtcCenter.dat`](#townetccenterdat) · [`TownFuncCharCenter.dat`](#townfunccharcenterdat) · [`TownPartyCharCenter.dat`](#townpartycharcenterdat) · [`TownTreasureCenter.dat`](#towntreasurecenterdat) · [`Trophy.dat`](#trophydat) · [`WishDataBase.dat`](#wishdatabasedat) · [`WorldMapHelpDataBase.dat`](#worldmaphelpdatabasedat) · [`zukan.dat`](#zukandat)

## Anime_TableDataBase.dat

Layout `fixed` · 12 bytes per row · key `id` · 3 of 3 fields named

Motion list of the character animation viewer (preview of a unit's sprite motions, prev/next browsing; screen object field +0x19170 = current row id). NOT the char->anm mapping: which anm file is shown comes from the character; this table only lists which animation numbers to cycle through. Groups are contiguous id ranges ended by a terminator row (anim_base=-1); the group used per character is hard-coded in the exe (see id). A new humanoid unit automatically uses the generic list 100..125; a new monster family would need an exe patch (or reuse an existing family id).

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | row id 100..383, sorted (exe binary-searches it, 0x1400b2a90). Rows form lists terminated by a row with… | high |
| 0x4 | `anim_base` | i32 | animation number inside the unit anm file; the viewer plays anim_base+1 (front) or anim_base+7… | high |
| 0x8 | `freeze_frame` | i32 | -1 = play normally (speed 1.0); >=0 = animation paused (speed 0) at this frame (exe 0x140456f90 ->… | high |

## AttackMap.dat

Layout `custom` · 8 bytes per row · key `id` · 4 of 4 fields named

Area-of-effect / target shapes used by magic.dat attack_map. File: u16 count, u16 cell_count, count x 8-byte shape records, cell_count x 3-byte cells, then 3 trailing junk bytes ("gra"). Coordinates: x = sideways, y = forward (facing direction of the user, +y away from the user). Shapes that contain an origin cell (kind 3) are centred on the target cursor (skills with range > 0); shapes without it are placed relative to the user (range 0 skills, e.g. 1001 = the panel in front, 3000 = 3 panels in a line). Cells of kind 2 are where the user ends up (skills that move the user, e.g. 1100 Triple Strike). Id families roughly = cell count x 1000 (1000-1300 one/two panels, 9000 = 9 cells...), 1-9 / 101-122 small cursor shapes, 25000-25008 straight lines of 1..9, 30000 self, 31000 radius-5 diamond (60 cells). The last record (id 33464, cells 61..129) looks like leftover junk and is not referenced.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | shape id (referenced by magic.dat attack_map) | high |
| 0x4 | `first_cell` | u16 | index of the first cell in the cells section (cells of a shape are contiguous) | high |
| 0x6 | `cell_num` | u8 | number of cells | high |
| 0x7 | `directional` | u8 | 1 = shape rotates with the user facing (not 90-degree symmetric); 0 = rotation-symmetric shape (verified:… | high |

## BattleEvent.dat

Layout `fixed` · 60 bytes per row · 0 of 20 fields named

*No fields named yet.*

## char.dat

Layout `fixed` · 908 bytes per row · key `id` · 113 of 129 fields named

Character / class master table (806 rows x 908 bytes). One row per unique character form, per generic class TIER (6 rows per class, ids family*10+0..5), per NPC and per map object. Key = id (+0xD0). Links: learnset -> magic.dat, evility1/2 -> Skill.dat, overload_id -> OverlordSkill.dat, help_id -> charhelp.dat, sprite_id -> anmNNNNN, magichange_weapon -> item.dat, voice_id -> charPersonal voice_group (/10) and Sound_Voice. Class-family ids (tier 0 ids: 10..990 uniques, 1010..1350 human classes, 2010..2330 monster classes) index comb.dat; tier rows are chained by JobRank.dat. Row layout: stats/learnset/evility block 0x000-0x0F7, 5x name (0x34), 5x title (0x44), aptitudes/masteries/resistances/misc 0x350-0x38B. Reserved empty class slots: Human Dummy rows (ids 1270-1355) and Monster Dummy rows (2220-2335).

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `immunity_flags` | i32 | bitmask, 0 for almost everyone; bosses: 127 (Bloodis, Void Dark, Liezerota Dark, Prinny Overlord), 16383… | low |
| 0x4 | `base_HP` | u16 | base stat (level-1 value before aptitude); order HP SP ATK DEF INT RES HIT SPD. Verified: Magician… | high |
| 0x6 | `base_SP` | u16 |  | high |
| 0x8 | `base_ATK` | u16 |  | high |
| 0xA | `base_DEF` | u16 |  | high |
| 0xC | `base_INT` | u16 |  | high |
| 0xE | `base_RES` | u16 |  | high |
| 0x10 | `base_HIT` | u16 |  | high |
| 0x12 | `base_SPD` | u16 |  | high |
| 0x14 | `learn1_skill` | u32 | → `magic.dat` learnset: skill/spell id (magic.dat key) learned at learn<N>_level; slots beyond learn_count are 0. 1xxxx… | high |
| 0x18 | `learn2_skill` | u32 | → `magic.dat` | high |
| 0x1C | `learn3_skill` | u32 | → `magic.dat` | high |
| 0x20 | `learn4_skill` | u32 | → `magic.dat` | high |
| 0x24 | `learn5_skill` | u32 | → `magic.dat` | high |
| 0x28 | `learn6_skill` | u32 | → `magic.dat` | high |
| 0x2C | `learn7_skill` | u32 | → `magic.dat` | high |
| 0x30 | `learn8_skill` | u32 | → `magic.dat` | high |
| 0x34 | `learn9_skill` | u32 | → `magic.dat` | high |
| 0x38 | `learn10_skill` | u32 | → `magic.dat` | high |
| 0x3C | `learn11_skill` | u32 | → `magic.dat` | high |
| 0x40 | `learn12_skill` | u32 | → `magic.dat` | high |
| 0x44 | `learn13_skill` | u32 | → `magic.dat` | high |
| 0x48 | `learn14_skill` | u32 | → `magic.dat` | high |
| 0x4C | `learn15_skill` | u32 | → `magic.dat` | high |
| 0x50 | `learn16_skill` | u32 | → `magic.dat` | high |
| 0x54 | `learn1_level` | u16 | level at which learn<N>_skill is learned (1..9999; 10000 seen = effectively never / special unlock) | high |
| 0x56 | `learn2_level` | u16 |  | high |
| 0x58 | `learn3_level` | u16 |  | high |
| 0x5A | `learn4_level` | u16 |  | high |
| 0x5C | `learn5_level` | u16 |  | high |
| 0x5E | `learn6_level` | u16 |  | high |
| 0x60 | `learn7_level` | u16 |  | high |
| 0x62 | `learn8_level` | u16 |  | high |
| 0x64 | `learn9_level` | u16 |  | high |
| 0x66 | `learn10_level` | u16 |  | high |
| 0x68 | `learn11_level` | u16 |  | high |
| 0x6A | `learn12_level` | u16 |  | high |
| 0x6C | `learn13_level` | u16 |  | high |
| 0x6E | `learn14_level` | u16 |  | high |
| 0x70 | `learn15_level` | u16 |  | high |
| 0x72 | `learn16_level` | u16 |  | high |
| 0x74 | `learn_count` | u16 | number of used learn slots (0..16); slots >= count are zero | high |
| 0xB8 | `evility1` | u32 | → `Skill.dat` innate (unique/class) Evility, Skill.dat id. Killia 10 War Demon, Seraphina 40, Warrior 1010 Wild Cry,… | high |
| 0xBC | `evility2` | u32 | → `Skill.dat` second innate Evility (only main story cast: Killia 11, Seraphina 41, Red Magnus 51, Christo 61, Usalia… | high |
| 0xC0 | `evility3` | u32 | → `Skill.dat` always 0 - presumed further innate Evility slot | low |
| 0xC4 | `evility4` | u32 | → `Skill.dat` always 0 - presumed further innate Evility slot | low |
| 0xC8 | `evility_count` | u16 | number of innate evilities: the engine uses evility1..evility<N> and ignores the rest (0x1402c0040 loops… | high |
| 0xCC | `overload_id` | u32 | → `OverlordSkill.dat` Overload (unique special) id, OverlordSkill.dat key: Killia 1, Seraphina 2, Red Magnus 4, Christo 6,… | high |
| 0xD0 | `id` | u32 | unique character id (KEY, referenced by comb.dat, JobRank.dat, Quest rewards, zukan.dat, DLC delivery,… | high |
| 0xD4 | `sprite_id` | u16 | → `anm` battle sprite = anm%05d file in ANM_HI_* (anmNNN0 main, anmNNN1 companion file). All tiers of a class… | high |
| 0xD6 | `size_radius` | u16 | 5..21: grows with body size (Prinny 5, humans 6, Bear 8, Dragon 9, Horseman 12, Proto Darkdeath 18, UFO… | low |
| 0xD8 | `height` | u16 | unit height (Prinny 11, humans 14, Succubus 17, Tree 38, Pillar 36) - probably used for lift/tower/marker… | medium |
| 0xDA | `height2` | u16 | second height value, usually = height but larger for tall heads/ears (Usalia 20, Red Magnus 17, Dark 17) -… | medium |
| 0xDC | `help_id` | u16 | → `charhelp.dat` charhelp.dat id of the description shown in status/encyclopedia (all tiers share the family help;… | high |
| 0xDE | `color_id` | u16 | look/palette variant: for classes = sprite_id + tier (Warrior 1010..1015 -> tier palettes), special forms… | medium |
| 0xE2 | `MOV` | u16 | Move range (Killia 5, Seraphina 4, Warrior 4, Thief/Ninja/Zeroken 6, objects 0) | high |
| 0xE4 | `move_type` | u16 | 0 normal; 1 flying/floating units (Imp, Fairy, Winged Warrior, Succubus, Sea Angel, Nine-Tails, Celestial… | low |
| 0xE6 | `attack_range` | u16 | probably normal-attack range: 1 for most, 2-4 for magic monsters (Succubus 4, Nine-Tails/Chimera/Sea Angel… | low |
| 0xE8 | `hl_value` | u16 | HL reward base when defeated: Lucky Board "HL Man" 2000/"HL Prince" 7500, Thief 150..200, most 10..65 | medium |
| 0xEA | `exp_value` | u16 | EXP reward base when defeated: Lucky Board "EXP Man" 200/"EXP Prince" 750, generic tiers 20..25 | medium |
| 0xF0 | `mana_value` | u16 | Mana-related value: Lucky Board "Mana Man" 2000/"Mana Prince" 7500; generic tiers 100,85,70,55,40,25… | medium |
| 0xF2 | `dlc_group` | u16 | 100 = base game; 110/120/130/140/150 = DLC content packs (Disgaea 1 / D4 / D3 / D2 / other guest… | medium |
| 0xF4 | `magichange_weapon` | u16 | → `item.dat` item.dat id of the weapon this unit becomes when Magichanging (Prinny 251 "Prinny Sword", Sludge 151 "Gel… | high |
| 0xF6 | `voice_id` | u16 | voice/sound set: Sound_Voice ids are (voice_id/10)*100000+n; charPersonal voice_group = voice_id/10 for… | medium |
| 0xF8 | `name (×5 languages)` | str(52) | character name (generic classes: class name, e.g. "Warrior") | high |
| 0x1FC | `title (×5 languages)` | str(68) | title / class-rank name shown under the name (Killia "Wanderer", Warrior tiers "Fighter".."Battle Master") | high |
| 0x350 | `apt_HP` | u8 | aptitude % per stat (HP SP ATK DEF INT RES HIT SPD); Killia ATK 125, Magician INT 120, tiers +5 every 2 tiers | high |
| 0x351 | `apt_SP` | u8 |  | high |
| 0x352 | `apt_ATK` | u8 |  | high |
| 0x353 | `apt_DEF` | u8 |  | high |
| 0x354 | `apt_INT` | u8 |  | high |
| 0x355 | `apt_RES` | u8 |  | high |
| 0x356 | `apt_HIT` | u8 |  | high |
| 0x357 | `apt_SPD` | u8 |  | high |
| 0x358 | `mastery_fist` | u16 | weapon-mastery aptitude per weapon type (Fist Sword Spear Bow Gun Axe Staff MonsterPhys MonsterMag);… | high |
| 0x35A | `mastery_sword` | u16 |  | high |
| 0x35C | `mastery_spear` | u16 |  | high |
| 0x35E | `mastery_bow` | u16 |  | high |
| 0x360 | `mastery_gun` | u16 |  | high |
| 0x362 | `mastery_axe` | u16 |  | high |
| 0x364 | `mastery_staff` | u16 |  | high |
| 0x366 | `mastery_monster_phys` | u16 |  | high |
| 0x368 | `mastery_monster_mag` | u16 |  | high |
| 0x36A | `mastery_armor` | u16 | 10th value in the mastery block: 10 typical, Armor Knight 18..23, Dark Knight 15..20, Horseman 14..19,… | low |
| 0x36C | `res_fire` | i8 | elemental resistance % (signed) Fire Wind Ice Star: Magician +25/+25/+25/0, Sludge -75 x4, Dragon King… | high |
| 0x36D | `res_wind` | i8 |  | high |
| 0x36E | `res_ice` | i8 |  | high |
| 0x36F | `res_star` | i8 |  | medium |
| 0x370 | `wres_fist` | i8 | weapon-type resistance % (signed) vs attacks of Fist Sword Spear Bow Gun Axe Staff Monster (D5 "weapon… | medium |
| 0x371 | `wres_sword` | i8 |  | medium |
| 0x372 | `wres_spear` | i8 |  | medium |
| 0x373 | `wres_bow` | i8 |  | medium |
| 0x374 | `wres_gun` | i8 |  | medium |
| 0x375 | `wres_axe` | i8 |  | medium |
| 0x376 | `wres_staff` | i8 |  | medium |
| 0x377 | `wres_monster` | i8 |  | medium |
| 0x378 | `kind_gender` | u8 | tens digit = kind (0 unique/special/object, 1 generic human class, 2 generic monster class), ones digit =… | high |
| 0x379 | `tier` | u8 | class tier / rank index 0..5 (Fighter 0 .. Battle Master 5); other values (up to 9) used by enemy-only… | high |
| 0x37C | `JMP` | u8 | Jump height (Killia 20, Seraphina 15, Magician 15, Thief 25, Ninja 30, Kunoichi/Rabbit/Winged 35, Level… | high |
| 0x37D | `throw` | u8 | Throw distance (Logan the "Throwing Overlord" 7, Bear 7, Killia 5, Magician 3, objects 0) | medium |
| 0x37E | `counter` | u8 | Counter count (Martial Artist/Fight Mistress 4, Zeroken/Ninja/Wrestler 3, Killia 2, Magician/Maid 0) | medium |
| 0x380 | `crit` | u8 | 0..20, grows slowly with tier (Warrior 5-6, Thief 9-11, Ninja/Kunoichi/Fuka 15, Asagi 12, objects 0).… | low |
| 0x383 | `magichange_type` | u8 | weapon type of the Magichange weapon (1 Fist 2 Sword 3 Spear 4 Bow 5 Gun 6 Axe 7 Staff) = first digit of… | high |
| 0x386 | `body_type` | u8 | 0 humanoid, 1 monster-type (monster classes, Christo, Usalia, story monsters; all Magichange users), 2 object | medium |
| 0x38A | `is_dummy_slot` | u8 | 1 = reserved empty class slot ("Human Dummy" ids 1270-1355, "Monster Dummy" 2220-2335), 0 otherwise | medium |

## charhelp.dat

Layout `fixed` · 1428 bytes per row · key `id` · 27 of 27 fields named

Character descriptions (334 x 1428): 3 lines x 5 languages (92-byte slots) per help id, plus story-flag conditions selecting which variant is shown. Also holds object and Geo Effect (9001-9040) texts. Referenced by char.dat help_id (+0xDC), which is matched against char_id (+4), not id.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | row id (unique, the table is sorted by it). Usually = char id; extra variants id+1, id+2 ... are… | high |
| 0x4 | `char_id` | u32 | → `char.dat` description key: char.dat help_id is matched against this field (0x14008eee0; help ids below 4000 are… | high |
| 0x8 | `line1 (×5 languages)` | str(92) | description line 1 | high |
| 0x1D4 | `line2 (×5 languages)` | str(92) | description line 2 | high |
| 0x3A0 | `line3 (×5 languages)` | str(92) | description line 3 (empty for most generic classes) | high |
| 0x56C | `flag_off1` | u32 | → `flag:FlagListDataBase.dat` story flag that must NOT be set for this variant to be shown (0 = unused). E.g. Killia help 10 hidden once… | medium |
| 0x570 | `flag_off2` | u32 | → `flag:FlagListDataBase.dat` | medium |
| 0x574 | `flag_off3` | u32 | → `flag:FlagListDataBase.dat` | medium |
| 0x578 | `flag_off4` | u32 | → `flag:FlagListDataBase.dat` | medium |
| 0x57C | `flag_off5` | u32 | → `flag:FlagListDataBase.dat` | medium |
| 0x580 | `flag_on1` | u32 | → `flag:FlagListDataBase.dat` story flag that MUST be set for this variant (0 = unused). Christo 61 needs 208 set and 1199 unset; Logan… | medium |
| 0x584 | `flag_on2` | u32 | → `flag:FlagListDataBase.dat` | medium |
| 0x588 | `flag_on3` | u32 | → `flag:FlagListDataBase.dat` | medium |
| 0x58C | `flag_on4` | u32 | → `flag:FlagListDataBase.dat` | medium |
| 0x590 | `flag_on5` | u32 | → `flag:FlagListDataBase.dat` | medium |

## charPersonal.dat

Layout `fixed` · 348 bytes per row · 8 of 9 fields named

Generic-class personalities (voice variants) offered at character creation: 3 rows per class voice group (26 human + 21 monster classes = 141 rows x 348). Looked up by (voice_group, variant). A new generic class needs 3 rows with its voice_group (char.dat voice_id/10) and matching Sound_Voice data to have selectable voices.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | personality entry id 0..131 (not unique: the DLC classes 124-126 reuse 129-131) | medium |
| 0x4 | `voice_group` | u16 | generic class voice group = char.dat voice_id / 10 (101 Warrior .. 126 Sorcerer, 201 Prinny .. 221… | high |
| 0x6 | `variant` | u8 | personality index 0..2 within the class (the 3 selectable voices/personalities at character creation) | high |
| 0x7 | `name (×5 languages)` | str(68) | personality name shown when creating a character ("Cool", "Quietly Burning", ...) | high |

## CheatShopInformation.dat

Layout `fixed` · 4304 bytes per row · key `id` · 66 of 66 fields named

Cheat Shop entries (24 rows): name, 2 summary lines (desc1/desc2) and up to 10 explanation lines, all 5-language fixed UTF-8 slots. Contains no numeric cheat parameters (ranges/steps are hard-coded).

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | cheat id 1..25 (11 unused) | high |
| 0x4 | `name (×5 languages)` | str(44) |  | high |
| 0xE0 | `desc1 (×5 languages)` | str(68) |  | high |
| 0x234 | `desc2 (×5 languages)` | str(68) |  | high |
| 0x388 | `expl1 (×5 languages)` | str(68) |  | high |
| 0x4DC | `expl2 (×5 languages)` | str(68) |  | high |
| 0x630 | `expl3 (×5 languages)` | str(68) |  | high |
| 0x784 | `expl4 (×5 languages)` | str(68) |  | high |
| 0x8D8 | `expl5 (×5 languages)` | str(68) |  | high |
| 0xA2C | `expl6 (×5 languages)` | str(68) |  | high |
| 0xB80 | `expl7 (×5 languages)` | str(68) |  | high |
| 0xCD4 | `expl8 (×5 languages)` | str(68) |  | high |
| 0xE28 | `expl9 (×5 languages)` | str(68) |  | high |
| 0xF7C | `expl10 (×5 languages)` | str(68) |  | high |

## comb.dat

Layout `custom` · 258 bytes per row · key `id` · 257 of 257 fields named

Team-attack (combo) affinity matrix between character families. File: u32 used_count (167); u16 ids[256] (char.dat tier-0 ids: 10..990 uniques, 1010..1350 human classes incl. Human Dummy 1270-1350, 2010..2330 monster classes incl. dummies; rest 0); u8 m[256][256]. Pseudo-record per row: id + 256 values, value m[row][col] = chance (%) that the column character joins/combos with the row character (asymmetric: Killia row, Seraphina column = 99 but not vice versa; Bloodis/Goldion rows vs Majorita = 0 (enemies), Usalia row vs Majorita = 80). Default 30, same family (diagonal) 50, friends 40-80, 99 = always, 0 = never. A new class/character needs its tier-0 id in a free id slot (replace a 0 or a dummy id) and a row+column of values, otherwise the default rate is not available. Header count NOT auto-updated.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u16 | → `char.dat` character family id of this row = char.dat id of tier 0 (10 Killia, 40 Seraphina, 1010 Warrior, 2010… | high |
| 0x2 | `with_10` | u8 | rate % when partner is id 10 (Killia) [id-vector slot 0] | medium |
| 0x3 | `with_20` | u8 | rate % when partner is id 20 (Killia) [id-vector slot 1] | medium |
| 0x4 | `with_30` | u8 | rate % when partner is id 30 (Killidia) [id-vector slot 2] | medium |
| 0x5 | `with_40` | u8 | rate % when partner is id 40 (Seraphina) [id-vector slot 3] | medium |
| 0x6 | `with_50` | u8 | rate % when partner is id 50 (Red Magnus) [id-vector slot 4] | medium |
| 0x7 | `with_60` | u8 | rate % when partner is id 60 (Christo) [id-vector slot 5] | medium |
| 0x8 | `with_70` | u8 | rate % when partner is id 70 (Usalia) [id-vector slot 6] | medium |
| 0x9 | `with_80` | u8 | rate % when partner is id 80 (Usalia) [id-vector slot 7] | medium |
| 0xA | `with_90` | u8 | rate % when partner is id 90 (???) [id-vector slot 8] | medium |
| 0xB | `with_100` | u8 | rate % when partner is id 100 (Zeroken) [id-vector slot 9] | medium |
| 0xC | `with_110` | u8 | rate % when partner is id 110 (Bloodis) [id-vector slot 10] | medium |
| 0xD | `with_120` | u8 | rate % when partner is id 120 (Goldion) [id-vector slot 11] | medium |
| 0xE | `with_130` | u8 | rate % when partner is id 130 (Majorita) [id-vector slot 12] | medium |
| 0xF | `with_140` | u8 | rate % when partner is id 140 (Majorita) [id-vector slot 13] | medium |
| 0x10 | `with_150` | u8 | rate % when partner is id 150 (Void Dark) [id-vector slot 14] | medium |
| 0x11 | `with_160` | u8 | rate % when partner is id 160 (Void) [id-vector slot 15] | medium |
| 0x12 | `with_170` | u8 | rate % when partner is id 170 (Liezerota・Dark) [id-vector slot 16] | medium |
| 0x13 | `with_180` | u8 | rate % when partner is id 180 (Liezerota) [id-vector slot 17] | medium |
| 0x14 | `with_190` | u8 | rate % when partner is id 190 (Yellow Prinny) [id-vector slot 18] | medium |
| 0x15 | `with_200` | u8 | rate % when partner is id 200 (Usalia's Father) [id-vector slot 19] | medium |
| 0x16 | `with_210` | u8 | rate % when partner is id 210 (Usalia's Mother) [id-vector slot 20] | medium |
| 0x17 | `with_220` | u8 | rate % when partner is id 220 (Panchos) [id-vector slot 21] | medium |
| 0x18 | `with_230` | u8 | rate % when partner is id 230 (Seraphina's Father) [id-vector slot 22] | medium |
| 0x19 | `with_240` | u8 | rate % when partner is id 240 (Asagi) [id-vector slot 23] | medium |
| 0x1A | `with_250` | u8 | rate % when partner is id 250 (Logan) [id-vector slot 24] | medium |
| 0x1B | `with_260` | u8 | rate % when partner is id 260 (Geese) [id-vector slot 25] | medium |
| 0x1C | `with_270` | u8 | rate % when partner is id 270 (Hedler) [id-vector slot 26] | medium |
| 0x1D | `with_280` | u8 | rate % when partner is id 280 (Erynder Z) [id-vector slot 27] | medium |
| 0x1E | `with_290` | u8 | rate % when partner is id 290 (Izuna) [id-vector slot 28] | medium |
| 0x1F | `with_300` | u8 | rate % when partner is id 300 (Drumdawn) [id-vector slot 29] | medium |
| 0x20 | `with_310` | u8 | rate % when partner is id 310 (Brave) [id-vector slot 30] | medium |
| 0x21 | `with_320` | u8 | rate % when partner is id 320 (Beheritas) [id-vector slot 31] | medium |
| 0x22 | `with_330` | u8 | rate % when partner is id 330 (Nero) [id-vector slot 32] | medium |
| 0x23 | `with_340` | u8 | rate % when partner is id 340 (Ignis) [id-vector slot 33] | medium |
| 0x24 | `with_350` | u8 | rate % when partner is id 350 (Vint) [id-vector slot 34] | medium |
| 0x25 | `with_360` | u8 | rate % when partner is id 360 (Graser) [id-vector slot 35] | medium |
| 0x26 | `with_370` | u8 | rate % when partner is id 370 (Mundo) [id-vector slot 36] | medium |
| 0x27 | `with_380` | u8 | rate % when partner is id 380 (Dark) [id-vector slot 37] | medium |
| 0x28 | `with_390` | u8 | rate % when partner is id 390 (Pleinair) [id-vector slot 38] | medium |
| 0x29 | `with_400` | u8 | rate % when partner is id 400 (Meaver) [id-vector slot 39] | medium |
| 0x2A | `with_410` | u8 | rate % when partner is id 410 (Dark Bit) [id-vector slot 40] | medium |
| 0x2B | `with_420` | u8 | rate % when partner is id 420 (Alfonzo) [id-vector slot 41] | medium |
| 0x2C | `with_430` | u8 | rate % when partner is id 430 (Prinny Overlord) [id-vector slot 42] | medium |
| 0x2D | `with_440` | u8 | rate % when partner is id 440 (Dark Killia) [id-vector slot 43] | medium |
| 0x2E | `with_450` | u8 | rate % when partner is id 450 (Void Dark?) [id-vector slot 44] | medium |
| 0x2F | `with_460` | u8 | rate % when partner is id 460 (Void Dark?) [id-vector slot 45] | medium |
| 0x30 | `with_470` | u8 | rate % when partner is id 470 (Unique Dummy) [id-vector slot 46] | medium |
| 0x31 | `with_480` | u8 | rate % when partner is id 480 (Unique Dummy) [id-vector slot 47] | medium |
| 0x32 | `with_490` | u8 | rate % when partner is id 490 (Unique Dummy) [id-vector slot 48] | medium |
| 0x33 | `with_500` | u8 | rate % when partner is id 500 (Seraphina) [id-vector slot 49] | medium |
| 0x34 | `with_510` | u8 | rate % when partner is id 510 (Laharl) [id-vector slot 50] | medium |
| 0x35 | `with_520` | u8 | rate % when partner is id 520 (Etna) [id-vector slot 51] | medium |
| 0x36 | `with_530` | u8 | rate % when partner is id 530 (Flonne) [id-vector slot 52] | medium |
| 0x37 | `with_540` | u8 | rate % when partner is id 540 (Sicily) [id-vector slot 53] | medium |
| 0x38 | `with_550` | u8 | rate % when partner is id 550 (Girl Laharl) [id-vector slot 54] | medium |
| 0x39 | `with_560` | u8 | rate % when partner is id 560 (Pleinair) [id-vector slot 55] | medium |
| 0x3A | `with_570` | u8 | rate % when partner is id 570 (Valvatorez) [id-vector slot 56] | medium |
| 0x3B | `with_580` | u8 | rate % when partner is id 580 (Fenrich) [id-vector slot 57] | medium |
| 0x3C | `with_590` | u8 | rate % when partner is id 590 (Artina) [id-vector slot 58] | medium |
| 0x3D | `with_600` | u8 | rate % when partner is id 600 (Fuka) [id-vector slot 59] | medium |
| 0x3E | `with_610` | u8 | rate % when partner is id 610 (Desco) [id-vector slot 60] | medium |
| 0x3F | `with_620` | u8 | rate % when partner is id 620 (Emizel) [id-vector slot 61] | medium |
| 0x40 | `with_630` | u8 | rate % when partner is id 630 (Nisa) [id-vector slot 62] | medium |
| 0x41 | `with_640` | u8 | rate % when partner is id 640 (Mao) [id-vector slot 63] | medium |
| 0x42 | `with_650` | u8 | rate % when partner is id 650 (Raspberyl) [id-vector slot 64] | medium |
| 0x43 | `with_660` | u8 | rate % when partner is id 660 (Salvatore) [id-vector slot 65] | medium |
| 0x44 | `with_670` | u8 | rate % when partner is id 670 (Almaz) [id-vector slot 66] | medium |
| 0x45 | `with_680` | u8 | rate % when partner is id 680 (Sapphire) [id-vector slot 67] | medium |
| 0x46 | `with_690` | u8 | rate % when partner is id 690 (Metallia) [id-vector slot 68] | medium |
| 0x47 | `with_700` | u8 | rate % when partner is id 700 (Adell) [id-vector slot 69] | medium |
| 0x48 | `with_710` | u8 | rate % when partner is id 710 (Rozalin) [id-vector slot 70] | medium |
| 0x49 | `with_720` | u8 | rate % when partner is id 720 (Axel) [id-vector slot 71] | medium |
| 0x4A | `with_730` | u8 | rate % when partner is id 730 (Zetta) [id-vector slot 72] | medium |
| 0x4B | `with_740` | u8 | rate % when partner is id 740 (Pram) [id-vector slot 73] | medium |
| 0x4C | `with_750` | u8 | rate % when partner is id 750 (Petta) [id-vector slot 74] | medium |
| 0x4D | `with_760` | u8 | rate % when partner is id 760 (Overlord Priere) [id-vector slot 75] | medium |
| 0x4E | `with_770` | u8 | rate % when partner is id 770 (Baal) [id-vector slot 76] | medium |
| 0x4F | `with_780` | u8 | rate % when partner is id 780 (Pure Flonne) [id-vector slot 77] | medium |
| 0x50 | `with_790` | u8 | rate % when partner is id 790 (Tyrant Valvatorez) [id-vector slot 78] | medium |
| 0x51 | `with_800` | u8 | rate % when partner is id 800 (Sister Artina) [id-vector slot 79] | medium |
| 0x52 | `with_810` | u8 | rate % when partner is id 810 (Rozalin) [id-vector slot 80] | medium |
| 0x53 | `with_820` | u8 | rate % when partner is id 820 (Ball Bit) [id-vector slot 81] | medium |
| 0x54 | `with_830` | u8 | rate % when partner is id 830 (Ball Bit) [id-vector slot 82] | medium |
| 0x55 | `with_840` | u8 | rate % when partner is id 840 (Ball Bit) [id-vector slot 83] | medium |
| 0x56 | `with_850` | u8 | rate % when partner is id 850 (Ball Bit) [id-vector slot 84] | medium |
| 0x57 | `with_860` | u8 | rate % when partner is id 860 (Magical ☆ Prinny) [id-vector slot 85] | medium |
| 0x58 | `with_870` | u8 | rate % when partner is id 870 (H252 Prinny) [id-vector slot 86] | medium |
| 0x59 | `with_880` | u8 | rate % when partner is id 880 (Poor Clever Prinny) [id-vector slot 87] | medium |
| 0x5A | `with_890` | u8 | rate % when partner is id 890 (Pringer?) [id-vector slot 88] | medium |
| 0x5B | `with_900` | u8 | rate % when partner is id 900 (N2 Prinny) [id-vector slot 89] | medium |
| 0x5C | `with_910` | u8 | rate % when partner is id 910 (Bankai Prinny) [id-vector slot 90] | medium |
| 0x5D | `with_920` | u8 | rate % when partner is id 920 (Martian Prinny) [id-vector slot 91] | medium |
| 0x5E | `with_930` | u8 | rate % when partner is id 930 (Tyrant Prinio) [id-vector slot 92] | medium |
| 0x5F | `with_940` | u8 | rate % when partner is id 940 (Value Your Life) [id-vector slot 93] | medium |
| 0x60 | `with_950` | u8 | rate % when partner is id 950 (DLC Dummy) [id-vector slot 94] | medium |
| 0x61 | `with_960` | u8 | rate % when partner is id 960 (DLC Dummy) [id-vector slot 95] | medium |
| 0x62 | `with_970` | u8 | rate % when partner is id 970 (DLC Dummy) [id-vector slot 96] | medium |
| 0x63 | `with_980` | u8 | rate % when partner is id 980 (DLC Dummy) [id-vector slot 97] | medium |
| 0x64 | `with_990` | u8 | rate % when partner is id 990 (DLC Dummy) [id-vector slot 98] | medium |
| 0x65 | `with_1010` | u8 | rate % when partner is id 1010 (Warrior) [id-vector slot 99] | medium |
| 0x66 | `with_1020` | u8 | rate % when partner is id 1020 (Valkyrie) [id-vector slot 100] | medium |
| 0x67 | `with_1030` | u8 | rate % when partner is id 1030 (Magician) [id-vector slot 101] | medium |
| 0x68 | `with_1040` | u8 | rate % when partner is id 1040 (Witch) [id-vector slot 102] | medium |
| 0x69 | `with_1050` | u8 | rate % when partner is id 1050 (Martial Artist) [id-vector slot 103] | medium |
| 0x6A | `with_1060` | u8 | rate % when partner is id 1060 (Fight Mistress) [id-vector slot 104] | medium |
| 0x6B | `with_1070` | u8 | rate % when partner is id 1070 (Clergy) [id-vector slot 105] | medium |
| 0x6C | `with_1080` | u8 | rate % when partner is id 1080 (Cleric) [id-vector slot 106] | medium |
| 0x6D | `with_1090` | u8 | rate % when partner is id 1090 (Thief) [id-vector slot 107] | medium |
| 0x6E | `with_1100` | u8 | rate % when partner is id 1100 (Maid) [id-vector slot 108] | medium |
| 0x6F | `with_1110` | u8 | rate % when partner is id 1110 (Archer) [id-vector slot 109] | medium |
| 0x70 | `with_1120` | u8 | rate % when partner is id 1120 (Gunner) [id-vector slot 110] | medium |
| 0x71 | `with_1130` | u8 | rate % when partner is id 1130 (Ninja) [id-vector slot 111] | medium |
| 0x72 | `with_1140` | u8 | rate % when partner is id 1140 (Lady Samurai) [id-vector slot 112] | medium |
| 0x73 | `with_1150` | u8 | rate % when partner is id 1150 (Armor Knight) [id-vector slot 113] | medium |
| 0x74 | `with_1160` | u8 | rate % when partner is id 1160 (Magic Knight) [id-vector slot 114] | medium |
| 0x75 | `with_1170` | u8 | rate % when partner is id 1170 (Celestial Host) [id-vector slot 115] | medium |
| 0x76 | `with_1180` | u8 | rate % when partner is id 1180 (Professor) [id-vector slot 116] | medium |
| 0x77 | `with_1190` | u8 | rate % when partner is id 1190 (Wrestler) [id-vector slot 117] | medium |
| 0x78 | `with_1200` | u8 | rate % when partner is id 1200 (Pirate) [id-vector slot 118] | medium |
| 0x79 | `with_1210` | u8 | rate % when partner is id 1210 (Sage) [id-vector slot 119] | medium |
| 0x7A | `with_1220` | u8 | rate % when partner is id 1220 (Dark Knight) [id-vector slot 120] | medium |
| 0x7B | `with_1230` | u8 | rate % when partner is id 1230 (Asagi) [id-vector slot 121] | medium |
| 0x7C | `with_1240` | u8 | rate % when partner is id 1240 (Celestial Hostess) [id-vector slot 122] | medium |
| 0x7D | `with_1250` | u8 | rate % when partner is id 1250 (Kunoichi) [id-vector slot 123] | medium |
| 0x7E | `with_1260` | u8 | rate % when partner is id 1260 (Sorcerer) [id-vector slot 124] | medium |
| 0x7F | `with_1270` | u8 | rate % when partner is id 1270 (Human Dummy) [id-vector slot 125] | medium |
| 0x80 | `with_1280` | u8 | rate % when partner is id 1280 (Human Dummy) [id-vector slot 126] | medium |
| 0x81 | `with_1290` | u8 | rate % when partner is id 1290 (Human Dummy) [id-vector slot 127] | medium |
| 0x82 | `with_1300` | u8 | rate % when partner is id 1300 (Human Dummy) [id-vector slot 128] | medium |
| 0x83 | `with_1310` | u8 | rate % when partner is id 1310 (Human Dummy) [id-vector slot 129] | medium |
| 0x84 | `with_1320` | u8 | rate % when partner is id 1320 (Human Dummy) [id-vector slot 130] | medium |
| 0x85 | `with_1330` | u8 | rate % when partner is id 1330 (Human Dummy) [id-vector slot 131] | medium |
| 0x86 | `with_1340` | u8 | rate % when partner is id 1340 (Human Dummy) [id-vector slot 132] | medium |
| 0x87 | `with_1350` | u8 | rate % when partner is id 1350 (Human Dummy) [id-vector slot 133] | medium |
| 0x88 | `with_2010` | u8 | rate % when partner is id 2010 (Prinny) [id-vector slot 134] | medium |
| 0x89 | `with_2020` | u8 | rate % when partner is id 2020 (Imp) [id-vector slot 135] | medium |
| 0x8A | `with_2030` | u8 | rate % when partner is id 2030 (Orc) [id-vector slot 136] | medium |
| 0x8B | `with_2040` | u8 | rate % when partner is id 2040 (Rabbit) [id-vector slot 137] | medium |
| 0x8C | `with_2050` | u8 | rate % when partner is id 2050 (Fairy) [id-vector slot 138] | medium |
| 0x8D | `with_2060` | u8 | rate % when partner is id 2060 (Sludge) [id-vector slot 139] | medium |
| 0x8E | `with_2070` | u8 | rate % when partner is id 2070 (Slumber Cat) [id-vector slot 140] | medium |
| 0x8F | `with_2080` | u8 | rate % when partner is id 2080 (Winged Warrior) [id-vector slot 141] | medium |
| 0x90 | `with_2090` | u8 | rate % when partner is id 2090 (Undead) [id-vector slot 142] | medium |
| 0x91 | `with_2100` | u8 | rate % when partner is id 2100 (Shroom) [id-vector slot 143] | medium |
| 0x92 | `with_2110` | u8 | rate % when partner is id 2110 (Flora Beast) [id-vector slot 144] | medium |
| 0x93 | `with_2120` | u8 | rate % when partner is id 2120 (Felynn) [id-vector slot 145] | medium |
| 0x94 | `with_2130` | u8 | rate % when partner is id 2130 (Sea Angel) [id-vector slot 146] | medium |
| 0x95 | `with_2140` | u8 | rate % when partner is id 2140 (Succubus) [id-vector slot 147] | medium |
| 0x96 | `with_2150` | u8 | rate % when partner is id 2150 (Chimera) [id-vector slot 148] | medium |
| 0x97 | `with_2160` | u8 | rate % when partner is id 2160 (Nether Noble) [id-vector slot 149] | medium |
| 0x98 | `with_2170` | u8 | rate % when partner is id 2170 (Bear) [id-vector slot 150] | medium |
| 0x99 | `with_2180` | u8 | rate % when partner is id 2180 (Dragon King) [id-vector slot 151] | medium |
| 0x9A | `with_2190` | u8 | rate % when partner is id 2190 (Nine-Tails) [id-vector slot 152] | medium |
| 0x9B | `with_2200` | u8 | rate % when partner is id 2200 (Twin Dragon) [id-vector slot 153] | medium |
| 0x9C | `with_2210` | u8 | rate % when partner is id 2210 (Horseman) [id-vector slot 154] | medium |
| 0x9D | `with_2220` | u8 | rate % when partner is id 2220 (Monster Dummy) [id-vector slot 155] | medium |
| 0x9E | `with_2230` | u8 | rate % when partner is id 2230 (Monster Dummy) [id-vector slot 156] | medium |
| 0x9F | `with_2240` | u8 | rate % when partner is id 2240 (Monster Dummy) [id-vector slot 157] | medium |
| 0xA0 | `with_2250` | u8 | rate % when partner is id 2250 (Monster Dummy) [id-vector slot 158] | medium |
| 0xA1 | `with_2260` | u8 | rate % when partner is id 2260 (Monster Dummy) [id-vector slot 159] | medium |
| 0xA2 | `with_2270` | u8 | rate % when partner is id 2270 (Monster Dummy) [id-vector slot 160] | medium |
| 0xA3 | `with_2280` | u8 | rate % when partner is id 2280 (Monster Dummy) [id-vector slot 161] | medium |
| 0xA4 | `with_2290` | u8 | rate % when partner is id 2290 (Monster Dummy) [id-vector slot 162] | medium |
| 0xA5 | `with_2300` | u8 | rate % when partner is id 2300 (Monster Dummy) [id-vector slot 163] | medium |
| 0xA6 | `with_2310` | u8 | rate % when partner is id 2310 (Monster Dummy) [id-vector slot 164] | medium |
| 0xA7 | `with_2320` | u8 | rate % when partner is id 2320 (Monster Dummy) [id-vector slot 165] | medium |
| 0xA8 | `with_2330` | u8 | rate % when partner is id 2330 (Monster Dummy) [id-vector slot 166] | medium |
| 0xA9 | `slot_167` | u8 | unused id-vector slot 167 (id 0, all values 0) | high |
| 0xAA | `slot_168` | u8 | unused id-vector slot 168 (id 0, all values 0) | high |
| 0xAB | `slot_169` | u8 | unused id-vector slot 169 (id 0, all values 0) | high |
| 0xAC | `slot_170` | u8 | unused id-vector slot 170 (id 0, all values 0) | high |
| 0xAD | `slot_171` | u8 | unused id-vector slot 171 (id 0, all values 0) | high |
| 0xAE | `slot_172` | u8 | unused id-vector slot 172 (id 0, all values 0) | high |
| 0xAF | `slot_173` | u8 | unused id-vector slot 173 (id 0, all values 0) | high |
| 0xB0 | `slot_174` | u8 | unused id-vector slot 174 (id 0, all values 0) | high |
| 0xB1 | `slot_175` | u8 | unused id-vector slot 175 (id 0, all values 0) | high |
| 0xB2 | `slot_176` | u8 | unused id-vector slot 176 (id 0, all values 0) | high |
| 0xB3 | `slot_177` | u8 | unused id-vector slot 177 (id 0, all values 0) | high |
| 0xB4 | `slot_178` | u8 | unused id-vector slot 178 (id 0, all values 0) | high |
| 0xB5 | `slot_179` | u8 | unused id-vector slot 179 (id 0, all values 0) | high |
| 0xB6 | `slot_180` | u8 | unused id-vector slot 180 (id 0, all values 0) | high |
| 0xB7 | `slot_181` | u8 | unused id-vector slot 181 (id 0, all values 0) | high |
| 0xB8 | `slot_182` | u8 | unused id-vector slot 182 (id 0, all values 0) | high |
| 0xB9 | `slot_183` | u8 | unused id-vector slot 183 (id 0, all values 0) | high |
| 0xBA | `slot_184` | u8 | unused id-vector slot 184 (id 0, all values 0) | high |
| 0xBB | `slot_185` | u8 | unused id-vector slot 185 (id 0, all values 0) | high |
| 0xBC | `slot_186` | u8 | unused id-vector slot 186 (id 0, all values 0) | high |
| 0xBD | `slot_187` | u8 | unused id-vector slot 187 (id 0, all values 0) | high |
| 0xBE | `slot_188` | u8 | unused id-vector slot 188 (id 0, all values 0) | high |
| 0xBF | `slot_189` | u8 | unused id-vector slot 189 (id 0, all values 0) | high |
| 0xC0 | `slot_190` | u8 | unused id-vector slot 190 (id 0, all values 0) | high |
| 0xC1 | `slot_191` | u8 | unused id-vector slot 191 (id 0, all values 0) | high |
| 0xC2 | `slot_192` | u8 | unused id-vector slot 192 (id 0, all values 0) | high |
| 0xC3 | `slot_193` | u8 | unused id-vector slot 193 (id 0, all values 0) | high |
| 0xC4 | `slot_194` | u8 | unused id-vector slot 194 (id 0, all values 0) | high |
| 0xC5 | `slot_195` | u8 | unused id-vector slot 195 (id 0, all values 0) | high |
| 0xC6 | `slot_196` | u8 | unused id-vector slot 196 (id 0, all values 0) | high |
| 0xC7 | `slot_197` | u8 | unused id-vector slot 197 (id 0, all values 0) | high |
| 0xC8 | `slot_198` | u8 | unused id-vector slot 198 (id 0, all values 0) | high |
| 0xC9 | `slot_199` | u8 | unused id-vector slot 199 (id 0, all values 0) | high |
| 0xCA | `slot_200` | u8 | unused id-vector slot 200 (id 0, all values 0) | high |
| 0xCB | `slot_201` | u8 | unused id-vector slot 201 (id 0, all values 0) | high |
| 0xCC | `slot_202` | u8 | unused id-vector slot 202 (id 0, all values 0) | high |
| 0xCD | `slot_203` | u8 | unused id-vector slot 203 (id 0, all values 0) | high |
| 0xCE | `slot_204` | u8 | unused id-vector slot 204 (id 0, all values 0) | high |
| 0xCF | `slot_205` | u8 | unused id-vector slot 205 (id 0, all values 0) | high |
| 0xD0 | `slot_206` | u8 | unused id-vector slot 206 (id 0, all values 0) | high |
| 0xD1 | `slot_207` | u8 | unused id-vector slot 207 (id 0, all values 0) | high |
| 0xD2 | `slot_208` | u8 | unused id-vector slot 208 (id 0, all values 0) | high |
| 0xD3 | `slot_209` | u8 | unused id-vector slot 209 (id 0, all values 0) | high |
| 0xD4 | `slot_210` | u8 | unused id-vector slot 210 (id 0, all values 0) | high |
| 0xD5 | `slot_211` | u8 | unused id-vector slot 211 (id 0, all values 0) | high |
| 0xD6 | `slot_212` | u8 | unused id-vector slot 212 (id 0, all values 0) | high |
| 0xD7 | `slot_213` | u8 | unused id-vector slot 213 (id 0, all values 0) | high |
| 0xD8 | `slot_214` | u8 | unused id-vector slot 214 (id 0, all values 0) | high |
| 0xD9 | `slot_215` | u8 | unused id-vector slot 215 (id 0, all values 0) | high |
| 0xDA | `slot_216` | u8 | unused id-vector slot 216 (id 0, all values 0) | high |
| 0xDB | `slot_217` | u8 | unused id-vector slot 217 (id 0, all values 0) | high |
| 0xDC | `slot_218` | u8 | unused id-vector slot 218 (id 0, all values 0) | high |
| 0xDD | `slot_219` | u8 | unused id-vector slot 219 (id 0, all values 0) | high |
| 0xDE | `slot_220` | u8 | unused id-vector slot 220 (id 0, all values 0) | high |
| 0xDF | `slot_221` | u8 | unused id-vector slot 221 (id 0, all values 0) | high |
| 0xE0 | `slot_222` | u8 | unused id-vector slot 222 (id 0, all values 0) | high |
| 0xE1 | `slot_223` | u8 | unused id-vector slot 223 (id 0, all values 0) | high |
| 0xE2 | `slot_224` | u8 | unused id-vector slot 224 (id 0, all values 0) | high |
| 0xE3 | `slot_225` | u8 | unused id-vector slot 225 (id 0, all values 0) | high |
| 0xE4 | `slot_226` | u8 | unused id-vector slot 226 (id 0, all values 0) | high |
| 0xE5 | `slot_227` | u8 | unused id-vector slot 227 (id 0, all values 0) | high |
| 0xE6 | `slot_228` | u8 | unused id-vector slot 228 (id 0, all values 0) | high |
| 0xE7 | `slot_229` | u8 | unused id-vector slot 229 (id 0, all values 0) | high |
| 0xE8 | `slot_230` | u8 | unused id-vector slot 230 (id 0, all values 0) | high |
| 0xE9 | `slot_231` | u8 | unused id-vector slot 231 (id 0, all values 0) | high |
| 0xEA | `slot_232` | u8 | unused id-vector slot 232 (id 0, all values 0) | high |
| 0xEB | `slot_233` | u8 | unused id-vector slot 233 (id 0, all values 0) | high |
| 0xEC | `slot_234` | u8 | unused id-vector slot 234 (id 0, all values 0) | high |
| 0xED | `slot_235` | u8 | unused id-vector slot 235 (id 0, all values 0) | high |
| 0xEE | `slot_236` | u8 | unused id-vector slot 236 (id 0, all values 0) | high |
| 0xEF | `slot_237` | u8 | unused id-vector slot 237 (id 0, all values 0) | high |
| 0xF0 | `slot_238` | u8 | unused id-vector slot 238 (id 0, all values 0) | high |
| 0xF1 | `slot_239` | u8 | unused id-vector slot 239 (id 0, all values 0) | high |
| 0xF2 | `slot_240` | u8 | unused id-vector slot 240 (id 0, all values 0) | high |
| 0xF3 | `slot_241` | u8 | unused id-vector slot 241 (id 0, all values 0) | high |
| 0xF4 | `slot_242` | u8 | unused id-vector slot 242 (id 0, all values 0) | high |
| 0xF5 | `slot_243` | u8 | unused id-vector slot 243 (id 0, all values 0) | high |
| 0xF6 | `slot_244` | u8 | unused id-vector slot 244 (id 0, all values 0) | high |
| 0xF7 | `slot_245` | u8 | unused id-vector slot 245 (id 0, all values 0) | high |
| 0xF8 | `slot_246` | u8 | unused id-vector slot 246 (id 0, all values 0) | high |
| 0xF9 | `slot_247` | u8 | unused id-vector slot 247 (id 0, all values 0) | high |
| 0xFA | `slot_248` | u8 | unused id-vector slot 248 (id 0, all values 0) | high |
| 0xFB | `slot_249` | u8 | unused id-vector slot 249 (id 0, all values 0) | high |
| 0xFC | `slot_250` | u8 | unused id-vector slot 250 (id 0, all values 0) | high |
| 0xFD | `slot_251` | u8 | unused id-vector slot 251 (id 0, all values 0) | high |
| 0xFE | `slot_252` | u8 | unused id-vector slot 252 (id 0, all values 0) | high |
| 0xFF | `slot_253` | u8 | unused id-vector slot 253 (id 0, all values 0) | high |
| 0x100 | `slot_254` | u8 | unused id-vector slot 254 (id 0, all values 0) | high |
| 0x101 | `slot_255` | u8 | unused id-vector slot 255 (id 0, all values 0) | high |

## Curry.dat

Layout `fixed` · 1692 bytes per row · key `id` · 61 of 78 fields named

Curry Shop menu (11 curries). Effects are small i32 strengths per stat/resistance/bonus; 'effects_*' is the short effect summary shown in the menu. text3 slots are empty in all rows.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | curry id 1..11 | high |
| 0x4 | `name (×5 languages)` | str(40) |  | high |
| 0xCC | `unlock_flag` | i32 | -1 = always available; 18050..18053 on the 4 DLC/bonus curries (Exclusive, Dengeki Rage, Sardine, Sweets):… | medium |
| 0xD0 | `price` | i32 | price in HL (450..800) | high |
| 0xD4 | `HP` | i32 | curry effect strength for HP (small ints; verified e.g. Pork Curry 'Critical Rate Up' -> CRT, Chicken 'SPD… | medium |
| 0xD8 | `SP` | i32 | curry effect strength for SP (small ints; verified e.g. Pork Curry 'Critical Rate Up' -> CRT, Chicken 'SPD… | medium |
| 0xDC | `ATK` | i32 | curry effect strength for ATK (small ints; verified e.g. Pork Curry 'Critical Rate Up' -> CRT, Chicken… | medium |
| 0xE0 | `DEF` | i32 | curry effect strength for DEF (small ints; verified e.g. Pork Curry 'Critical Rate Up' -> CRT, Chicken… | medium |
| 0xE4 | `INT` | i32 | curry effect strength for INT (small ints; verified e.g. Pork Curry 'Critical Rate Up' -> CRT, Chicken… | medium |
| 0xE8 | `RES` | i32 | curry effect strength for RES (small ints; verified e.g. Pork Curry 'Critical Rate Up' -> CRT, Chicken… | medium |
| 0xEC | `HIT` | i32 | curry effect strength for HIT (small ints; verified e.g. Pork Curry 'Critical Rate Up' -> CRT, Chicken… | medium |
| 0xF0 | `SPD` | i32 | curry effect strength for SPD (small ints; verified e.g. Pork Curry 'Critical Rate Up' -> CRT, Chicken… | medium |
| 0xF4 | `MOV` | i32 | curry effect strength for MOV (small ints; verified e.g. Pork Curry 'Critical Rate Up' -> CRT, Chicken… | medium |
| 0xF8 | `JMP` | i32 | curry effect strength for JMP (small ints; verified e.g. Pork Curry 'Critical Rate Up' -> CRT, Chicken… | medium |
| 0xFC | `RNG` | i32 | curry effect strength for RNG (small ints; verified e.g. Pork Curry 'Critical Rate Up' -> CRT, Chicken… | medium |
| 0x100 | `THR` | i32 | curry effect strength for THR (small ints; verified e.g. Pork Curry 'Critical Rate Up' -> CRT, Chicken… | medium |
| 0x104 | `CNT` | i32 | curry effect strength for CNT (small ints; verified e.g. Pork Curry 'Critical Rate Up' -> CRT, Chicken… | medium |
| 0x108 | `CRT` | i32 | curry effect strength for CRT (small ints; verified e.g. Pork Curry 'Critical Rate Up' -> CRT, Chicken… | medium |
| 0x10C | `res_fist` | i32 | resistance-up effect for fist (Go Go 'Paralyze Res Up', Katsu 'Sleep', Shrimp 'Amnesia', Beef 'Deprave'… | medium |
| 0x110 | `res_sword` | i32 | resistance-up effect for sword (Go Go 'Paralyze Res Up', Katsu 'Sleep', Shrimp 'Amnesia', Beef 'Deprave'… | medium |
| 0x114 | `res_spear` | i32 | resistance-up effect for spear (Go Go 'Paralyze Res Up', Katsu 'Sleep', Shrimp 'Amnesia', Beef 'Deprave'… | medium |
| 0x118 | `res_bow` | i32 | resistance-up effect for bow (Go Go 'Paralyze Res Up', Katsu 'Sleep', Shrimp 'Amnesia', Beef 'Deprave'… | medium |
| 0x11C | `res_gun` | i32 | resistance-up effect for gun (Go Go 'Paralyze Res Up', Katsu 'Sleep', Shrimp 'Amnesia', Beef 'Deprave'… | medium |
| 0x120 | `res_axe` | i32 | resistance-up effect for axe (Go Go 'Paralyze Res Up', Katsu 'Sleep', Shrimp 'Amnesia', Beef 'Deprave'… | medium |
| 0x124 | `res_staff` | i32 | resistance-up effect for staff (Go Go 'Paralyze Res Up', Katsu 'Sleep', Shrimp 'Amnesia', Beef 'Deprave'… | medium |
| 0x128 | `res_monster` | i32 | resistance-up effect for monster (Go Go 'Paralyze Res Up', Katsu 'Sleep', Shrimp 'Amnesia', Beef 'Deprave'… | medium |
| 0x12C | `res_fire` | i32 | resistance-up effect for fire (Go Go 'Paralyze Res Up', Katsu 'Sleep', Shrimp 'Amnesia', Beef 'Deprave'… | medium |
| 0x130 | `res_ice` | i32 | resistance-up effect for ice (Go Go 'Paralyze Res Up', Katsu 'Sleep', Shrimp 'Amnesia', Beef 'Deprave'… | medium |
| 0x134 | `res_wind` | i32 | resistance-up effect for wind (Go Go 'Paralyze Res Up', Katsu 'Sleep', Shrimp 'Amnesia', Beef 'Deprave'… | medium |
| 0x138 | `res_star` | i32 | resistance-up effect for star (Go Go 'Paralyze Res Up', Katsu 'Sleep', Shrimp 'Amnesia', Beef 'Deprave'… | medium |
| 0x13C | `res_poison` | i32 | resistance-up effect for poison (Go Go 'Paralyze Res Up', Katsu 'Sleep', Shrimp 'Amnesia', Beef 'Deprave'… | medium |
| 0x140 | `res_sleep` | i32 | resistance-up effect for sleep (Go Go 'Paralyze Res Up', Katsu 'Sleep', Shrimp 'Amnesia', Beef 'Deprave'… | high |
| 0x144 | `res_paralysis` | i32 | resistance-up effect for paralysis (Go Go 'Paralyze Res Up', Katsu 'Sleep', Shrimp 'Amnesia', Beef… | high |
| 0x148 | `res_amnesia` | i32 | resistance-up effect for amnesia (Go Go 'Paralyze Res Up', Katsu 'Sleep', Shrimp 'Amnesia', Beef 'Deprave'… | high |
| 0x14C | `res_deprave` | i32 | resistance-up effect for deprave (Go Go 'Paralyze Res Up', Katsu 'Sleep', Shrimp 'Amnesia', Beef 'Deprave'… | high |
| 0x184 | `HP_regen` | i32 | HP recovery per turn effect (Beef Curry 'HP Recovery') | high |
| 0x188 | `SP_regen` | i32 | SP recovery per turn effect (Seafood Curry 'SP Recovery') | high |
| 0x190 | `EXP_up` | i32 | EXP gain up (Exclusive Curry) | high |
| 0x194 | `mana_up` | i32 | Mana gain up (Exclusive Curry; also 1 on Go Go Curry) | medium |
| 0x198 | `HL_up` | i32 | HL gain up (Exclusive Curry) | high |
| 0x1A0 | `enemy_revenge_up` | i32 | enemy Revenge gauge up (Dengeki Rage Curry) | high |
| 0x1AC | `icon` | i32 | icon/graphic variant (1 or 2) | low |
| 0x1B0 | `desc (×5 languages)` | str(132) |  | high |
| 0x444 | `effects (×5 languages)` | str(60) |  | high |
| 0x570 | `text3 (×5 languages)` | str(60) |  | medium |

## DemoCheckDataBase.dat

Layout `fixed` · 12 bytes per row · key `id` · 3 of 3 fields named

Demo pairing / check table (160 rows).

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | demo/event id (mostly = EventViewer ids 101..2903, plus 4001, 5001) | medium |
| 0x4 | `has_link` | u32 | 1 = linked_event valid (106 rows), 0 = none | medium |
| 0x8 | `linked_event` | u32 | another event number (usually id+3 within a chapter, e.g. 101->104; DLC 2301->1811 = event no. of the next… | low |

## DLC_Delivery_HelpDataBase.dat

Layout `pool` · 24 bytes per row · key `id` · 6 of 6 fields named

Scenario summary text lines for the DLC delivery viewer (110 rows). DLC_DeliveryDataBase/SPC_Delivery type-2 rows point at the first line id.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | help line id (1000.. per scenario summary; consecutive ids = consecutive lines) | high |
| 0x4 | `text (×5 languages)` | strptr |  | high |

## DLC_DeliveryDataBase.dat

Layout `pool` · 120 bytes per row · key `id` · 30 of 30 fields named

Pool-string variant of the DLC delivery list (45 rows; FR/CHN/KOR strings are EN copies). Same meaning as SPC_Delivery.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | entry id (1..17 top level, 1001..8002 children) | high |
| 0x4 | `group` | u32 | DLC wave/group 110..150 (= DLC_ShopDataBase.delivery_group) | medium |
| 0x8 | `type` | u32 | entry type | high |
| 0xC | `value` | u32 | type 3: char.dat id (verified 510 Laharl, 520 Etna, 550 Girl Laharl, 1240 Celestial Hostess...); type 2:… | high |
| 0x10 | `name (×5 languages)` | strptr |  | high |
| 0x24 | `desc (×5 languages)` | strptr |  | high |
| 0x38 | `desc2 (×5 languages)` | strptr |  | high |
| 0x4C | `desc3 (×5 languages)` | strptr |  | high |
| 0x60 | `desc4 (×5 languages)` | strptr |  | high |
| 0x74 | `dlc_id` | u32 | DLC content id for top-level rows (1000004 Girl Laharl, 3000000.. scenarios; SPC numbering with an extra… | high |

## DLC_ShopDataBase.dat

Layout `pool` · 300 bytes per row · key `id` · 75 of 75 fields named

Pool-string variant of the DLC shop list (26 rows): label, DLC content id, 7 five-language strings each preceded by its character count (FR/CHN/KOR are EN copies).

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | shop entry id (= SPC_Shop.id) | high |
| 0x4 | `label_len` | u32 | character count of label (auto-computed on write) | high |
| 0x8 | `label` | strptr | internal identifier, e.g. DLC_SHOP_CHARA_LAHARL_CHAN | high |
| 0xC | `dlc_id` | u32 | DLC content id (100004 Girl Laharl, 300000.. scenarios, 600000.. item/money) | high |
| 0x10 | `name_JP_len` | u32 | character count of name_JP (auto-computed on write) | high |
| 0x14 | `name (×5 languages)` | strptr |  | high |
| 0x18 | `name_EN_len` | u32 | character count of name_EN (auto-computed on write) | high |
| 0x20 | `name_FR_len` | u32 | character count of name_FR (auto-computed on write) | high |
| 0x28 | `name_CHN_len` | u32 | character count of name_CHN (auto-computed on write) | high |
| 0x30 | `name_KOR_len` | u32 | character count of name_KOR (auto-computed on write) | high |
| 0x38 | `desc_JP_len` | u32 | character count of desc_JP (auto-computed on write) | high |
| 0x3C | `desc (×5 languages)` | strptr |  | high |
| 0x40 | `desc_EN_len` | u32 | character count of desc_EN (auto-computed on write) | high |
| 0x48 | `desc_FR_len` | u32 | character count of desc_FR (auto-computed on write) | high |
| 0x50 | `desc_CHN_len` | u32 | character count of desc_CHN (auto-computed on write) | high |
| 0x58 | `desc_KOR_len` | u32 | character count of desc_KOR (auto-computed on write) | high |
| 0x60 | `desc2_JP_len` | u32 | character count of desc2_JP (auto-computed on write) | high |
| 0x64 | `desc2 (×5 languages)` | strptr |  | high |
| 0x68 | `desc2_EN_len` | u32 | character count of desc2_EN (auto-computed on write) | high |
| 0x70 | `desc2_FR_len` | u32 | character count of desc2_FR (auto-computed on write) | high |
| 0x78 | `desc2_CHN_len` | u32 | character count of desc2_CHN (auto-computed on write) | high |
| 0x80 | `desc2_KOR_len` | u32 | character count of desc2_KOR (auto-computed on write) | high |
| 0x88 | `desc3_JP_len` | u32 | character count of desc3_JP (auto-computed on write) | high |
| 0x8C | `desc3 (×5 languages)` | strptr |  | high |
| 0x90 | `desc3_EN_len` | u32 | character count of desc3_EN (auto-computed on write) | high |
| 0x98 | `desc3_FR_len` | u32 | character count of desc3_FR (auto-computed on write) | high |
| 0xA0 | `desc3_CHN_len` | u32 | character count of desc3_CHN (auto-computed on write) | high |
| 0xA8 | `desc3_KOR_len` | u32 | character count of desc3_KOR (auto-computed on write) | high |
| 0xB0 | `desc4_JP_len` | u32 | character count of desc4_JP (auto-computed on write) | high |
| 0xB4 | `desc4 (×5 languages)` | strptr |  | high |
| 0xB8 | `desc4_EN_len` | u32 | character count of desc4_EN (auto-computed on write) | high |
| 0xC0 | `desc4_FR_len` | u32 | character count of desc4_FR (auto-computed on write) | high |
| 0xC8 | `desc4_CHN_len` | u32 | character count of desc4_CHN (auto-computed on write) | high |
| 0xD0 | `desc4_KOR_len` | u32 | character count of desc4_KOR (auto-computed on write) | high |
| 0xD8 | `received_JP_len` | u32 | character count of received_JP (auto-computed on write) | high |
| 0xDC | `received (×5 languages)` | strptr |  | high |
| 0xE0 | `received_EN_len` | u32 | character count of received_EN (auto-computed on write) | high |
| 0xE8 | `received_FR_len` | u32 | character count of received_FR (auto-computed on write) | high |
| 0xF0 | `received_CHN_len` | u32 | character count of received_CHN (auto-computed on write) | high |
| 0xF8 | `received_KOR_len` | u32 | character count of received_KOR (auto-computed on write) | high |
| 0x100 | `not_received_JP_len` | u32 | character count of not_received_JP (auto-computed on write) | high |
| 0x104 | `not_received (×5 languages)` | strptr |  | high |
| 0x108 | `not_received_EN_len` | u32 | character count of not_received_EN (auto-computed on write) | high |
| 0x110 | `not_received_FR_len` | u32 | character count of not_received_FR (auto-computed on write) | high |
| 0x118 | `not_received_CHN_len` | u32 | character count of not_received_CHN (auto-computed on write) | high |
| 0x120 | `not_received_KOR_len` | u32 | character count of not_received_KOR (auto-computed on write) | high |
| 0x128 | `delivery_group` | u32 | DLC wave/group 100..150 (= DLC_DeliveryDataBase.group) | medium |

## dungeon.dat

Layout `fixed` · 336 bytes per row · key `id` · 28 of 41 fields named

Stages/maps (story stages, challenge/carnage stages, post-game/DLC scenario maps, Trials, hubs, Item World rooms). map_id selects Map/map%05d (.hex/.chr/.gop/.obf) - enemies/geo panels are in the map files, not here. Netherworld text (WorldMapHelpDataBase id 1000+N) corresponds to map_id//100-1 (40xx -> 1029). Quest.unlock_stage refers to stage_key; Quest cond_type 4 targets and QuestEnemy.map refer to map_id; talk.dat entry ids are map ids.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | row id = row index 0..222 | high |
| 0x4 | `map_id` | u32 | → `map` map file number: _extracted/Map/Map_JP/map%05d (verified 214/223 exist; 80001 Reward Room, 90001 Unique… | high |
| 0x10 | `weather_effect` | u32 | → `MakaiSkillDataBase.dat` the stage's Netherworld skill (MakaiSkillDataBase id; 0 = none), switched on when the battle starts… | high |
| 0x14 | `episode` | u16 | 0-based story episode (0 = Ep.1 ... 15 = finale), 16 = post-game/DLC, 17 = Trials, 65535 = not a battle… | high |
| 0x16 | `stage_key` | u16 | stage id used by Quest.unlock_stage (5000+episode*10+stage: 5011..5402, 4099 test map); 0 for demo/hub maps | high |
| 0x18 | `stage_rank` | u16 | 1..37 difficulty tier, rises ~every 2 story stages (Ep1 1-2 ... Ep15 31-33, post-game 33-36, Baal 37);… | medium |
| 0x1A | `camera_angle` | u16 | initial camera yaw in degrees (315 default, 225 Trials, 45 "Researching Love", 0 hubs) | high |
| 0x1C | `is_story_stage` | u8 | 1 = regular stage (story / post-game), 0 = challenge/carnage/demo/test/hub | medium |
| 0x1D | `stage_type` | u8 | 0 normal, 1 extra (challenge stages, overlord fights, post-game), 2 carnage (Carnage stages, Song of Calamity) | medium |
| 0x1E | `point1_x` | u8 | coordinate-like byte, 255 = unused (set on 9 stages only: 106,502,703,1003,1005,1303,1501,1506); pairs… | low |
| 0x1F | `point2_x` | u8 | coordinate-like byte, 255 = unused (set on 9 stages only: 106,502,703,1003,1005,1303,1501,1506); pairs… | low |
| 0x20 | `point3_x` | u8 | coordinate-like byte, 255 = unused (set on 9 stages only: 106,502,703,1003,1005,1303,1501,1506); pairs… | low |
| 0x21 | `point4_x` | u8 | coordinate-like byte, 255 = unused (set on 9 stages only: 106,502,703,1003,1005,1303,1501,1506); pairs… | low |
| 0x22 | `point1_y` | u8 | see point1_x | low |
| 0x23 | `point2_y` | u8 | see point2_x | low |
| 0x24 | `point3_y` | u8 | see point3_x | low |
| 0x25 | `point4_y` | u8 | see point4_x | low |
| 0x36 | `base_x` | u8 | 0..24; small coordinate, plausibly base panel X | low |
| 0x37 | `base_y` | u8 | 0..27; plausibly base panel Y | low |
| 0x38 | `special_mode` | u8 | 0 normally; 3 on 405/407 (Dreaming Mushroom/Carnage 4), 2 on 1605/1607 (final boss stages); goes with… | low |
| 0x3C | `special_value1` | u32 | 90 (405), 170 (1605), 380 (1607) | low |
| 0x40 | `special_param` | u32 | 2 on 1605/1607 | low |
| 0x44 | `special_value2` | u32 | 170 (1605), 380 (1607) | low |
| 0x49 | `name (×5 languages)` | str(52) | stage name | high |

## EffectTableDataBase.dat

Layout `fixed` · 8 bytes per row · key `effect_id` · 2 of 2 fields named

List of battle effect models (383 x 8): one row per Start/Effect/effectNNNN.obf file (exact 1:1 match, sorted), plus a percentage parameter.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `effect_id` | u32 | → `effect` effect file number: Start_JP/Effect/effect%04d.obf (100..2563) | high |
| 0x4 | `scale` | u32 | 100 for 354 rows, 110/120/130/140 for 29 (mostly large effects); probably a size/scale or memory-budget… | low |

## Ending.dat

Layout `fixed` · 1108 bytes per row · key `id` · 12 of 12 fields named

Endings list (gallery / records).

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | ending 1..6 | high |
| 0x4 | `name (×5 languages)` | str(84) | ending name | high |
| 0x1A8 | `desc (×5 languages)` | str(136) | how to get it | high |
| 0x450 | `flag` | u32 | → `flag:FlagListDataBase.dat` flag set when the ending was seen: 2120-2124 FLAG_BAD_ENDING_1..5, 2110 FLAG_TRUE_ENDING | high |

## Enemy_Create_TableDataBase.dat

Layout `fixed` · 28 bytes per row · 0 of 7 fields named

*No fields named yet.*

## Enemy_Parameters_TableDataBase

Layout `fixed` · 8 bytes per row · 0 of 4 fields named

*No fields named yet.*

## EventCg.dat

Layout `fixed` · 1112 bytes per row · key `id` · 15 of 15 fields named

Event CG gallery entries.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | CG id 1..54 | high |
| 0x4 | `name (×5 languages)` | str(84) | CG title | high |
| 0x1A8 | `desc (×5 languages)` | str(136) | CG description | high |
| 0x450 | `unlock_flag` | u16 | → `flag:FlagListDataBase.dat` flag that unlocks the CG in the gallery (14601.. FLAG_EVENT_VIEWER_CGn) | high |
| 0x452 | `anm` | u16 | → `anm` anm file with the picture (anm08301-08317 event CGs, 09035-09040 battle results, 09300 Chara World) | high |
| 0x454 | `anim_id` | u16 | animation number inside that anm (10, 20, 101..132) | high |
| 0x456 | `order` | u16 | gallery display order 0..53 | high |

## EventViewer.dat

Layout `fixed` · 484 bytes per row · key `id` · 27 of 28 fields named

Event (cutscene) replay viewer list. Flag groups: 4 x 4 u16 flag ids + 4 u8 counts (same structure as SkitFlagList); DLC episodes require FLAG_DLC_SCENARIO_OPEN_* (18100..) and a DLC demo flag (2130..).

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | 1..30 = chapter header rows, then entries chapter*100+n (101.., 2301.. DLC) | high |
| 0x4 | `event_no` | u16 | event (demo script) number played (100.., 1801.. DLC); 0 for headers | high |
| 0x6 | `name (×5 languages)` | str(88) | title in the event viewer | high |
| 0x1BE | `req_on1` | u16 | → `flag:FlagListDataBase.dat` flags that must all be ON for the entry to be listed | medium |
| 0x1C0 | `req_on2` | u16 | → `flag:FlagListDataBase.dat` flags that must all be ON for the entry to be listed | medium |
| 0x1C2 | `req_on3` | u16 | → `flag:FlagListDataBase.dat` flags that must all be ON for the entry to be listed | medium |
| 0x1C4 | `req_on4` | u16 | → `flag:FlagListDataBase.dat` flags that must all be ON for the entry to be listed | medium |
| 0x1C6 | `req_off1` | u16 | → `flag:FlagListDataBase.dat` flags that must be OFF (tutorial rows: 3312/3313) | low |
| 0x1C8 | `req_off2` | u16 | → `flag:FlagListDataBase.dat` flags that must be OFF (tutorial rows: 3312/3313) | low |
| 0x1CA | `req_off3` | u16 | → `flag:FlagListDataBase.dat` flags that must be OFF (tutorial rows: 3312/3313) | low |
| 0x1CC | `req_off4` | u16 | → `flag:FlagListDataBase.dat` flags that must be OFF (tutorial rows: 3312/3313) | low |
| 0x1CE | `set_on1` | u16 | → `flag:FlagListDataBase.dat` flags set ON before playback (Suicidal Decision: 1650; skits: 8000+n read flag) | medium |
| 0x1D0 | `set_on2` | u16 | → `flag:FlagListDataBase.dat` flags set ON before playback (Suicidal Decision: 1650; skits: 8000+n read flag) | medium |
| 0x1D2 | `set_on3` | u16 | → `flag:FlagListDataBase.dat` flags set ON before playback (Suicidal Decision: 1650; skits: 8000+n read flag) | medium |
| 0x1D4 | `set_on4` | u16 | → `flag:FlagListDataBase.dat` flags set ON before playback (Suicidal Decision: 1650; skits: 8000+n read flag) | medium |
| 0x1D6 | `set_off1` | u16 | → `flag:FlagListDataBase.dat` flags cleared before playback (Tragic Ending: 1650) | medium |
| 0x1D8 | `set_off2` | u16 | → `flag:FlagListDataBase.dat` flags cleared before playback (Tragic Ending: 1650) | medium |
| 0x1DA | `set_off3` | u16 | → `flag:FlagListDataBase.dat` flags cleared before playback (Tragic Ending: 1650) | medium |
| 0x1DC | `set_off4` | u16 | → `flag:FlagListDataBase.dat` flags cleared before playback (Tragic Ending: 1650) | medium |
| 0x1DE | `req_on_count` | u8 | number of used req_on entries (0..4) | high |
| 0x1DF | `req_off_count` | u8 | number of used req_off entries (0..4) | high |
| 0x1E0 | `set_on_count` | u8 | number of used set_on entries (0..4) | high |
| 0x1E1 | `set_off_count` | u8 | number of used set_off entries (0..4) | high |

## Feature.dat

Layout `fixed` · 448 bytes per row · 7 of 8 fields named

Character "feature" profile lines (752 x 448): random trivia text shown for units, keyed by (group, variant). 5-language 88-byte text slots.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `group` | u32 | profile-line group 0..408 (0-75 contiguous with up to 10 variants, then multiples of 8 with a single… | medium |
| 0x4 | `variant` | u8 | variant 0..10 within the group (2 is the common/default one: 416 rows). For groups 0-11 variants 0/1 carry… | low |
| 0x5 | `text (×5 languages)` | str(88) | character profile / trivia line ("Loves meat.") | high |

## FlagList_DebugDataBase.dat

Layout `pool` · 20 bytes per row · key `id` · 5 of 5 fields named

Debug flag names (debug menu), separate id space.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | debug flag number 0..218 | high |
| 0x4 | `label_len` | u32 | character count of label (auto-computed on write) | high |
| 0x8 | `label` | strptr | identifier DEBUG_FLAG_* | high |
| 0xC | `comment_JP_len` | u32 | character count of comment_JP (auto-computed on write) | high |
| 0x10 | `comment (×5 languages)` | strptr | developer comment (JP) | high |

## FlagList_DLCDataBase.dat

Layout `pool` · 20 bytes per row · key `id` · 5 of 5 fields named

DLC ownership flag names.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | DLC flag id: 2000000.. (shops/spa/curry), 4000000.. (magazine weapons), 5000000 (Poritan), 6000000… | high |
| 0x4 | `label_len` | u32 | character count of label (auto-computed on write) | high |
| 0x8 | `label` | strptr | identifier DLC_FLAG_* | high |
| 0xC | `comment_JP_len` | u32 | character count of comment_JP (auto-computed on write) | high |
| 0x10 | `comment (×5 languages)` | strptr | DLC name (JP) | high |

## FlagListDataBase.dat

Layout `pool` · 20 bytes per row · key `id` · 5 of 5 fields named

Named game flags (story/system/DLC/charamake...). Only names; flag state lives in the save. Not every used flag has a row (ranges such as FLAG_SKIT_READ_START 8000.. are addressed by offset). Referenced as "flag:FlagListDataBase.dat" from EventCg, Ending, viewers, SkitFlagList, Quest, ...

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | flag number (game flag bit index), 0..19701 | high |
| 0x4 | `label_len` | u32 | character count of label (auto-computed on write) | high |
| 0x8 | `label` | strptr | identifier, e.g. FLAG_SCENARIO_01_CLEAR | high |
| 0xC | `comment_JP_len` | u32 | character count of comment_JP (auto-computed on write) | high |
| 0x10 | `comment (×5 languages)` | strptr | developer comment (JP) | high |

## GE.dat

Layout `fixed` · 808 bytes per row · key `id` · 12 of 12 fields named

Geo Effects (Geo panel effects): 61 rows, id + 5-language name/description + magnitude value.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | Geo effect id 1..61 (row index+1); the Geo panel effect number used by maps | high |
| 0x4 | `name (×5 languages)` | str(44) |  | high |
| 0xE0 | `desc (×5 languages)` | str(116) | effect description; the game appends it to the panel colour ("... units on") | high |
| 0x324 | `value` | u32 | effect magnitude: % for Damage/Recovery (20) and EXP/HL/ATK/DEF/element/boost effects (50); +1 for Attack… | medium |

## Group.dat

Layout `fixed` · 1092 bytes per row · key `id` · 39 of 40 fields named

Squads (49 x 1092): name + 3 description lines, number of ranks, rank-up costs and member limits per rank. Per-rank effect texts are in GroupHelp.dat (id*10+rank). Not a character-class table.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | squad id (1..25 player squads, 101..124 enemy/story squads). GroupHelp ids are id*10 + rank | high |
| 0x4 | `name (×5 languages)` | str(52) | squad name | high |
| 0x108 | `desc1 (×5 languages)` | str(52) | description line 1 | high |
| 0x20C | `desc2 (×5 languages)` | str(52) | description line 2 | high |
| 0x310 | `desc3 (×5 languages)` | str(52) | description line 3 | high |
| 0x414 | `max_rank` | u32 | number of squad ranks (3..9); GroupHelp has one line per rank (id*10+1 .. id*10+max_rank) | high |
| 0x418 | `rank2_cost` | u32 | cost to raise the squad to rank N (rank2 cost = first value: Capture 15, Elite Four 30000; then e.g.… | medium |
| 0x41C | `rank3_cost` | u32 |  | medium |
| 0x420 | `rank4_cost` | u32 |  | medium |
| 0x424 | `rank5_cost` | u32 |  | medium |
| 0x428 | `rank6_cost` | u32 |  | medium |
| 0x42C | `rank7_cost` | u32 |  | medium |
| 0x430 | `rank8_cost` | u32 |  | medium |
| 0x434 | `rank9_cost` | u32 |  | medium |
| 0x438 | `rank1_members` | u8 | squad member limit at rank N (Capture 4,4,6,6,6,8,8 matching GroupHelp "Squad limit +2" at ranks 3/6); 0… | high |
| 0x439 | `rank2_members` | u8 |  | high |
| 0x43A | `rank3_members` | u8 |  | high |
| 0x43B | `rank4_members` | u8 |  | high |
| 0x43C | `rank5_members` | u8 |  | high |
| 0x43D | `rank6_members` | u8 |  | high |
| 0x43E | `rank7_members` | u8 |  | high |
| 0x43F | `rank8_members` | u8 |  | high |
| 0x440 | `rank9_members` | u8 |  | high |

## GroupHelp.dat

Layout `fixed` · 664 bytes per row · key `id` · 6 of 6 fields named

Squad rank effect texts (157 x 664): one line (5 languages) per squad rank; id = Group.dat id*10 + rank.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | squad id * 10 + rank (11 = Capture Squad rank 1) | high |
| 0x4 | `text (×5 languages)` | str(132) | effect gained at this squad rank ("Squad limit +2." ...) | high |

## HABIT.dat

Layout `fixed` · 1072 bytes per row · key `id` · 33 of 34 fields named

Innocents / residents (110 x 1072). level cap, id, granted evility (special innocents), names (64 B) and descriptions (144 B) in 5 languages, 18 per-item-category appearance weights (order from ref/database_0.scheme), group and value type. Item unique innocents reference id (item.dat unique_innocent).

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `level_cap` | u32 | maximum innocent level/value (stat innocents 50000, ailment & elemental 100, weapon resistance 20,… | medium |
| 0x4 | `id` | u32 | innocent id (key): 0 Jobless, 1-8 stat innocents (HP SP ATK DEF INT RES HIT SPD), 11-17 dual-stat, 21-28… | high |
| 0x8 | `evility` | u16 | → `Skill.dat` evility granted by special innocents (id 201+) -> Skill.dat id (3000-3306, 6125, 6200); 0 otherwise | high |
| 0xA | `name (×5 languages)` | str(64) | innocent name | high |
| 0x14A | `desc (×5 languages)` | str(144) | innocent description | high |
| 0x41A | `rate_consumable` | u8 | relative chance (weight) of this innocent appearing on items of this category (Item World / random items).… | medium |
| 0x41B | `rate_fist` | u8 | appearance weight on fist items | medium |
| 0x41C | `rate_sword` | u8 | appearance weight on sword items | medium |
| 0x41D | `rate_spear` | u8 | appearance weight on spear items | medium |
| 0x41E | `rate_bow` | u8 | appearance weight on bow items | medium |
| 0x41F | `rate_gun` | u8 | appearance weight on gun items | medium |
| 0x420 | `rate_axe` | u8 | appearance weight on axe items | medium |
| 0x421 | `rate_staff` | u8 | appearance weight on staff items | medium |
| 0x422 | `rate_monster_phys` | u8 | appearance weight on monster phys items | medium |
| 0x423 | `rate_monster_mag` | u8 | appearance weight on monster mag items | medium |
| 0x424 | `rate_armor` | u8 | appearance weight on armor items | medium |
| 0x425 | `rate_belt` | u8 | appearance weight on belt items | medium |
| 0x426 | `rate_shoes` | u8 | appearance weight on shoes items | medium |
| 0x427 | `rate_orb` | u8 | appearance weight on orb items | medium |
| 0x428 | `rate_glasses` | u8 | appearance weight on glasses items | medium |
| 0x429 | `rate_muscle` | u8 | appearance weight on muscle items | medium |
| 0x42A | `rate_armor7` | u8 | appearance weight on armor7 items | low |
| 0x42B | `rate_emblem` | u8 | appearance weight on emblem items | medium |
| 0x42C | `group` | u8 | innocent group: 1 stat, 2 on-attack effect (ailment/critical), 3 resistance, 4 bonus… | high |
| 0x42D | `value_type` | u8 | value display type (scheme: "#mult"): 0 stat points, 1 percent, 2 weapon resistance percent, 3 no value… | low |

## HabitAffinity.dat

Layout `fixed` · 132 bytes per row · key `id` · 49 of 49 fields named

Innocent combination table (47 x 132): for Innocent type <id> combined with the Innocent type of column j (columns ordered like the rows), the resulting Innocent type id. E.g. Dietician(1)+Master(2) -> 11 Muscleman; stat innocents 1-8 x 1-8 give the 11-17 tier, 61-75 rows are identity (no transformation), 28 always stays 28.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | → `HABIT.dat` Innocent type id (HABIT.dat innocent id, u32 at +4: 1 Dietician, 2 Master ... 75) | high |
| 0x4 | `with_0` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 0 (Jobless); 0 = no result. Columns follow the row-id… | medium |
| 0x5 | `with_1` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 1 (Dietician); 0 = no result | medium |
| 0x6 | `with_2` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 2 (Master); 0 = no result | medium |
| 0x7 | `with_3` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 3 (Gladiator); 0 = no result | medium |
| 0x8 | `with_4` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 4 (Sentry); 0 = no result | medium |
| 0x9 | `with_5` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 5 (Tutor); 0 = no result | medium |
| 0xA | `with_6` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 6 (Physician); 0 = no result | medium |
| 0xB | `with_7` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 7 (Marksman); 0 = no result | medium |
| 0xC | `with_8` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 8 (Coach); 0 = no result | medium |
| 0xD | `with_11` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 11 (Muscleman); 0 = no result | medium |
| 0xE | `with_12` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 12 (Sprinter); 0 = no result | medium |
| 0xF | `with_13` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 13 (Nerd); 0 = no result | medium |
| 0x10 | `with_14` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 14 (Hard Worker); 0 = no result | medium |
| 0x11 | `with_15` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 15 (Patient); 0 = no result | medium |
| 0x12 | `with_16` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 16 (Pugilist); 0 = no result | medium |
| 0x13 | `with_17` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 17 (Sniper); 0 = no result | medium |
| 0x14 | `with_21` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 21 (Alchemist); 0 = no result | medium |
| 0x15 | `with_22` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 22 (Hypnotist); 0 = no result | medium |
| 0x16 | `with_23` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 23 (Witch Doctor); 0 = no result | medium |
| 0x17 | `with_24` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 24 (Amnesiac); 0 = no result | medium |
| 0x18 | `with_25` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 25 (Drainer); 0 = no result | medium |
| 0x19 | `with_26` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 26 (Shrinker); 0 = no result | medium |
| 0x1A | `with_27` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 27 (Charmer); 0 = no result | medium |
| 0x1B | `with_28` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 28 (Professional); 0 = no result | medium |
| 0x1C | `with_41` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 41 (Pharmacist); 0 = no result | medium |
| 0x1D | `with_42` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 42 (Coffee Maker); 0 = no result | medium |
| 0x1E | `with_43` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 43 (Medicine Man); 0 = no result | medium |
| 0x1F | `with_44` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 44 (Psychologist); 0 = no result | medium |
| 0x20 | `with_45` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 45 (Toxicologist); 0 = no result | medium |
| 0x21 | `with_46` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 46 (Expander); 0 = no result | medium |
| 0x22 | `with_47` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 47 (Pastor); 0 = no result | medium |
| 0x23 | `with_51` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 51 (Firefighter); 0 = no result | medium |
| 0x24 | `with_52` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 52 (Aeronaut); 0 = no result | medium |
| 0x25 | `with_53` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 53 (Cryophile); 0 = no result | medium |
| 0x26 | `with_61` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 61 (Boxing Coach); 0 = no result | medium |
| 0x27 | `with_62` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 62 (Fencing Coach); 0 = no result | medium |
| 0x28 | `with_63` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 63 (Spear Teacher); 0 = no result | medium |
| 0x29 | `with_64` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 64 (Archery Coach); 0 = no result | medium |
| 0x2A | `with_65` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 65 (Marksman Coach); 0 = no result | medium |
| 0x2B | `with_66` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 66 (Lumberjack Coach); 0 = no result | medium |
| 0x2C | `with_67` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 67 (Staff Twirler); 0 = no result | medium |
| 0x2D | `with_68` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 68 (Monster Trainer); 0 = no result | medium |
| 0x2E | `with_71` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 71 (Broker); 0 = no result | medium |
| 0x2F | `with_72` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 72 (Statistician); 0 = no result | medium |
| 0x30 | `with_73` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 73 (Manager); 0 = no result | medium |
| 0x31 | `with_74` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 74 (Instructor); 0 = no result | medium |
| 0x32 | `with_75` | u8 | → `HABIT.dat` resulting Innocent type id when combined with type 75 (Mentor); 0 = no result | medium |
| 0x33 | `unused_033` | bytes(81) | room for more partner columns (u8 x 81, always 0): record = u32 id + u8[128] | high |

## HOSPITAL.dat

Layout `fixed` · 20 bytes per row · key `id` · 5 of 6 fields named

Hospital prize list (33 rows): an item is awarded when all non-zero requirements (HP healed, SP healed, revive count) are reached.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `hp_healed_req` | u32 | required cumulative HP healed at the hospital (200 .. 3,840,000) | high |
| 0x4 | `sp_healed_req` | u32 | required cumulative SP healed (50 .. 480,000) | high |
| 0x8 | `id` | u32 | reward id 1..33 | high |
| 0xC | `revive_req` | u16 | required number of revivals (5 .. 300) | high |
| 0xE | `reward_item` | u16 | → `item.dat` item given: muscles (HP rows), orbs (SP rows), recovery items (revive rows), emblems (mixed rows) | high |

## item.dat

Layout `fixed` · 1120 bytes per row · key `id` · 38 of 43 fields named

Items: weapons, armor, accessories and consumables (1364 rows x 1120 bytes). Price and 8 stat bonuses are 64-bit. Key 'id' is used by shop/drop/delivery/exchange tables. unique_innocent -> HABIT.dat id, use_skill -> magic.dat id, weapon_anm -> anm file. Rows 0..~1130 are equipment in order fist, sword, spear, bow, gun, axe, staff, monster, armor, belt, shoes, orb, glasses, muscle, emblem; each normal series is followed by its +10000 high-rank copy.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `price` | u64 | shop price in HL (u64; e.g. Thimble 80, Ultimus 300,000,000) | high |
| 0x8 | `HP` | i64 | base HP bonus of the item (signed 64-bit; axes have negative HIT/SPD) | high |
| 0x10 | `SP` | i64 | base SP bonus of the item (signed 64-bit; axes have negative HIT/SPD) | high |
| 0x18 | `ATK` | i64 | base ATK bonus of the item (signed 64-bit; axes have negative HIT/SPD) | high |
| 0x20 | `DEF` | i64 | base DEF bonus of the item (signed 64-bit; axes have negative HIT/SPD) | high |
| 0x28 | `INT` | i64 | base INT bonus of the item (signed 64-bit; axes have negative HIT/SPD) | high |
| 0x30 | `RES` | i64 | base RES bonus of the item (signed 64-bit; axes have negative HIT/SPD) | high |
| 0x38 | `HIT` | i64 | base HIT bonus of the item (signed 64-bit; axes have negative HIT/SPD) | high |
| 0x40 | `SPD` | i64 | base SPD bonus of the item (signed 64-bit; axes have negative HIT/SPD) | high |
| 0x48 | `id` | u32 | item id (unique; weapons 101..,  +10000 = high-rank copies, armor 901.., consumables 2001.., DLC 5101..).… | high |
| 0x4C | `icon` | u16 | menu icon id (0 fist,1 sword,2 spear,3 bow,4 gun,5 axe,6 staff,7 monster, 13 monster-spear, 21 medal, 29… | medium |
| 0x4E | `category` | u16 | item sub-category shown in menus/sorting | high |
| 0x50 | `MOV` | u16 | movement bonus (shoes 1..4, Baal gear 1) | high |
| 0x52 | `CNT` | u16 | counter bonus (fists 1..2) | high |
| 0x54 | `unique_innocent` | u16 | → `HABIT.dat` innocent (resident) always attached to the item, 0 = none. Values are HABIT.dat ids (247 Burner on fire… | high |
| 0x56 | `weapon_anm` | u16 | → `anm` weapon model/sprite file (anmNNNNN.dat); 0 = no weapon graphic (armor/items). 415 of 416 values exist in… | high |
| 0x58 | `not_in_shop` | u8 | 1 = not sold in the shop (all +10000 high-rank items, rare/special items); 0 = normal shop stock | medium |
| 0x59 | `name (×5 languages)` | str(64) |  | high |
| 0x199 | `desc (×5 languages)` | str(136) |  | high |
| 0x446 | `rank` | u8 | item rank (1..40 normal series, 101..140 for the +10000 high-rank copies; char-exclusive items 39, DLC… | high |
| 0x448 | `RNG` | u8 | attack range (fist/sword/axe 1, spear 2, bow 4-6, gun 5-7, staff 2-3); for usable items the use range | high |
| 0x449 | `JMP` | u8 | jump bonus (shoes 2..34, Baal gear 12) | high |
| 0x44B | `CRT` | u8 | critical rate % (weapons 2..30) | high |
| 0x44C | `weapon_param_44C` | u32 | per-weapon-type constant: fist/sword/spear/bow/monster 5, gun 3, axe 30, staff 10 (some special weapons… | low |
| 0x450 | `use_skill` | u32 | → `magic.dat` magic.dat id executed when the item is used: attack items 960..983 ('(Item Skill)'), steal hands 5… | high |
| 0x454 | `item_group` | u16 | grouping: 1 normal weapon, 2 special/legendary/DLC weapon, 3 char-exclusive, 4 char-exclusive XL, 5… | low |
| 0x456 | `dlc_pct_456` | u16 | 100 normally; 110..150 on crossover/DLC weapons (Prinny Book 110 ... Zetta Sword 150). Probably a… | low |
| 0x458 | `char_exclusive` | u8 | 1 = character-specific weapon (Usalia Fists, Usanagi Sword, Dragzwei, Starfox Cane, Desco Sword, Beryl… | medium |
| 0x459 | `equip_type` | u8 | equipment class | high |
| 0x45A | `sort_order` | u16 | list/encyclopedia sort position (mostly equals row index; 0xFFFF = not listed, e.g. Runeblades) | medium |

## itemexchange.dat

Layout `fixed` · 12 bytes per row · key `id` · 3 of 5 fields named

Point-threshold item rewards (52 rows): when a counter reaches 'points', 'item' is given. The counter itself is not identified (low); rewards are IW items, emblems and gag weapons.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `points` | u16 | required cumulative points/threshold (10, 25, 50, 75, 100, 150 ... 4700 in steps of 100) | medium |
| 0x2 | `item` | u16 | → `item.dat` reward item id (Diver-5, Dark Rosary, gag weapons TV/Watermelon/Pie...) | high |
| 0x4 | `id` | u16 | entry id 1..52 (= row index + 1) | high |

## ItemGetSkillDataBase.dat

Layout `fixed` · 16 bytes per row · key `entry_id` · 4 of 4 fields named

Skill Shop list (245 x 16): spells and generic evilities that can be bought/learned. kind 2 = magic.dat spell (entries 2000-2046: Fire..Tera, Heal, buffs, debuffs, ailments, Geo), kind 1 = Skill.dat evility (entries 4001-4197, the generic 4000-4324 shop evilities).

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `entry_id` | u32 | list entry id (2000+n for spells, 4001+n for evilities; equals the evility id up to 4033). Not an item.dat id | medium |
| 0x4 | `kind` | u32 | what skill_id refers to | high |
| 0x8 | `skill_id` | u32 | magic.dat id (kind 2) or Skill.dat id (kind 1) | high |
| 0xC | `shop_rank` | u32 | 0..4; tier within a family (Fire 0, Mega 1.. Tera 4; evilities 0-4). Probably shop rank needed to unlock | medium |

## ItemPersonalityCorrection.dat

Layout `fixed` · 132 bytes per row · key `id` · 1 of 129 fields named

20 rows x (id + 128 bytes). Almost all zero: only row 2 has byte[4+1]=50 and byte[4+69]=100 (percent-like). Probably a leftover item 'personality' correction table from earlier titles; layout of the 128 bytes unknown (kept as u8).

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | entry id 1..20 | high |

## ItemWorld_ProbabilityDataBase.d

Layout `fixed` · 44 bytes per row · key `id` · 11 of 11 fields named

Item World probability/parameter table: each id has 10 values (probably one per 10-floor band or rarity tier). Rows 0..11 are graded (row 2: 90,85,...,45; row 10: 1..10), rows 500+ are mostly all 1. The baseline mis-detected strings here; these are plain u32.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | entry id (0..11, 500..510, 600..611, 650..651) | high |
| 0x4 | `value1` | u32 | observed 0..90, 5 distinct; top 1(x23), 5(x6), 0(x6), 90(x1), 10(x1) | low |
| 0x8 | `value2` | u32 | observed 1..85, 6 distinct; top 1(x27), 7(x5), 2(x2), 85(x1), 14(x1), 8(x1) | low |
| 0xC | `value3` | u32 | observed 1..80, 6 distinct; top 1(x25), 10(x6), 3(x3), 80(x1), 17(x1), 2(x1) | low |
| 0x10 | `value4` | u32 | observed 1..75, 8 distinct; top 1(x25), 12(x5), 4(x2), 75(x1), 20(x1), 5(x1) | low |
| 0x14 | `value5` | u32 | observed 1..70, 8 distinct; top 1(x25), 15(x5), 5(x2), 70(x1), 22(x1), 8(x1) | low |
| 0x18 | `value6` | u32 | observed 1..65, 7 distinct; top 1(x25), 17(x5), 10(x2), 6(x2), 65(x1), 25(x1) | low |
| 0x1C | `value7` | u32 | observed 1..60, 8 distinct; top 1(x25), 20(x5), 7(x2), 60(x1), 26(x1), 14(x1) | low |
| 0x20 | `value8` | u32 | observed 1..55, 8 distinct; top 1(x25), 22(x5), 8(x2), 55(x1), 29(x1), 16(x1) | low |
| 0x24 | `value9` | u32 | observed 1..50, 8 distinct; top 1(x25), 25(x5), 9(x2), 50(x1), 32(x1), 18(x1) | low |
| 0x28 | `value10` | u32 | observed 1..45, 7 distinct; top 1(x25), 30(x5), 10(x3), 45(x1), 35(x1), 20(x1) | low |

## ItemWorld_Room_CharDataDataBase

Layout `pool` · 80 bytes per row · key `id` · 18 of 20 fields named

NPCs placed in Item World rooms (279 rows, pool strings). Links room -> char.dat class and position.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | NPC entry id (0 Guide, 100.. civilians, 200.. innocents, 300.. commuting prinnies, 1001 peddler, 1800… | high |
| 0x4 | `name_JP_len` | u32 | character count of name_JP (auto-computed on write) | high |
| 0x8 | `name (×5 languages)` | strptr |  | high |
| 0xC | `name_EN_len` | u32 | character count of name_EN (auto-computed on write) | high |
| 0x14 | `name_FR_len` | u32 | character count of name_FR (auto-computed on write) | high |
| 0x1C | `name_CHN_len` | u32 | character count of name_CHN (auto-computed on write) | high |
| 0x24 | `name_KOR_len` | u32 | character count of name_KOR (auto-computed on write) | high |
| 0x2C | `room_id` | u32 | → `ItemWorld_RoomDataBase.dat` room the NPC appears in (ItemWorld_RoomDataBase id; 1000 = generic, some values like 102 not in that table) | medium |
| 0x30 | `char_id` | u32 | → `char.dat` class/character of the NPC: char.dat id (char offset 208; 2010 Prinny, 3500 Mil ...). 7524 and 9996..9999… | high |
| 0x38 | `level` | u32 | NPC level (mostly 100; 9999 for Guardians) | low |
| 0x3C | `talk_id` | u32 | message/script id (3060000/10/20/30 for commuting prinnies, 4500001.. innocents); 0 = none | low |
| 0x40 | `x` | i32 | map x position | medium |
| 0x44 | `y` | i32 | map y position | medium |
| 0x48 | `dir` | i32 | facing angle (0/90/180/270) | medium |

## ItemWorld_RoomDataBase.dat

Layout `fixed` · 44 bytes per row · key `id` · 11 of 11 fields named

Item World secret/small rooms (小部屋): room id -> map file, two map positions and the room flag.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | room id (1..11, 51..62, 151, 152, 1000); referenced by ItemWorld_Room_CharDataDataBase.room_id | high |
| 0x4 | `map` | i32 | → `map` map file number (map40001 ... map40152 exist in Map_JP); -1 for id 1000 | high |
| 0x8 | `map2` | i32 | always -1 (alternative map slot?) | low |
| 0xC | `map3` | i32 | always -1 (alternative map slot?) | low |
| 0x10 | `pos1_x` | i32 | map position 1 x (entry?) | low |
| 0x14 | `pos1_y` | i32 | map position 1 y | low |
| 0x18 | `pos1_dir` | i32 | facing angle (0/180) | low |
| 0x1C | `pos2_x` | i32 | map position 2 x (exit?) | low |
| 0x20 | `pos2_y` | i32 | map position 2 y | low |
| 0x24 | `pos2_dir` | i32 | always 0 | low |
| 0x28 | `flag` | u32 | → `flag:FlagListDataBase.dat` FlagListDataBase id '小部屋番号N' (small-room number flag, 15300+); 0 for id 1000 | high |

## JobRank.dat

Layout `fixed` · 20 bytes per row · key `id` · 11 of 12 fields named

Class rank (tier) chain for generic classes: one row per class tier (325 rows x 20 bytes; 1010-1265 human classes incl. DLC Celestial Hostess/Kunoichi/Sorcerer, 2010-2215 monsters, 9930-9995 unused dummy chains). Gives mastery EXP requirement, next tier id and per-rank stat bonus. A new generic class needs 6 rows here (ids family*10+0..5).

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | → `char.dat` char.dat id of the class tier this row describes (1010 = Warrior tier 0 "Fighter"); 0 = empty row 0 | high |
| 0x4 | `mastery_exp` | u32 | class-mastery EXP needed for this rank: base x 1,4,16,64,256,4096 (Warrior base 64: 64..262144; Thief 96;… | medium |
| 0x8 | `next_id` | u16 | → `char.dat` char.dat id of the next tier (1010->1011 ... 1015->1010 wraps). Asagi 1230.. points to 1237..1242 (no such… | high |
| 0xA | `bonus_HP` | u8 | permanent stat bonus granted by reaching this class-mastery rank (HP SP ATK DEF INT RES HIT SPD). Pattern… | medium |
| 0xB | `bonus_SP` | u8 |  | medium |
| 0xC | `bonus_ATK` | u8 |  | medium |
| 0xD | `bonus_DEF` | u8 |  | medium |
| 0xE | `bonus_INT` | u8 |  | medium |
| 0xF | `bonus_RES` | u8 |  | medium |
| 0x10 | `bonus_HIT` | u8 |  | medium |
| 0x11 | `bonus_SPD` | u8 |  | medium |

## magic.dat

Layout `fixed` · 1140 bytes per row · key `id` · 81 of 85 fields named

Skills / spells (583 x 1140). Descriptions (148 B x5) at +0x0C, names (48 B x5) at +0x2F0; numeric block at +0x3E0: 6 effect slots (type u8 + value i16), type/weapon/element/formula bytes, skill-EXP and per-level power tables, range, AttackMap shape, height limits, combo partners, owner char ids (user1..4), animation id, base/dual links. Learnsets are in char.dat (+0x14: 16 x u32 magic ids, +0x54: 16 x u16 learn levels); weapon skills are learned by weapon mastery (learn_level); generic spells/evilities are also sold via ItemGetSkillDataBase.dat.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | skill id (key). Ranges: 1-22 system/Base-Panel commands, 101-607 weapon skills (hundreds digit = weapon: 1… | high |
| 0x4 | `sub_type` | u16 | 0 for most skills. Throw-item skills 960-984: 1 physical objects (spear, axe, ball), 3 elemental/holy… | low |
| 0x6 | `sp_cost` | u16 | base SP cost (Fire 4, Mega 16, Giga 49, Omega 96, Tera 128; weapon skills 4/12/24/48/96/160/255;… | high |
| 0x8 | `learn_level` | u16 | weapon skills: Weapon Mastery level needed (1,3,7,15,25,50; 999 for the ultimate 7th skill); tower skills… | medium |
| 0xA | `rank` | u16 | tier inside the skill family: weapon skills 0..6, spells 0/2/4/6/8 (Fire..Tera), buffs 1, unique 0..3,… | medium |
| 0xC | `desc (×5 languages)` | str(148) | skill description ("Fist:%s<text>", "Unique:%s..", "Combo (Name):%s.."; the game fills %s) | high |
| 0x2F0 | `name (×5 languages)` | str(48) | skill name | high |
| 0x3E0 | `effect1_type` | u8 | effect code of slot 1 (see enum). Slot 1 is the main effect (1 = damage/heal), further slots add ailments… | high |
| 0x3E1 | `effect2_type` | u8 | effect code of slot 2 (see enum). Slot 1 is the main effect (1 = damage/heal), further slots add ailments… | high |
| 0x3E2 | `effect3_type` | u8 | effect code of slot 3 (see enum). Slot 1 is the main effect (1 = damage/heal), further slots add ailments… | medium |
| 0x3E3 | `effect4_type` | u8 | effect code of slot 4 (see enum). Slot 1 is the main effect (1 = damage/heal), further slots add ailments… | medium |
| 0x3E4 | `effect5_type` | u8 | effect code of slot 5 (see enum). Slot 1 is the main effect (1 = damage/heal), further slots add ailments… | medium |
| 0x3E5 | `effect6_type` | u8 | effect code of slot 6 (see enum). Slot 1 is the main effect (1 = damage/heal), further slots add ailments… | medium |
| 0x3E6 | `effect1_value` | i16 | value for effect1_type: damage (1) = power modifier % (Fire -10, Mega 5, Giga 30, Omega 50, Tera 70; Tera… | medium |
| 0x3E8 | `effect2_value` | i16 | value for effect2_type: damage (1) = power modifier % (Fire -10, Mega 5, Giga 30, Omega 50, Tera 70; Tera… | medium |
| 0x3EA | `effect3_value` | i16 | value for effect3_type: damage (1) = power modifier % (Fire -10, Mega 5, Giga 30, Omega 50, Tera 70; Tera… | medium |
| 0x3EC | `effect4_value` | i16 | value for effect4_type: damage (1) = power modifier % (Fire -10, Mega 5, Giga 30, Omega 50, Tera 70; Tera… | medium |
| 0x3EE | `effect5_value` | i16 | value for effect5_type: damage (1) = power modifier % (Fire -10, Mega 5, Giga 30, Omega 50, Tera 70; Tera… | medium |
| 0x3F0 | `effect6_value` | i16 | value for effect6_type: damage (1) = power modifier % (Fire -10, Mega 5, Giga 30, Omega 50, Tera 70; Tera… | medium |
| 0x3F2 | `skill_type` | u8 | skill class | high |
| 0x3F3 | `moves_units` | u8 | 1 = skill relocates the user or the target (Triple Strike, Hurricane Blow, every "(Blow)" skill, Ninja Art… | medium |
| 0x3F4 | `weapon_type` | u8 | associated weapon / mastery | high |
| 0x3F5 | `element` | u8 | element (Fire family 1, Wind 2, Ice 3, Star 4; Hellfire Shot 1, Cryo Heartbreaker 3) | high |
| 0x3F6 | `formula` | u8 | effect class / damage formula | high |
| 0x3F7 | `motion_type` | u8 | user motion: 1..6 weapon pose (same codes as weapon_type), 0 generic, 8/9/10 Magichange (9 single, 10… | medium |
| 0x3F8 | `exp_lv1` | u32 | skill EXP needed to reach the next level. Main table x4 per level: 80,320,1280,...,5242880,0; buffs… | medium |
| 0x3FC | `exp_lv2` | u32 | skill EXP needed at level 2 | medium |
| 0x400 | `exp_lv3` | u32 | skill EXP needed at level 3 | medium |
| 0x404 | `exp_lv4` | u32 | skill EXP needed at level 4 | medium |
| 0x408 | `exp_lv5` | u32 | skill EXP needed at level 5 | medium |
| 0x40C | `exp_lv6` | u32 | skill EXP needed at level 6 | medium |
| 0x410 | `exp_lv7` | u32 | skill EXP needed at level 7 | medium |
| 0x414 | `exp_lv8` | u32 | skill EXP needed at level 8 | medium |
| 0x418 | `exp_lv9` | u32 | skill EXP needed at level 9 | medium |
| 0x41C | `exp_lv10` | u32 | skill EXP needed at level 10 | medium |
| 0x420 | `power_lv1` | i16 | primary magnitude per skill level: attack power % (100,107,...,163), buff % (30..75), debuff % (-30..-57),… | medium |
| 0x422 | `power_lv2` | i16 | primary magnitude at skill level 2 | medium |
| 0x424 | `power_lv3` | i16 | primary magnitude at skill level 3 | medium |
| 0x426 | `power_lv4` | i16 | primary magnitude at skill level 4 | medium |
| 0x428 | `power_lv5` | i16 | primary magnitude at skill level 5 | medium |
| 0x42A | `power_lv6` | i16 | primary magnitude at skill level 6 | medium |
| 0x42C | `power_lv7` | i16 | primary magnitude at skill level 7 | medium |
| 0x42E | `power_lv8` | i16 | primary magnitude at skill level 8 | medium |
| 0x430 | `power_lv9` | i16 | primary magnitude at skill level 9 | medium |
| 0x432 | `power_lv10` | i16 | primary magnitude at skill level 10 | medium |
| 0x434 | `area_flag` | u8 | 0/1; 1 mostly on skills whose AttackMap shape has more than one cell (257 of 266 set rows). Exact meaning… | low |
| 0x435 | `range` | u8 | targeting range in panels (Rapid Arrow 4, Tri-Burst 5, Hellfire Shot 3, 99 = map-wide). 0 = shape placed… | medium |
| 0x436 | `range_flag` | u8 | 0/1; set on most gun skills, some bow/unique/monster skills, Killer Spin, Tower Move/Lift (59 rows). Maybe… | low |
| 0x438 | `attack_map` | u16 | → `AttackMap.dat` area shape -> AttackMap.dat id (0 = none; generic spells use 0). Shapes with an origin cell (cells kind 3)… | high |
| 0x43A | `height_up` | u8 | vertical reach upwards (12 melee, 48 spells, 99 unlimited) | medium |
| 0x43B | `height_down` | u8 | vertical reach downwards (12/18 melee, 64 spells, 99 unlimited) | medium |
| 0x43C | `flag_43C` | u8 | 0/1 (102 rows): mostly high-tier/cinematic weapon & unique skills (Big Bang, Blade Rush, Top Shot, unique… | low |
| 0x43D | `blow_distance` | u8 | knock-back distance (panels) of "(Blow)" skills: Rising Domination 5, Stratus Drive 4, Pirate Shell 3,… | medium |
| 0x43E | `blow_height_up` | u8 | Blow skills: 12/16/24 (probably knock-back height limit) | low |
| 0x43F | `blow_height_down` | u8 | Blow skills: 36/48 (probably knock-back fall limit) | low |
| 0x440 | `flag_440` | u8 | 0/1 (118 rows): set for weapon skills up to mastery 25, Fire..Giga spells, Heal..Giga Heal, most monster… | low |
| 0x441 | `tower_units` | u8 | tower skills: stacked units required (Tower Release 2, Move/Lift/Throw 3, Whip 4, Fishing 5, Laser 6) | medium |
| 0x442 | `partner1` | u16 | → `char.dat` combo (3xxxx) skills: partner character that must take part (char.dat id at +0xD0); 0 = none | high |
| 0x444 | `partner2` | u16 | → `char.dat` combo (3xxxx) skills: partner character that must take part (char.dat id at +0xD0); 0 = none | high |
| 0x446 | `partner3` | u16 | → `char.dat` combo (3xxxx) skills: partner character that must take part (char.dat id at +0xD0); 0 = none | high |
| 0x44C | `partner_count` | u8 | number of partner1..3 used (combo skills 1..3, else 0) | high |
| 0x44D | `icon` | u8 | probably skill icon/graphic class: 0 sword, 1 axe, 2 bow, 3 spear, 4 fist, 5 magic, 6 gun, 8/10… | low |
| 0x44E | `skill_group` | u8 | list group: 0 generic magic, 1 fist 2 sword 3 spear 4 bow 5 gun 6 axe, 7 character unique & combo, 8… | medium |
| 0x450 | `anim_id` | u16 | skill animation/sequence id (distinct per skill, 1..1811, 65535 = none); not an effectNNNN / anmNNNNN file… | medium |
| 0x454 | `user1` | u32 | → `char.dat` character/class that owns this skill (char.dat id at +0xD0: Killia 10, Seraphina 40, Warrior family 1010,… | high |
| 0x458 | `user2` | u32 | → `char.dat` character/class that owns this skill (char.dat id at +0xD0: Killia 10, Seraphina 40, Warrior family 1010,… | high |
| 0x45C | `user3` | u32 | → `char.dat` character/class that owns this skill (char.dat id at +0xD0: Killia 10, Seraphina 40, Warrior family 1010,… | high |
| 0x460 | `user4` | u32 | → `char.dat` character/class that owns this skill (char.dat id at +0xD0: Killia 10, Seraphina 40, Warrior family 1010,… | high |
| 0x464 | `user_count` | u16 | number of user entries: 0 generic, 1 unique/class/monster skill, 2..4 combo skills | high |
| 0x466 | `version_group` | u16 | 100 base game; 110/120/130/140/150 on DLC-character content (Laharl group 110, Valvatorez group 120, Mao… | low |
| 0x468 | `flag_468` | u32 | 0/1: set on ranged skills (bow, gun, throws, towers, lift/throw, some unique), clear on melee weapon… | low |
| 0x46C | `base_skill` | u32 | → `magic.dat` skill this one derives from (generic spells -> Dummy template 9001-9005 of the same tier; duplicated… | high |
| 0x470 | `dual_skill` | u32 | → `magic.dat` single Magichange skill -> id of its Dual Magichange version; 0 none | high |

## Makai.dat

Layout `fixed` · 1380 bytes per row · key `id` · 30 of 40 fields named

Named netherworlds (49): story worlds 1..14, class netherworlds 18..39 (featured class char id), Carnage Dimension (40), DLC/post-game worlds 41..49. Used by the Nether Research / world map; each row has a 3-line description, unlock flag, level and 6 exterior parts (MakaiEdit_ExteriorPartsDataBase ids).

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | netherworld id 1..49 | high |
| 0x4 | `name (×5 languages)` | str(34) |  | high |
| 0xAE | `desc1 (×5 languages)` | str(76) | description line 1 | high |
| 0x22A | `desc2 (×5 languages)` | str(76) | description line 2 | high |
| 0x3A6 | `desc3 (×5 languages)` | str(76) | description line 3 | high |
| 0x524 | `unlock_flag` | u16 | → `flag:FlagListDataBase.dat` available when flag ON: scenario flags 199..1712, 14018 FLAG_FACILITY_SHURA_WORLD for Carnage; 0 for extra… | medium |
| 0x526 | `level` | u16 | rises with story order (5..150), class worlds 30..175, DLC worlds 200, Carnage 1000; probably base enemy level | medium |
| 0x538 | `class_char_id` | i32 | → `char.dat` featured monster/class of class netherworlds 18..39 (e.g. 2010 Prinny Village): char.dat id (u32 at… | high |
| 0x54C | `ext_aerial` | u32 | → `MakaiEdit_ExteriorPartsDataBase` exterior part (aerial, 1000s) of the netherworld ship/planet | medium |
| 0x550 | `ext_upper` | u32 | → `MakaiEdit_ExteriorPartsDataBase` exterior part (upper, 2000s) | medium |
| 0x554 | `ext_outer` | u32 | → `MakaiEdit_ExteriorPartsDataBase` exterior part (outer, 6000s; DLC rows use 3000) | medium |
| 0x558 | `ext_core` | u32 | → `MakaiEdit_ExteriorPartsDataBase` exterior part (core, 3000s; DLC rows 4000) | medium |
| 0x55C | `ext_lower` | u32 | → `MakaiEdit_ExteriorPartsDataBase` exterior part (lower, 4000s; DLC rows 5000) | medium |
| 0x560 | `ext_optional` | u32 | → `MakaiEdit_ExteriorPartsDataBase` exterior part (optional, 5000s; DLC rows 6000) | medium |

## Makai_Invader_UniqueDataBase.da

Layout `pool` · 96 bytes per row · 10 of 34 fields named

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x4 | `name (×5 languages)` | strptr |  | medium |
| 0x18 | `desc (×5 languages)` | strptr |  | medium |

## Makai_InvaderaDataBase.dat

Layout `pool` · 148 bytes per row · 5 of 37 fields named

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x4 | `name (×5 languages)` | strptr |  | medium |

## MakaiEdit_ExteriorPartsDataBase

Layout `pool` · 92 bytes per row · key `id` · 19 of 23 fields named

Netherworld Edit exterior parts (look of your pocket netherworld / research netherworlds). 6 categories x 30. Referenced by Makai.dat ext_* fields.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | part id: 1000s aerial, 2000s upper, 3000s core, 4000s lower, 5000s optional, 6000s outer | high |
| 0x4 | `category` | u32 | part slot (from description texts) | high |
| 0x8 | `unlock_id` | i32 | 1..16 = story netherworld the part comes from (1 Blood Parch...), 101..118 extra parts, -1 dummy | low |
| 0xC | `name (×5 languages)` | strptr |  | high |
| 0x20 | `desc (×5 languages)` | strptr |  | high |
| 0x34 | `unused_text (×5 languages)` | strptr | always "ダミー" (dummy) | high |
| 0x58 | `index` | u32 | index within category 0..29 | high |

## MakaiEdit_TownEditCategoryDataB

Layout `pool` · 60 bytes per row · key `id` · 12 of 17 fields named

Pocket netherworld (base) edit: terrain block categories/shapes. Pool table with strlen fields.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | row id 1..92 | high |
| 0x4 | `shape_id` | u32 | terrain shape id (1..56, 1001..1052, 2001..2013); = id for the NxM blocks | medium |
| 0xC | `name_JP_len` | u32 | character count of name_JP (auto-computed on write) | high |
| 0x10 | `name (×5 languages)` | strptr | shape name (1x1..8x8, Square, Stairs, ...); preceded by its strlen field | high |
| 0x14 | `name_EN_len` | u32 | character count of name_EN (auto-computed on write) | high |
| 0x1C | `name_FR_len` | u32 | character count of name_FR (auto-computed on write) | high |
| 0x24 | `name_CHN_len` | u32 | character count of name_CHN (auto-computed on write) | high |
| 0x2C | `name_KOR_len` | u32 | character count of name_KOR (auto-computed on write) | high |

## MakaiName.dat

Layout `fixed` · 1864 bytes per row · key `id` · 31 of 31 fields named

Word lists and description templates used to generate random netherworld names/descriptions (Nether Research). Text only.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | row id 1..129 | high |
| 0x4 | `word1 (×5 languages)` | str(24) | name word slot 1 (adjective, e.g. "Downpour") | high |
| 0x7C | `word2 (×5 languages)` | str(24) | name word slot 2 (e.g. "Spiky"); empty in 28 rows | medium |
| 0xF4 | `word3 (×5 languages)` | str(24) | name word slot 3 (noun, e.g. "Weapon"); empty in 32 rows | medium |
| 0x16C | `desc1 (×5 languages)` | str(100) | description line 1 (may contain <char>/<item> placeholders) | high |
| 0x360 | `desc2 (×5 languages)` | str(100) | description line 2 | high |
| 0x554 | `desc3 (×5 languages)` | str(100) | description line 3 | high |

## MakaiSkillDataBase.dat

Layout `pool` · 80 bytes per row · key `id` · 18 of 20 fields named

Netherworld skills (effects active while a netherworld is dropped/called into battle). Pool table.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | netherworld skill id: 1000.. persistent, 2000.. end-of-turn, 3000.. on-call | high |
| 0x4 | `name (×5 languages)` | strptr |  | high |
| 0x18 | `desc (×5 languages)` | strptr |  | high |
| 0x30 | `trigger` | u32 | when the skill acts (matches dummy row names 永続/終了時/起動時) | high |
| 0x34 | `effect_type` | u32 | 1..4; 2 = stat/resistance modifier, 3 = gain-rate bonus (EXP/Mana/HL...), 1 = damage/one-shot, 4 = special | low |
| 0x38 | `target` | u32 | 6 for most (all units), 3 = allies (Baal debuffs), 1 = ally single?, 4 = enemies | low |
| 0x3C | `param` | u32 | affected parameter: 1 HP, 2 SP, 9 MOV, 10 JMP, 12 throw range, 16 fire/17 wind/18 water resist, 50 EXP, 51… | medium |
| 0x40 | `value` | i32 | effect value (%, or flat for MOV/JMP/range); -1 = none | medium |
| 0x44 | `value2` | u32 | secondary value (Baal Berserk DEF -50, Final Trump Card 1) | low |
| 0x4C | `index` | u32 | row index 0..99 | high |

## MapObjectTableDataBase.dat

Layout `pool` · 12 bytes per row · key `id` · 2 of 3 fields named

Map 3D object list (id -> .obf model). Pool layout without strlen field.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | map object id 1000..1059 | high |
| 0x4 | `obf_name` | strptr | 3D object file stem: Data/Object/Object_*.dat member "<name>.obf" (mapobj_00101.., chw00001.. = Chara World) | high |

## MapPartShopItemDataBase.dat

Layout `fixed` · 12 bytes per row · key `id` · 3 of 3 fields named

Base (Pocket Netherworld) edit shop: map parts for sale. Objects are char.dat 'characters' (geo objects). Parts 101..161 have no char id (probably exterior/terrain parts, cf. MakaiEdit_ExteriorPartsDataBase).

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | map-part id (1..53 = placeable objects, 101..161 = other parts) | high |
| 0x4 | `price` | u32 | price in HL (60,000 .. 1,200,000) | high |
| 0x8 | `char_id` | u32 | → `char.dat` parts 1..53: char.dat id (char offset 208) of the placeable object (5102 Blood Parch Cactus, 5217 Cactus… | high |

## music.dat

Layout `fixed` · 1048 bytes per row · key `track_id` · 15 of 17 fields named

Music gallery (Jukebox) entries: one row per selectable track. file -> Sound_BGM archive member NNN.ogg; loop points / volume of that file are in Sound_BGM_0.dat. Verified against ref/ReadMe.md + ref/database_0.scheme (layout identical).

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x8 | `file` | u32 | → `Sound_BGM_0.dat` music file number = NNN.ogg in Data/Sound_BGM/Sound_BGM_0.dat (= Sound_BGM_0.dat id). 400 = RANDOM, 401 =… | high |
| 0xC | `unlock` | u32 | 0 = unlocked by story progress, 3 = available from the start (BGM01, SONG01, RANDOM, RANDOM MEDLEY).… | medium |
| 0x10 | `title (×5 languages)` | str(44) | gallery title (e.g. "BGM 01") | high |
| 0xEC | `desc (×5 languages)` | str(160) | gallery description / real song name | high |
| 0x40C | `track_id` | i16 | track id (random-play on/off state; RANDOM & MEDLEY share 100). ReadMe: ids > 151 not picked up | high |
| 0x40E | `dlc_group` | i16 | content group: 100 = base game, 110 = D1 DLC songs (501-504), 120 = D4 (511-517), 130 = D3 (521-528), 140… | medium |
| 0x410 | `list_order` | u32 | position in the music gallery list (0..75) | high |

## name.dat

Layout `custom` · 12 bytes per row · 3 of 3 fields named

Random name lists for created/recruited generic characters. File: u32 counts[30]; u32 offsets[sum(counts)] (relative to pool start, list order); pool of NUL-terminated UTF-8 strings. Lists = 6 kinds x 5 languages (JP,EN,FR,CHN,KOR): 0-4 human male, 5-9 human female, 10-14 monster male, 15-19 monster female, 20-24 extra joke names/nicknames ("What?", "VIP", "Generic Name"; FR list only 45 entries), 25-29 extra (only 29 KOR used, 29 names). Lists are per language and NOT index-aligned (EN male starts "Dr. Klahn", JP male starts with アーカード; CHN female list has 944 entries). Pseudo-record per list entry; kind chosen by char.dat kind_gender (human/monster x male/female).

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `list` | u32 | list index 0-29 (kind*5 + language); kinds: 0 human male, 1 human female, 2 monster male, 3 monster… | high |
| 0x4 | `pool_order` | u32 | position of the string inside the pool (kept for byte-identical rewrite; new rows: use a large number) | high |
| 0x8 | `text` | strptr | the name | high |

## nameplate.dat

Layout `fixed` · 76 bytes per row · key `id` · 2 of 2 fields named

Name plate table - stub. The exe loader (0x1400c29e0) expects 0x16C-byte records (u32 id + 5 x 72-byte names), but the shipped file has a single truncated 76-byte record (id + JP name only). Do not add rows with this schema; a working table would need 364-byte records.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | only row: 99 | high |
| 0x4 | `name (×5 languages)` | str(72) | placeholder "？？？" | high |

## news.dat

Layout `fixed` · 2036 bytes per row · key `id` · 8 of 10 fields named

Netherworld News ticker messages (261 rows) with a show/hide flag window (story progress).

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | news id 1..261 | high |
| 0x4 | `hide_flag` | u32 | → `flag:FlagListDataBase.dat` news disappears once this flag is ON (0 = never). e.g. 399 FLAG_SCENARIO_03_CLEAR | high |
| 0x8 | `show_flag` | u32 | → `flag:FlagListDataBase.dat` news becomes available when this flag is ON (0 = always). scenario flags (101..1736), facility flags… | high |
| 0xE | `text (×5 languages)` | str(404) | news ticker text | high |

## OverlordSkill.dat

Layout `fixed` · 2128 bytes per row · key `id` · 22 of 22 fields named

Overload skills (49 x 2128): id, 5-language name, three 5-language description lines, script id. The battle skill itself is the magic.dat row 3000-3047 (skill_type 4); which character owns which Overload is in char.dat (+0xCC, u16 Overload id; 41 rows non-zero).

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | Overload id (key): 1-20 main story characters, 100-114 generic/story, 200-213 DLC characters. The matching… | high |
| 0x4 | `name (×5 languages)` | str(52) | Overload name | high |
| 0x108 | `desc1 (×5 languages)` | str(124) | description line 1 | high |
| 0x374 | `desc2 (×5 languages)` | str(124) | description line 2 | high |
| 0x5E0 | `desc3 (×5 languages)` | str(124) | description line 3 (mostly empty / a single full-width space) | high |
| 0x84C | `script_id` | u32 | → `script` event-script entry run for the Overload (13200000 + n*100/10 per Overload, 16100900 shared by several… | medium |

## pirate.dat

Layout `fixed` · 328 bytes per row · key `id` · 80 of 104 fields named

Item World pirate crews (16 rows, JP-only text; D4-style leftover). Crew = ship + up to 15 members (char.dat class id, level, position triple x/height/y).

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | pirate crew id 1..16 | high |
| 0x4 | `name (×5 languages)` | str(44) | crew name (JP only, e.g. ガンマンハット海賊団) | high |
| 0x30 | `ship_id` | u16 | → `ship.dat` ship.dat id (1..11); ship_obj below = 4260 + ship_id | high |
| 0x3F | `member_count` | u8 | number of crew members (1..14) | high |
| 0x54 | `ship_obj` | u16 | ship object id (4261..4271, = ship.dat ship_obj) | high |
| 0x56 | `member1_char` | u16 | → `char.dat` crew member class id (char.dat id) | medium |
| 0x58 | `member2_char` | u16 | → `char.dat` crew member class id (char.dat id) | medium |
| 0x5A | `member3_char` | u16 | → `char.dat` crew member class id (char.dat id) | medium |
| 0x5C | `member4_char` | u16 | → `char.dat` crew member class id (char.dat id) | medium |
| 0x5E | `member5_char` | u16 | → `char.dat` crew member class id (char.dat id) | medium |
| 0x60 | `member6_char` | u16 | → `char.dat` crew member class id (char.dat id) | medium |
| 0x62 | `member7_char` | u16 | → `char.dat` crew member class id (char.dat id) | medium |
| 0x64 | `member8_char` | u16 | → `char.dat` crew member class id (char.dat id) | medium |
| 0x66 | `member9_char` | u16 | → `char.dat` crew member class id (char.dat id) | medium |
| 0x68 | `member10_char` | u16 | → `char.dat` crew member class id (char.dat id) | medium |
| 0x6A | `member11_char` | u16 | → `char.dat` crew member class id (char.dat id) | medium |
| 0x6C | `member12_char` | u16 | → `char.dat` crew member class id (char.dat id) | medium |
| 0x6E | `member13_char` | u16 | → `char.dat` crew member class id (char.dat id) | medium |
| 0x70 | `member14_char` | u16 | → `char.dat` crew member class id (char.dat id) | medium |
| 0x72 | `member15_char` | u16 | → `char.dat` crew member class id (char.dat id) | medium |
| 0x74 | `member1_level` | u16 | observed 100..900, 12 distinct; top 150(x3), 300(x2), 400(x2), 250(x1), 100(x1), 200(x1) | medium |
| 0x76 | `member2_level` | u16 | observed 0..800, 12 distinct; top 300(x3), 200(x2), 130(x2), 250(x1), 400(x1), 85(x1) | medium |
| 0x78 | `member3_level` | u16 | observed 0..800, 12 distinct; top 300(x3), 200(x2), 130(x2), 250(x1), 400(x1), 85(x1) | medium |
| 0x7A | `member4_level` | u16 | observed 0..800, 12 distinct; top 300(x3), 200(x2), 130(x2), 250(x1), 400(x1), 85(x1) | medium |
| 0x7C | `member5_level` | u16 | observed 0..800, 12 distinct; top 300(x3), 200(x2), 130(x2), 250(x1), 400(x1), 85(x1) | medium |
| 0x7E | `member6_level` | u16 | observed 0..800, 10 distinct; top 0(x4), 300(x3), 200(x2), 250(x1), 85(x1), 500(x1) | medium |
| 0x80 | `member7_level` | u16 | observed 0..800, 9 distinct; top 0(x6), 200(x2), 300(x2), 250(x1), 85(x1), 130(x1) | medium |
| 0x82 | `member8_level` | u16 | observed 0..800, 7 distinct; top 0(x8), 200(x2), 300(x2), 250(x1), 85(x1), 130(x1) | medium |
| 0x84 | `member9_level` | u16 | observed 0..800, 6 distinct; top 0(x11), 250(x1), 85(x1), 200(x1), 800(x1), 300(x1) | medium |
| 0x86 | `member10_level` | u16 | observed 0..200, 3 distinct; top 0(x14), 85(x1), 200(x1) | medium |
| 0x88 | `member11_level` | u16 | observed 0..85, 2 distinct; top 0(x15), 85(x1) | medium |
| 0x8A | `member12_level` | u16 | observed 0..85, 2 distinct; top 0(x15), 85(x1) | medium |
| 0x8C | `member13_level` | u16 | observed 0..85, 2 distinct; top 0(x15), 85(x1) | medium |
| 0x8E | `member14_level` | u16 | observed 0..85, 2 distinct; top 0(x15), 85(x1) | medium |
| 0x90 | `member15_level` | u16 | always 0 | medium |
| 0x92 | `member1_x` | i32 | observed 0..4718592, 5 distinct; top 786432(x6), 0(x4), 3145728(x3), 4718592(x2), 1572864(x1) | low |
| 0x96 | `member1_h` | i32 | observed -3145728..0, 7 distinct; top -786432(x9), -1966080(x2), -2031616(x1), 0(x1), -2424832(x1),… | low |
| 0x9A | `member1_y` | i32 | observed 0..7929855, 8 distinct; top 65535(x5), 851967(x5), 5570559(x1), 4784127(x1), 0(x1), 7143423(x1) | low |
| 0x9E | `member2_x` | i32 | observed 0..3932160, 6 distinct; top 0(x4), 1572864(x3), 786432(x3), 3145728(x2), 2359296(x2), 3932160(x2) | low |
| 0xA2 | `member2_h` | i32 | observed -3932160..0, 9 distinct; top -786432(x7), 0(x2), -1703936(x1), -3932160(x1), -1572864(x1),… | low |
| 0xA6 | `member2_y` | i32 | observed 0..7929855, 8 distinct; top 1638399(x4), 3997695(x3), 65535(x2), 0(x2), 6356991(x2), 851967(x1) | low |
| 0xAA | `member3_x` | i32 | observed 0..5505024, 7 distinct; top 0(x5), 3932160(x3), 786432(x2), 1572864(x2), 5505024(x2), 2359296(x1) | low |
| 0xAE | `member3_h` | i32 | observed -2490368..0, 8 distinct; top -786432(x8), 0(x2), -1703936(x1), -1572864(x1), -1310720(x1),… | low |
| 0xB2 | `member3_y` | i32 | observed 0..7143423, 10 distinct; top 2424831(x3), 1638399(x2), 851967(x2), 0(x2), 6356991(x2), 3997695(x1) | low |
| 0xB6 | `member4_x` | i32 | observed 0..6291456, 7 distinct; top 0(x7), 3932160(x2), 786432(x2), 3145728(x2), 2359296(x1), 6291456(x1) | low |
| 0xBA | `member4_h` | i32 | observed -2424832..0, 8 distinct; top -786432(x8), 0(x2), -1703936(x1), -1572864(x1), -1310720(x1),… | low |
| 0xBE | `member4_y` | i32 | observed 0..5570559, 8 distinct; top 2424831(x3), 1638399(x3), 5570559(x2), 851967(x2), 0(x2), 4784127(x2) | low |
| 0xC2 | `member5_x` | i32 | observed 0..5505024, 7 distinct; top 0(x6), 4718592(x3), 1572864(x3), 3932160(x1), 3145728(x1), 786432(x1) | low |
| 0xC6 | `member5_h` | i32 | observed -2555904..0, 8 distinct; top -786432(x8), 0(x2), -1703936(x1), -1572864(x1), -1310720(x1),… | low |
| 0xCA | `member5_y` | i32 | observed 0..4784127, 8 distinct; top 4784127(x3), 3997695(x2), 851967(x2), 1638399(x2), 0(x2), 65535(x2) | low |
| 0xCE | `member6_x` | i32 | observed 0..4718592, 5 distinct; top 0(x6), 3145728(x4), 1572864(x3), 4718592(x2), 786432(x1) | low |
| 0xD2 | `member6_h` | i32 | observed -2490368..0, 8 distinct; top 0(x5), -786432(x5), -1703936(x1), -589824(x1), -1572864(x1),… | low |
| 0xD6 | `member6_y` | i32 | observed 0..6356991, 8 distinct; top 0(x5), 2424831(x3), 4784127(x2), 851967(x2), 6356991(x1), 3997695(x1) | low |
| 0xDA | `member7_x` | i32 | observed 0..4718592, 5 distinct; top 0(x9), 4718592(x3), 786432(x2), 1572864(x1), 2359296(x1) | low |
| 0xDE | `member7_h` | i32 | observed -2031616..0, 7 distinct; top 0(x7), -786432(x4), -1703936(x1), -589824(x1), -1572864(x1),… | low |
| 0xE2 | `member7_y` | i32 | observed 0..4784127, 7 distinct; top 0(x7), 65535(x3), 3997695(x2), 2424831(x1), 4784127(x1), 851967(x1) | low |
| 0xE6 | `member8_x` | i32 | observed 0..6291456, 6 distinct; top 0(x10), 4718592(x2), 1572864(x1), 2359296(x1), 786432(x1), 6291456(x1) | low |
| 0xEA | `member8_h` | i32 | observed -2031616..0, 7 distinct; top 0(x9), -786432(x2), -1703936(x1), -589824(x1), -1572864(x1),… | low |
| 0xEE | `member8_y` | i32 | observed 0..4784127, 6 distinct; top 0(x9), 3997695(x2), 65535(x2), 1638399(x1), 3211263(x1), 4784127(x1) | low |
| 0xF2 | `member9_x` | i32 | observed 0..7077888, 4 distinct; top 0(x13), 1572864(x1), 2359296(x1), 7077888(x1) | low |
| 0xF6 | `member9_h` | i32 | observed -2031616..0, 5 distinct; top 0(x12), -1703936(x1), -1572864(x1), -786432(x1), -2031616(x1) | low |
| 0xFA | `member9_y` | i32 | observed 0..7143423, 5 distinct; top 0(x12), 1638399(x1), 7143423(x1), 65535(x1), 3211263(x1) | low |
| 0xFE | `member10_x` | i32 | observed 0..2359296, 2 distinct; top 0(x15), 2359296(x1) | low |
| 0x102 | `member10_h` | i32 | observed -1572864..0, 2 distinct; top 0(x15), -1572864(x1) | low |
| 0x106 | `member10_y` | i32 | observed 0..5570559, 2 distinct; top 0(x15), 5570559(x1) | low |
| 0x10A | `member11_x` | i32 | observed 0..2359296, 2 distinct; top 0(x15), 2359296(x1) | low |
| 0x10E | `member11_h` | i32 | observed -1572864..0, 2 distinct; top 0(x15), -1572864(x1) | low |
| 0x112 | `member11_y` | i32 | observed 0..3997695, 2 distinct; top 0(x15), 3997695(x1) | low |
| 0x116 | `member12_x` | i32 | observed 0..1572864, 2 distinct; top 0(x15), 1572864(x1) | low |
| 0x11A | `member12_h` | i32 | observed -1572864..0, 2 distinct; top 0(x15), -1572864(x1) | low |
| 0x11E | `member12_y` | i32 | observed 0..6356991, 2 distinct; top 0(x15), 6356991(x1) | low |
| 0x122 | `member13_x` | i32 | observed 0..1572864, 2 distinct; top 0(x15), 1572864(x1) | low |
| 0x126 | `member13_h` | i32 | observed -1572864..0, 2 distinct; top 0(x15), -1572864(x1) | low |
| 0x12A | `member13_y` | i32 | observed 0..4784127, 2 distinct; top 0(x15), 4784127(x1) | low |
| 0x12E | `member14_x` | i32 | observed 0..1572864, 2 distinct; top 0(x15), 1572864(x1) | low |
| 0x132 | `member14_h` | i32 | observed -1572864..0, 2 distinct; top 0(x15), -1572864(x1) | low |
| 0x136 | `member14_y` | i32 | observed 0..3211263, 2 distinct; top 0(x15), 3211263(x1) | low |
| 0x13A | `member15_x` | i32 | always 0 | low |
| 0x13E | `member15_h` | i32 | always 0 | low |
| 0x142 | `member15_y` | i32 | always 0 | low |

## Quest.dat

Layout `fixed` · 5636 bytes per row · key `id` · 83 of 94 fields named

Quests (Quest shop quests ids 1..187 and resident request quests ids 2000..9502). Each row: title, 5 description lines, client name, prerequisites (up to 4 quest ids + up to 4 FlagList flags), one objective (cond_type + up to 3 targets/counts + option), one reward (reward_type/value/count), optional stage unlock (dungeon.stage_key) and optional extra enemies (QuestEnemy group). Recruiting: reward_type 5 = unique character joins (value=char.dat id: 250 Logan, 280 Erynder Z, 300 Drumdawn, 310 Brave), reward_type 6 = unlocks a class for creation (value = class base char id, e.g. 1090 Thief, 1230 Asagi, 2020 Imp).

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | quest id (1..187 quest-shop quests, 2000..9502 resident requests); unique | high |
| 0x4 | `name (×5 languages)` | str(52) | quest title | high |
| 0x108 | `quest_kind` | u8 | 0 = Quest-shop quest (ids 1..187), 1 = request quest (ids 2000..9502) | medium |
| 0x109 | `cond_type` | u8 | objective type; verified by titles/targets | high |
| 0x10A | `group_major` | u8 | request quests: 1..7 = thousands digit-1 of id (9xxx use 0); 0 for shop quests | medium |
| 0x10B | `group_minor` | u8 | request quests: 1..8 sub-group (roughly hundreds digit+1 / requester group); 0 for shop quests | medium |
| 0x10C | `prereq_quest1` | u32 | → `Quest.dat` quest id that must be completed first (0 = none); e.g. Ninja rep needs quest 5, Pirate needs 6+7 | high |
| 0x110 | `prereq_quest2` | u32 | → `Quest.dat` quest id that must be completed first (0 = none); e.g. Ninja rep needs quest 5, Pirate needs 6+7 | high |
| 0x114 | `prereq_quest3` | u32 | → `Quest.dat` quest id that must be completed first (0 = none); e.g. Ninja rep needs quest 5, Pirate needs 6+7 | high |
| 0x118 | `prereq_quest4` | u32 | → `Quest.dat` quest id that must be completed first (0 = none); e.g. Ninja rep needs quest 5, Pirate needs 6+7 | medium |
| 0x11C | `prereq_quest_count` | u32 | number of used prereq_quest slots (0..3) | high |
| 0x120 | `req_flag1` | u32 | → `flag:FlagListDataBase.dat` FlagListDataBase id that must be set (e.g. 299 FLAG_SCENARIO_02_CLEAR, 1699 post-game, 14018… | high |
| 0x124 | `req_flag2` | u32 | → `flag:FlagListDataBase.dat` FlagListDataBase id that must be set (e.g. 299 FLAG_SCENARIO_02_CLEAR, 1699 post-game, 14018… | high |
| 0x128 | `req_flag3` | u32 | → `flag:FlagListDataBase.dat` FlagListDataBase id that must be set (e.g. 299 FLAG_SCENARIO_02_CLEAR, 1699 post-game, 14018… | medium |
| 0x12C | `req_flag4` | u32 | → `flag:FlagListDataBase.dat` FlagListDataBase id that must be set (e.g. 299 FLAG_SCENARIO_02_CLEAR, 1699 post-game, 14018… | medium |
| 0x130 | `req_flag_count` | u32 | number of used req_flag slots (0..2) | high |
| 0x134 | `req_extra` | u32 | 0 normally; 100 for the six story "Trial" challenge quests (157,158,164,166,169,170); 16009… | low |
| 0x13A | `important` | u16 | 1 for quests 2..50 and 187 (overlord recruit, class unlock, key quests), else 0; probably… | medium |
| 0x13C | `sort_order` | u16 | progress slot: the save keeps each quest's completed/accepted/seen state at this index, and lists are… | high |
| 0x13E | `desc1 (×5 languages)` | str(148) | quest description line 1 | high |
| 0x422 | `desc2 (×5 languages)` | str(148) | quest description line 2 | high |
| 0x706 | `desc3 (×5 languages)` | str(148) | quest description line 3 | high |
| 0x9EA | `desc4 (×5 languages)` | str(148) | quest description line 4 | high |
| 0xCCE | `desc5 (×5 languages)` | str(148) | quest description line 5 | high |
| 0xFB2 | `client (×5 languages)` | str(148) | requester name/title, e.g. "Thief Rep, Colette" (JP right-aligned with U+3000) | high |
| 0x1298 | `cond_target1` | i32 | objective target; meaning depends on cond_type (class id / char id / item id / map id / stat index /… | high |
| 0x129C | `cond_target2` | i32 | objective target; meaning depends on cond_type (class id / char id / item id / map id / stat index /… | high |
| 0x12A0 | `cond_target3` | i32 | objective target; meaning depends on cond_type (class id / char id / item id / map id / stat index /… | high |
| 0x12A4 | `cond_count1` | u32 | required amount/level for cond_target1 | high |
| 0x12A8 | `cond_count2` | u32 | required amount/level for cond_target2 | high |
| 0x12AC | `cond_count3` | u32 | required amount/level for cond_target3 | high |
| 0x12B0 | `cond_option` | u32 | extra condition kind: 0 none, 1 min enemy level (cond 0), 3 rarity?, 4 item level, 5 item category (label… | medium |
| 0x12B4 | `cond_option_value` | u32 | parameter of cond_option (e.g. min level 10..1000, item level 10..100) | medium |
| 0x12C0 | `cond_label1 (×5 languages)` | str(52) | objective item-category label e.g. "Attack Item", "Fist" | high |
| 0x13C4 | `cond_label2 (×5 languages)` | str(52) | unused label slot for target 2 (always empty) | medium |
| 0x14C8 | `cond_label3 (×5 languages)` | str(52) | unused label slot for target 3 (always empty) | medium |
| 0x15CC | `reward_type` | u32 | reward kind (verified by reward values) | high |
| 0x15D0 | `reward_value` | u32 | depends on reward_type: HL amount / item.dat id / Skill.dat id / magic.dat id / char.dat id / class id /… | high |
| 0x15D4 | `reward_count` | u32 | quantity (1..3) | high |
| 0x15D8 | `unlock_stage` | u32 | → `dungeon.dat:stage_key` dungeon.dat stage_key made available by the quest (e.g. 5018 "Overlord of Revenge" for quest 2); 0 none | high |
| 0x15DC | `unlock_stage2` | u32 | → `dungeon.dat:stage_key` second stage_key (equal to unlock_stage for Challenge/Carnage stages, 0 for Overlord fights and "Trial"… | medium |
| 0x15FC | `quest_enemy_group` | u32 | → `QuestEnemy.dat:group` QuestEnemy.dat group whose units are added to the cond_target map (verified: 15 quests, group maps ==… | high |
| 0x1600 | `difficulty` | u32 | 1..10, grows with quest difficulty (overlord fights 2,3,5,7; class unlocks 2..8); probably star rank shown… | medium |

## QuestEnemy.dat

Layout `fixed` · 1208 bytes per row · key `id` · 136 of 260 fields named

Extra enemy units injected into an existing map by a quest (Quest.quest_enemy_group -> group). Bytes 616..1207 are one 592-byte map-unit record, the same structure used in Map/mapNNNNN/mapNNNNN.chr (u16 count @0 + 16-byte header, then N*592): char_id, level, direction, position, equipment, 20 evilities, 20 skills (+levels). Adding a custom boss to any stage without editing map files: new QuestEnemy rows (group G, map M) + Quest row with cond_type 4, cond_target1 M, quest_enemy_group G.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | row id 1..107 | high |
| 0x4 | `group` | u32 | quest enemy group; Quest.quest_enemy_group selects all rows of a group (15 groups: 1,5..18) | high |
| 0x8 | `map_id` | u32 | → `map` map the units are added to (= dungeon.map_id; equals the Quest cond_target1 of the owning quest) | high |
| 0xC | `name (×5 languages)` | str(52) | unit display name (e.g. "Gallatin"); empty = use class name | high |
| 0x110 | `title (×5 languages)` | str(68) | unit title/epithet (e.g. "Evil God Disciple") | high |
| 0x268 | `unit_index` | u32 | placement index (== id here); first field of the 592-byte map-unit record also used in Map/mapNNNNN.chr… | high |
| 0x26C | `char_id` | u32 | → `char.dat` char.dat id (class+rank, e.g. 1155 Armor Knight rank 5, 1031 Magician, 5202, or unique ids) | high |
| 0x270 | `level` | u16 | unit level (3..3300 here) | high |
| 0x272 | `direction` | u16 | facing in degrees: 0/90/180/270 | high |
| 0x274 | `pos_x` | u16 | map X coordinate | high |
| 0x276 | `pos_y` | u16 | map Y coordinate | high |
| 0x280 | `weapon` | u32 | → `item.dat` equipped weapon (item.dat id, e.g. 239 Yoshitsuna); 0 = none | high |
| 0x284 | `weapon2` | u32 | → `item.dat` second weapon slot (item.dat id; rarely used, weapons only) | medium |
| 0x288 | `equip1` | u32 | → `item.dat` armour/accessory slot 1 (item.dat id; 12xx armour, 13xx belts, 14xx shoes, 15xx orbs, 16xx glasses, 17xx… | high |
| 0x28C | `equip2` | u32 | → `item.dat` armour/accessory slot 2 (item.dat id; 12xx armour, 13xx belts, 14xx shoes, 15xx orbs, 16xx glasses, 17xx… | high |
| 0x290 | `equip3` | u32 | → `item.dat` armour/accessory slot 3 (item.dat id; 12xx armour, 13xx belts, 14xx shoes, 15xx orbs, 16xx glasses, 17xx… | high |
| 0x294 | `equip4` | u32 | → `item.dat` armour/accessory slot 4 (item.dat id; 12xx armour, 13xx belts, 14xx shoes, 15xx orbs, 16xx glasses, 17xx… | medium |
| 0x2A0 | `evility1` | u32 | → `Skill.dat` evility (Skill.dat id, e.g. 4008 Fire Body, 5001 Theft Prevention); 0 = empty | high |
| 0x2A4 | `evility2` | u32 | → `Skill.dat` evility (Skill.dat id, e.g. 4008 Fire Body, 5001 Theft Prevention); 0 = empty | high |
| 0x2A8 | `evility3` | u32 | → `Skill.dat` evility (Skill.dat id, e.g. 4008 Fire Body, 5001 Theft Prevention); 0 = empty | high |
| 0x2AC | `evility4` | u32 | → `Skill.dat` evility (Skill.dat id, e.g. 4008 Fire Body, 5001 Theft Prevention); 0 = empty | high |
| 0x2B0 | `evility5` | u32 | → `Skill.dat` evility (Skill.dat id, e.g. 4008 Fire Body, 5001 Theft Prevention); 0 = empty | high |
| 0x2B4 | `evility6` | u32 | → `Skill.dat` evility (Skill.dat id, e.g. 4008 Fire Body, 5001 Theft Prevention); 0 = empty | medium |
| 0x2B8 | `evility7` | u32 | → `Skill.dat` evility (Skill.dat id, e.g. 4008 Fire Body, 5001 Theft Prevention); 0 = empty | medium |
| 0x2BC | `evility8` | u32 | → `Skill.dat` evility (Skill.dat id, e.g. 4008 Fire Body, 5001 Theft Prevention); 0 = empty | medium |
| 0x2C0 | `evility9` | u32 | → `Skill.dat` evility (Skill.dat id, e.g. 4008 Fire Body, 5001 Theft Prevention); 0 = empty | medium |
| 0x2C4 | `evility10` | u32 | → `Skill.dat` evility (Skill.dat id, e.g. 4008 Fire Body, 5001 Theft Prevention); 0 = empty | medium |
| 0x2C8 | `evility11` | u32 | → `Skill.dat` evility (Skill.dat id, e.g. 4008 Fire Body, 5001 Theft Prevention); 0 = empty | medium |
| 0x2CC | `evility12` | u32 | → `Skill.dat` evility (Skill.dat id, e.g. 4008 Fire Body, 5001 Theft Prevention); 0 = empty | medium |
| 0x2D0 | `evility13` | u32 | → `Skill.dat` evility (Skill.dat id, e.g. 4008 Fire Body, 5001 Theft Prevention); 0 = empty | medium |
| 0x2D4 | `evility14` | u32 | → `Skill.dat` evility (Skill.dat id, e.g. 4008 Fire Body, 5001 Theft Prevention); 0 = empty | medium |
| 0x2D8 | `evility15` | u32 | → `Skill.dat` evility (Skill.dat id, e.g. 4008 Fire Body, 5001 Theft Prevention); 0 = empty | medium |
| 0x2DC | `evility16` | u32 | → `Skill.dat` evility (Skill.dat id, e.g. 4008 Fire Body, 5001 Theft Prevention); 0 = empty | medium |
| 0x2E0 | `evility17` | u32 | → `Skill.dat` evility (Skill.dat id, e.g. 4008 Fire Body, 5001 Theft Prevention); 0 = empty | medium |
| 0x2E4 | `evility18` | u32 | → `Skill.dat` evility (Skill.dat id, e.g. 4008 Fire Body, 5001 Theft Prevention); 0 = empty | medium |
| 0x2E8 | `evility19` | u32 | → `Skill.dat` evility (Skill.dat id, e.g. 4008 Fire Body, 5001 Theft Prevention); 0 = empty | medium |
| 0x2EC | `evility20` | u32 | → `Skill.dat` evility (Skill.dat id, e.g. 4008 Fire Body, 5001 Theft Prevention); 0 = empty | medium |
| 0x2F0 | `skill1` | u32 | → `magic.dat` learned skill/spell (magic.dat id, e.g. 1001 Fire, 1004 Tera Fire); 0 = empty | high |
| 0x2F4 | `skill2` | u32 | → `magic.dat` learned skill/spell (magic.dat id, e.g. 1001 Fire, 1004 Tera Fire); 0 = empty | high |
| 0x2F8 | `skill3` | u32 | → `magic.dat` learned skill/spell (magic.dat id, e.g. 1001 Fire, 1004 Tera Fire); 0 = empty | high |
| 0x2FC | `skill4` | u32 | → `magic.dat` learned skill/spell (magic.dat id, e.g. 1001 Fire, 1004 Tera Fire); 0 = empty | high |
| 0x300 | `skill5` | u32 | → `magic.dat` learned skill/spell (magic.dat id, e.g. 1001 Fire, 1004 Tera Fire); 0 = empty | high |
| 0x304 | `skill6` | u32 | → `magic.dat` learned skill/spell (magic.dat id, e.g. 1001 Fire, 1004 Tera Fire); 0 = empty | medium |
| 0x308 | `skill7` | u32 | → `magic.dat` learned skill/spell (magic.dat id, e.g. 1001 Fire, 1004 Tera Fire); 0 = empty | medium |
| 0x30C | `skill8` | u32 | → `magic.dat` learned skill/spell (magic.dat id, e.g. 1001 Fire, 1004 Tera Fire); 0 = empty | medium |
| 0x310 | `skill9` | u32 | → `magic.dat` learned skill/spell (magic.dat id, e.g. 1001 Fire, 1004 Tera Fire); 0 = empty | medium |
| 0x314 | `skill10` | u32 | → `magic.dat` learned skill/spell (magic.dat id, e.g. 1001 Fire, 1004 Tera Fire); 0 = empty | medium |
| 0x318 | `skill11` | u32 | → `magic.dat` learned skill/spell (magic.dat id, e.g. 1001 Fire, 1004 Tera Fire); 0 = empty | medium |
| 0x31C | `skill12` | u32 | → `magic.dat` learned skill/spell (magic.dat id, e.g. 1001 Fire, 1004 Tera Fire); 0 = empty | medium |
| 0x320 | `skill13` | u32 | → `magic.dat` learned skill/spell (magic.dat id, e.g. 1001 Fire, 1004 Tera Fire); 0 = empty | medium |
| 0x324 | `skill14` | u32 | → `magic.dat` learned skill/spell (magic.dat id, e.g. 1001 Fire, 1004 Tera Fire); 0 = empty | medium |
| 0x328 | `skill15` | u32 | → `magic.dat` learned skill/spell (magic.dat id, e.g. 1001 Fire, 1004 Tera Fire); 0 = empty | medium |
| 0x32C | `skill16` | u32 | → `magic.dat` learned skill/spell (magic.dat id, e.g. 1001 Fire, 1004 Tera Fire); 0 = empty | medium |
| 0x330 | `skill17` | u32 | → `magic.dat` learned skill/spell (magic.dat id, e.g. 1001 Fire, 1004 Tera Fire); 0 = empty | medium |
| 0x334 | `skill18` | u32 | → `magic.dat` learned skill/spell (magic.dat id, e.g. 1001 Fire, 1004 Tera Fire); 0 = empty | medium |
| 0x338 | `skill19` | u32 | → `magic.dat` learned skill/spell (magic.dat id, e.g. 1001 Fire, 1004 Tera Fire); 0 = empty | medium |
| 0x33C | `skill20` | u32 | → `magic.dat` learned skill/spell (magic.dat id, e.g. 1001 Fire, 1004 Tera Fire); 0 = empty | medium |
| 0x340 | `skill1_level` | u16 | level of skill1 (1..99) | high |
| 0x342 | `skill2_level` | u16 | level of skill2 (1..99) | high |
| 0x344 | `skill3_level` | u16 | level of skill3 (1..99) | high |
| 0x346 | `skill4_level` | u16 | level of skill4 (1..99) | high |
| 0x348 | `skill5_level` | u16 | level of skill5 (1..99) | high |
| 0x34A | `skill6_level` | u16 | level of skill6 (1..99) | medium |
| 0x34C | `skill7_level` | u16 | level of skill7 (1..99) | medium |
| 0x34E | `skill8_level` | u16 | level of skill8 (1..99) | medium |
| 0x350 | `skill9_level` | u16 | level of skill9 (1..99) | medium |
| 0x352 | `skill10_level` | u16 | level of skill10 (1..99) | medium |
| 0x354 | `skill11_level` | u16 | level of skill11 (1..99) | medium |
| 0x356 | `skill12_level` | u16 | level of skill12 (1..99) | medium |
| 0x358 | `skill13_level` | u16 | level of skill13 (1..99) | medium |
| 0x35A | `skill14_level` | u16 | level of skill14 (1..99) | medium |
| 0x35C | `skill15_level` | u16 | level of skill15 (1..99) | medium |
| 0x35E | `skill16_level` | u16 | level of skill16 (1..99) | medium |
| 0x360 | `skill17_level` | u16 | level of skill17 (1..99) | medium |
| 0x362 | `skill18_level` | u16 | level of skill18 (1..99) | medium |
| 0x364 | `skill19_level` | u16 | level of skill19 (1..99) | medium |
| 0x366 | `skill20_level` | u16 | level of skill20 (1..99) | medium |
| 0x368 | `skill1_param` | u16 | 0..9 per skill (range/area upgrade?) | low |
| 0x36A | `skill2_param` | u16 | 0..9 per skill (range/area upgrade?) | low |
| 0x36C | `skill3_param` | u16 | 0..9 per skill (range/area upgrade?) | low |
| 0x36E | `skill4_param` | u16 | 0..9 per skill (range/area upgrade?) | low |
| 0x370 | `skill5_param` | u16 | 0..9 per skill (range/area upgrade?) | low |
| 0x372 | `skill6_param` | u16 | 0..9 per skill (range/area upgrade?) | low |
| 0x374 | `skill7_param` | u16 | 0..9 per skill (range/area upgrade?) | low |
| 0x376 | `skill8_param` | u16 | 0..9 per skill (range/area upgrade?) | low |
| 0x378 | `skill9_param` | u16 | 0..9 per skill (range/area upgrade?) | low |
| 0x37A | `skill10_param` | u16 | 0..9 per skill (range/area upgrade?) | low |
| 0x37C | `skill11_param` | u16 | 0..9 per skill (range/area upgrade?) | low |
| 0x37E | `skill12_param` | u16 | 0..9 per skill (range/area upgrade?) | low |
| 0x380 | `skill13_param` | u16 | 0..9 per skill (range/area upgrade?) | low |
| 0x382 | `skill14_param` | u16 | 0..9 per skill (range/area upgrade?) | low |
| 0x384 | `skill15_param` | u16 | 0..9 per skill (range/area upgrade?) | low |
| 0x386 | `skill16_param` | u16 | 0..9 per skill (range/area upgrade?) | low |
| 0x388 | `skill17_param` | u16 | 0..9 per skill (range/area upgrade?) | low |
| 0x38A | `skill18_param` | u16 | 0..9 per skill (range/area upgrade?) | low |
| 0x38C | `skill19_param` | u16 | 0..9 per skill (range/area upgrade?) | low |
| 0x38E | `skill20_param` | u16 | 0..9 per skill (range/area upgrade?) | low |
| 0x390 | `skill1_param2` | u16 | 0..9 or 99 per skill | low |
| 0x392 | `skill2_param2` | u16 | 0..9 or 99 per skill | low |
| 0x394 | `skill3_param2` | u16 | 0..9 or 99 per skill | low |
| 0x396 | `skill4_param2` | u16 | 0..9 or 99 per skill | low |
| 0x398 | `skill5_param2` | u16 | 0..9 or 99 per skill | low |
| 0x39A | `skill6_param2` | u16 | 0..9 or 99 per skill | low |
| 0x39C | `skill7_param2` | u16 | 0..9 or 99 per skill | low |
| 0x39E | `skill8_param2` | u16 | 0..9 or 99 per skill | low |
| 0x3A0 | `skill9_param2` | u16 | 0..9 or 99 per skill | low |
| 0x3A2 | `skill10_param2` | u16 | 0..9 or 99 per skill | low |
| 0x3A4 | `skill11_param2` | u16 | 0..9 or 99 per skill | low |
| 0x3A6 | `skill12_param2` | u16 | 0..9 or 99 per skill | low |
| 0x3A8 | `skill13_param2` | u16 | 0..9 or 99 per skill | low |
| 0x3AA | `skill14_param2` | u16 | 0..9 or 99 per skill | low |
| 0x3AC | `skill15_param2` | u16 | 0..9 or 99 per skill | low |
| 0x3AE | `skill16_param2` | u16 | 0..9 or 99 per skill | low |
| 0x3B0 | `skill17_param2` | u16 | 0..9 or 99 per skill | low |
| 0x3B2 | `skill18_param2` | u16 | 0..9 or 99 per skill | low |
| 0x3B4 | `skill19_param2` | u16 | 0..9 or 99 per skill | low |
| 0x3B6 | `skill20_param2` | u16 | 0..9 or 99 per skill | low |
| 0x3B8 | `stat_mod_HP` | u16 | per-stat value, usually 0 or 1 (1 on 26% of .chr units, same for all 8), sometimes 7..86 | low |
| 0x3BA | `stat_mod_SP` | u16 | per-stat value, usually 0 or 1 (1 on 26% of .chr units, same for all 8), sometimes 7..86 | low |
| 0x3BC | `stat_mod_ATK` | u16 | per-stat value, usually 0 or 1 (1 on 26% of .chr units, same for all 8), sometimes 7..86 | low |
| 0x3BE | `stat_mod_DEF` | u16 | per-stat value, usually 0 or 1 (1 on 26% of .chr units, same for all 8), sometimes 7..86 | low |
| 0x3C0 | `stat_mod_INT` | u16 | per-stat value, usually 0 or 1 (1 on 26% of .chr units, same for all 8), sometimes 7..86 | low |
| 0x3C2 | `stat_mod_RES` | u16 | per-stat value, usually 0 or 1 (1 on 26% of .chr units, same for all 8), sometimes 7..86 | low |
| 0x3C4 | `stat_mod_HIT` | u16 | per-stat value, usually 0 or 1 (1 on 26% of .chr units, same for all 8), sometimes 7..86 | low |
| 0x3C6 | `stat_mod_SPD` | u16 | per-stat value, usually 0 or 1 (1 on 26% of .chr units, same for all 8), sometimes 7..86 | low |
| 0x3E0 | `ai_id` | i32 | -1 or 101..113 (104 most common); probably AI/behaviour pattern | low |
| 0x3E6 | `target_x` | u16 | 0..23; second coordinate pair (AI move target?) | low |
| 0x3E8 | `target_y` | u16 | 0..22 | low |

## RDFloorData.dat

Layout `fixed` · 24 bytes per row · 0 of 12 fields named

*No fields named yet.*

## RMDUNGEON.dat

Layout `fixed` · 20 bytes per row · 0 of 15 fields named

*No fields named yet.*

## room.dat

Layout `fixed` · 96 bytes per row · 0 of 33 fields named

*No fields named yet.*

## saveicon.dat

Layout `fixed` · 200 bytes per row · key `id` · 10 of 12 fields named

Selectable save-file icons (character portraits). The row order also matches the character index list used by SkitStartCharSetting (0 Killia, 1 Seraphina, 2 Red Magnus, 3 Christo, 4 Usalia, 5 Zeroken, 6 Goldion, ...).

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `unlock_type` | i32 | -1 = always available (Killia, Seraphina, Prinny); 10 = needs unlock (character obtained?) | low |
| 0x4 | `unlock_param` | i32 | -1 with unlock_type -1, else 0 | low |
| 0x8 | `id` | u32 | save icon id 1..41 | high |
| 0xC | `icon_index` | u16 | icon index 0..40 (= id-1) in the save icon texture (saveicon.txf) | medium |
| 0xE | `name (×5 languages)` | str(36) | character name shown for the icon | high |
| 0xC4 | `dlc_group` | u32 | content group 100 base, 110 D1 chars, 120 D4, 130 D3/others, 140 D2, 150 other titles (same codes as… | medium |

## ScreenPosDataBase.dat

Layout `fixed` · 116 bytes per row · key `id` · 27 of 29 fields named

UI element screen positions per language (x,y,z,w,h); 6 rows differ between languages. PC exe picks the slot by game language (0=JP,1=EN,2=FR); language ids 3/4 are remapped to slot 1, so CHN/KOR slots appear unused on PC.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | layout id: 1-3, 1000001-1000171, 1500001-1500053, 2000001-2000038, 3000001-3000016 (looked up by constant,… | high |
| 0x4 | `x (×5 languages)` | i32 | JP: screen x (scaled by the resolution factor in 0x14013b3f0) | high |
| 0x8 | `y (×5 languages)` | i32 | JP: screen y (scaled) | high |
| 0xC | `z (×5 languages)` | i32 | JP: depth/priority (NOT scaled; -250..4000) | medium |
| 0x10 | `w (×5 languages)` | i32 | JP: width (scaled; 0/-1 = unused) | medium |
| 0x14 | `h (×5 languages)` | i32 | JP: height (scaled; 0/-1 = unused) | medium |
| 0x68 | `color` | u32 | 0 or 0x80808080 (RGBA modulate 128 = neutral); one row has decimal 80808080 | low |

## senator.dat

Layout `fixed` · 304 bytes per row · key `senator_id` · 9 of 25 fields named

Dark Assembly senators: 258 rows = 43 parties x 6 senators (levels 5..220). Each links to a char.dat id (its sprite/class). Bills are in WishDataBase.dat; senator2.dat is an affinity matrix.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `senator_id` | u16 | senator id; equals char_id in every row (1010..1225, 2010..2215) | medium |
| 0x2 | `level` | u16 | senator level: 5,30,75,120,180,220 for the 6 senators of each party | high |
| 0x4 | `char_id` | u32 | → `char.dat` appearance/class of the senator: char.dat id (u32 at char.dat record +0xD0; e.g. 10=Killia,… | high |
| 0xA | `name (×5 languages)` | str(52) |  | high |
| 0x10E | `party` | u8 | party (faction) id 1..43 (Warrior=1 ... Horseman=39, 40..43 extra); WishDataBase bills 200+party ("Train… | high |

## senator2.dat

Layout `fixed` · 132 bytes per row · key `id` · 129 of 129 fields named

Relationship/affinity matrix, 107 rows x 128 i8 columns. Rows 1..80 form 8 groups of 10: members of a group are +50 to each other and -25 to the paired group (1-10 vs 11-20, 21-30 vs 31-40, ...), 0 to self and everyone else; columns 81..128 and rows 81..107 are 0. What the ids are (senator groups? personalities?) is unverified.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | row id 1..107; rows 1..80 used, 81..107 all zero | high |
| 0x4 | `rel_001` | i8 | relation to id 1: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x5 | `rel_002` | i8 | relation to id 2: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x6 | `rel_003` | i8 | relation to id 3: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x7 | `rel_004` | i8 | relation to id 4: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x8 | `rel_005` | i8 | relation to id 5: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x9 | `rel_006` | i8 | relation to id 6: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0xA | `rel_007` | i8 | relation to id 7: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0xB | `rel_008` | i8 | relation to id 8: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0xC | `rel_009` | i8 | relation to id 9: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0xD | `rel_010` | i8 | relation to id 10: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0xE | `rel_011` | i8 | relation to id 11: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0xF | `rel_012` | i8 | relation to id 12: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x10 | `rel_013` | i8 | relation to id 13: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x11 | `rel_014` | i8 | relation to id 14: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x12 | `rel_015` | i8 | relation to id 15: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x13 | `rel_016` | i8 | relation to id 16: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x14 | `rel_017` | i8 | relation to id 17: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x15 | `rel_018` | i8 | relation to id 18: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x16 | `rel_019` | i8 | relation to id 19: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x17 | `rel_020` | i8 | relation to id 20: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x18 | `rel_021` | i8 | relation to id 21: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x19 | `rel_022` | i8 | relation to id 22: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x1A | `rel_023` | i8 | relation to id 23: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x1B | `rel_024` | i8 | relation to id 24: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x1C | `rel_025` | i8 | relation to id 25: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x1D | `rel_026` | i8 | relation to id 26: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x1E | `rel_027` | i8 | relation to id 27: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x1F | `rel_028` | i8 | relation to id 28: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x20 | `rel_029` | i8 | relation to id 29: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x21 | `rel_030` | i8 | relation to id 30: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x22 | `rel_031` | i8 | relation to id 31: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x23 | `rel_032` | i8 | relation to id 32: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x24 | `rel_033` | i8 | relation to id 33: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x25 | `rel_034` | i8 | relation to id 34: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x26 | `rel_035` | i8 | relation to id 35: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x27 | `rel_036` | i8 | relation to id 36: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x28 | `rel_037` | i8 | relation to id 37: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x29 | `rel_038` | i8 | relation to id 38: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x2A | `rel_039` | i8 | relation to id 39: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x2B | `rel_040` | i8 | relation to id 40: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x2C | `rel_041` | i8 | relation to id 41: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x2D | `rel_042` | i8 | relation to id 42: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x2E | `rel_043` | i8 | relation to id 43: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x2F | `rel_044` | i8 | relation to id 44: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x30 | `rel_045` | i8 | relation to id 45: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x31 | `rel_046` | i8 | relation to id 46: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x32 | `rel_047` | i8 | relation to id 47: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x33 | `rel_048` | i8 | relation to id 48: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x34 | `rel_049` | i8 | relation to id 49: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x35 | `rel_050` | i8 | relation to id 50: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x36 | `rel_051` | i8 | relation to id 51: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x37 | `rel_052` | i8 | relation to id 52: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x38 | `rel_053` | i8 | relation to id 53: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x39 | `rel_054` | i8 | relation to id 54: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x3A | `rel_055` | i8 | relation to id 55: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x3B | `rel_056` | i8 | relation to id 56: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x3C | `rel_057` | i8 | relation to id 57: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x3D | `rel_058` | i8 | relation to id 58: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x3E | `rel_059` | i8 | relation to id 59: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x3F | `rel_060` | i8 | relation to id 60: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x40 | `rel_061` | i8 | relation to id 61: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x41 | `rel_062` | i8 | relation to id 62: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x42 | `rel_063` | i8 | relation to id 63: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x43 | `rel_064` | i8 | relation to id 64: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x44 | `rel_065` | i8 | relation to id 65: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x45 | `rel_066` | i8 | relation to id 66: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x46 | `rel_067` | i8 | relation to id 67: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x47 | `rel_068` | i8 | relation to id 68: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x48 | `rel_069` | i8 | relation to id 69: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x49 | `rel_070` | i8 | relation to id 70: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x4A | `rel_071` | i8 | relation to id 71: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x4B | `rel_072` | i8 | relation to id 72: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x4C | `rel_073` | i8 | relation to id 73: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x4D | `rel_074` | i8 | relation to id 74: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x4E | `rel_075` | i8 | relation to id 75: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x4F | `rel_076` | i8 | relation to id 76: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x50 | `rel_077` | i8 | relation to id 77: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x51 | `rel_078` | i8 | relation to id 78: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x52 | `rel_079` | i8 | relation to id 79: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x53 | `rel_080` | i8 | relation to id 80: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | medium |
| 0x54 | `rel_081` | i8 | relation to id 81: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x55 | `rel_082` | i8 | relation to id 82: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x56 | `rel_083` | i8 | relation to id 83: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x57 | `rel_084` | i8 | relation to id 84: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x58 | `rel_085` | i8 | relation to id 85: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x59 | `rel_086` | i8 | relation to id 86: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x5A | `rel_087` | i8 | relation to id 87: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x5B | `rel_088` | i8 | relation to id 88: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x5C | `rel_089` | i8 | relation to id 89: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x5D | `rel_090` | i8 | relation to id 90: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x5E | `rel_091` | i8 | relation to id 91: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x5F | `rel_092` | i8 | relation to id 92: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x60 | `rel_093` | i8 | relation to id 93: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x61 | `rel_094` | i8 | relation to id 94: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x62 | `rel_095` | i8 | relation to id 95: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x63 | `rel_096` | i8 | relation to id 96: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x64 | `rel_097` | i8 | relation to id 97: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x65 | `rel_098` | i8 | relation to id 98: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x66 | `rel_099` | i8 | relation to id 99: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x67 | `rel_100` | i8 | relation to id 100: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x68 | `rel_101` | i8 | relation to id 101: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x69 | `rel_102` | i8 | relation to id 102: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x6A | `rel_103` | i8 | relation to id 103: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x6B | `rel_104` | i8 | relation to id 104: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x6C | `rel_105` | i8 | relation to id 105: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x6D | `rel_106` | i8 | relation to id 106: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x6E | `rel_107` | i8 | relation to id 107: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x6F | `rel_108` | i8 | relation to id 108: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x70 | `rel_109` | i8 | relation to id 109: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x71 | `rel_110` | i8 | relation to id 110: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x72 | `rel_111` | i8 | relation to id 111: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x73 | `rel_112` | i8 | relation to id 112: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x74 | `rel_113` | i8 | relation to id 113: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x75 | `rel_114` | i8 | relation to id 114: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x76 | `rel_115` | i8 | relation to id 115: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x77 | `rel_116` | i8 | relation to id 116: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x78 | `rel_117` | i8 | relation to id 117: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x79 | `rel_118` | i8 | relation to id 118: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x7A | `rel_119` | i8 | relation to id 119: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x7B | `rel_120` | i8 | relation to id 120: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x7C | `rel_121` | i8 | relation to id 121: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x7D | `rel_122` | i8 | relation to id 122: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x7E | `rel_123` | i8 | relation to id 123: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x7F | `rel_124` | i8 | relation to id 124: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x80 | `rel_125` | i8 | relation to id 125: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x81 | `rel_126` | i8 | relation to id 126: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x82 | `rel_127` | i8 | relation to id 127: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |
| 0x83 | `rel_128` | i8 | relation to id 128: +50 friendly (same group of 10), -25 hostile (paired rival group), 0 self/none | low |

## ship.dat

Layout `fixed` · 396 bytes per row · key `id` · 7 of 21 fields named

Item World pirate ships (11 rows, JP-only). Referenced by pirate.dat ship_id/ship_obj. Trailing 8 bytes are small ship parameters (unknown).

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | ship id 1..11 | high |
| 0x4 | `ship_obj` | u16 | ship object id 4261..4271 (= 4260 + id) | high |
| 0x7 | `icon` | u8 | 100..110 (= 99 + id): icon/graphic id | low |
| 0xC | `name (×5 languages)` | str(49) | ship name (JP only) | high |
| 0x3D | `desc1 (×5 languages)` | str(108) | description line 1 (JP) | high |
| 0xA9 | `desc2 (×5 languages)` | str(108) | description line 2 (JP) | high |
| 0x115 | `desc3 (×5 languages)` | str(108) | description line 3 (JP) | high |

## Skill.dat

Layout `pool` · 96 bytes per row · key `id` · 24 of 24 fields named

Evilities (796 x 96, pool layout: 10 strptr fields into the string pool after the records). Record: id, names, slot cost, descriptions (%d = value1/value2), kind (unique/normal), effect group, values, source, display category, Mana cost, owner char/class, class learn order, version group. A class owns 6 evilities (owner = class id, class_order 1..6, 6 = unique); characters own theirs via owner too; the innate evility of a char.dat row is at char.dat +0xB8.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | evility id (key). 10-410 character evilities (grouped per character, e.g. 10-16 Killia), 1010-1242 generic… | high |
| 0x4 | `name (×5 languages)` | strptr | evility name (string pool) | high |
| 0x18 | `slot_cost` | u32 | Evility slot cost when equipped (0 = unique/innate, else 1..6) | high |
| 0x1C | `desc (×5 languages)` | strptr | description; %d placeholders are filled with value1/value2 | high |
| 0x30 | `kind` | u32 | uniqueness | high |
| 0x34 | `effect_group` | u32 | internal effect family 0..44 (9 plain stat % boosts, 11 conditional stat boosts, 2 damage-dealt, 3… | medium |
| 0x38 | `value1` | u32 | first %d of the description (Muscle Increase 10 = +10% ATK, Status Guard 90) | high |
| 0x3C | `value2` | u32 | second %d of the description (Violence 50% EXP, Magic Bundle 50% more SP) | high |
| 0x40 | `source` | u32 | where the evility comes from | medium |
| 0x44 | `category` | u32 | display category (evility list filter/icon) | medium |
| 0x48 | `subcategory` | u32 | secondary grouping 0..18 (1 INT/ATK growth & counters, 2 two-stat, 3 HIT growth, 4 SPD, 5 single-stat %, 6… | low |
| 0x4C | `mana_cost` | u32 | Mana cost to learn (100/250/1000/5000/30000 for character evilities, 10000 for a class unique, 0 = cannot… | high |
| 0x50 | `shop_rank` | u32 | 0..4 (one 9) on generic shop evilities 4000-4324; probably shop rank / unlock tier (elemental bodies 2,… | low |
| 0x54 | `owner` | u32 | → `char.dat` character/class that owns or teaches it (char.dat id at +0xD0; Killia 10, Warrior family 1010); 0 = generic | high |
| 0x58 | `class_order` | u32 | generic classes: learn order 1..5, 6 = the class unique (x0) evility; 0 otherwise | medium |
| 0x5C | `version_group` | u32 | 100 base game; 110-150 DLC content groups (same codes as magic.dat version_group); 0 on 2 unused rows | low |

## SkitFlagList.dat

Layout `fixed` · 44 bytes per row · key `id` · 22 of 23 fields named

When a skit becomes available at the base: req_on = story flags that must be set (e.g. 100 FLAG_SCENARIO_START, 199 episode 1 clear), req_off = flags that must be clear (next story flag, 7000+n "skit seen"), set_on = flags set when played (7000+n, 8000+n read flag). set_off unused (count 0).

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | skit entry 1..109 (same ids as SkitStartCharSetting) | high |
| 0x4 | `skit_no` | u16 | → `skit` skit number -> skit%04d.dat | high |
| 0x6 | `req_on1` | u16 | → `flag:FlagListDataBase.dat` flags that must all be ON for the entry to be listed | medium |
| 0x8 | `req_on2` | u16 | → `flag:FlagListDataBase.dat` flags that must all be ON for the entry to be listed | medium |
| 0xA | `req_on3` | u16 | → `flag:FlagListDataBase.dat` flags that must all be ON for the entry to be listed | medium |
| 0xC | `req_on4` | u16 | → `flag:FlagListDataBase.dat` flags that must all be ON for the entry to be listed | medium |
| 0xE | `req_off1` | u16 | → `flag:FlagListDataBase.dat` flags that must be OFF (tutorial rows: 3312/3313) | low |
| 0x10 | `req_off2` | u16 | → `flag:FlagListDataBase.dat` flags that must be OFF (tutorial rows: 3312/3313) | low |
| 0x12 | `req_off3` | u16 | → `flag:FlagListDataBase.dat` flags that must be OFF (tutorial rows: 3312/3313) | low |
| 0x14 | `req_off4` | u16 | → `flag:FlagListDataBase.dat` flags that must be OFF (tutorial rows: 3312/3313) | low |
| 0x16 | `set_on1` | u16 | → `flag:FlagListDataBase.dat` flags set ON before playback (Suicidal Decision: 1650; skits: 8000+n read flag) | medium |
| 0x18 | `set_on2` | u16 | → `flag:FlagListDataBase.dat` flags set ON before playback (Suicidal Decision: 1650; skits: 8000+n read flag) | medium |
| 0x1A | `set_on3` | u16 | → `flag:FlagListDataBase.dat` flags set ON before playback (Suicidal Decision: 1650; skits: 8000+n read flag) | medium |
| 0x1C | `set_on4` | u16 | → `flag:FlagListDataBase.dat` flags set ON before playback (Suicidal Decision: 1650; skits: 8000+n read flag) | medium |
| 0x1E | `set_off1` | u16 | → `flag:FlagListDataBase.dat` flags cleared before playback (Tragic Ending: 1650) | medium |
| 0x20 | `set_off2` | u16 | → `flag:FlagListDataBase.dat` flags cleared before playback (Tragic Ending: 1650) | medium |
| 0x22 | `set_off3` | u16 | → `flag:FlagListDataBase.dat` flags cleared before playback (Tragic Ending: 1650) | medium |
| 0x24 | `set_off4` | u16 | → `flag:FlagListDataBase.dat` flags cleared before playback (Tragic Ending: 1650) | medium |
| 0x26 | `req_on_count` | u8 | number of used req_on entries (0..4) | high |
| 0x27 | `req_off_count` | u8 | number of used req_off entries (0..4) | high |
| 0x28 | `set_on_count` | u8 | number of used set_on entries (0..4) | high |
| 0x29 | `set_off_count` | u8 | number of used set_off entries (0..4) | high |

## SkitStartCharSetting.dat

Layout `fixed` · 20 bytes per row · key `id` · 17 of 17 fields named

Which main characters take part in each skit (16 boolean slots, 13..15 unused). Skit ids match SkitFlagList.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | skit entry id 1..109 (= SkitFlagList id) | high |
| 0x4 | `present_killia` | u8 | 1 = character present in this skit (must be in the party/base). Index order = saveicon.dat order. | high |
| 0x5 | `present_seraphina` | u8 | 1 = character present in this skit (must be in the party/base). Index order = saveicon.dat order. | high |
| 0x6 | `present_red_magnus` | u8 | 1 = character present in this skit (must be in the party/base). Index order = saveicon.dat order. | high |
| 0x7 | `present_christo` | u8 | 1 = character present in this skit (must be in the party/base). Index order = saveicon.dat order. | high |
| 0x8 | `present_usalia` | u8 | 1 = character present in this skit (must be in the party/base). Index order = saveicon.dat order. | high |
| 0x9 | `present_zeroken` | u8 | 1 = character present in this skit (must be in the party/base). Index order = saveicon.dat order. | high |
| 0xA | `present_goldion` | u8 | 1 = character present in this skit (must be in the party/base). Index order = saveicon.dat order. Goldion… | medium |
| 0xB | `present_slot07` | u8 | 1 = character present in this skit (must be in the party/base). Index order = saveicon.dat order. probably… | low |
| 0xC | `present_slot08` | u8 | 1 = character present in this skit (must be in the party/base). Index order = saveicon.dat order. probably… | low |
| 0xD | `present_slot09` | u8 | 1 = character present in this skit (must be in the party/base). Index order = saveicon.dat order. probably… | low |
| 0xE | `present_slot10` | u8 | 1 = character present in this skit (must be in the party/base). Index order = saveicon.dat order. unknown… | low |
| 0xF | `present_prinny` | u8 | 1 = character present in this skit (must be in the party/base). Index order = saveicon.dat order. Prinny… | medium |
| 0x10 | `present_asagi` | u8 | 1 = character present in this skit (must be in the party/base). Index order = saveicon.dat order. Asagi… | medium |
| 0x11 | `present_slot13` | u8 | 1 = character present in this skit (must be in the party/base). Index order = saveicon.dat order. never set | low |
| 0x12 | `present_slot14` | u8 | 1 = character present in this skit (must be in the party/base). Index order = saveicon.dat order. never set | low |
| 0x13 | `present_slot15` | u8 | 1 = character present in this skit (must be in the party/base). Index order = saveicon.dat order. never set | low |

## SkitViewer.dat

Layout `fixed` · 484 bytes per row · key `id` · 27 of 28 fields named

Skit replay viewer list (same record layout as EventViewer). req_on1 = set_on1 = skit read flag 8000+n (FLAG_SKIT_READ_START = 8000).

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | 1 = header row, entries 101..209 | high |
| 0x4 | `skit_no` | u16 | → `skit` skit number -> Skit/Skit_0.dat member skit%04d.dat (101..1605, 2001..2030) | high |
| 0x6 | `name (×5 languages)` | str(88) | skit title | high |
| 0x1BE | `req_on1` | u16 | → `flag:FlagListDataBase.dat` flags that must all be ON for the entry to be listed | medium |
| 0x1C0 | `req_on2` | u16 | → `flag:FlagListDataBase.dat` flags that must all be ON for the entry to be listed | medium |
| 0x1C2 | `req_on3` | u16 | → `flag:FlagListDataBase.dat` flags that must all be ON for the entry to be listed | medium |
| 0x1C4 | `req_on4` | u16 | → `flag:FlagListDataBase.dat` flags that must all be ON for the entry to be listed | medium |
| 0x1C6 | `req_off1` | u16 | → `flag:FlagListDataBase.dat` flags that must be OFF (tutorial rows: 3312/3313) | low |
| 0x1C8 | `req_off2` | u16 | → `flag:FlagListDataBase.dat` flags that must be OFF (tutorial rows: 3312/3313) | low |
| 0x1CA | `req_off3` | u16 | → `flag:FlagListDataBase.dat` flags that must be OFF (tutorial rows: 3312/3313) | low |
| 0x1CC | `req_off4` | u16 | → `flag:FlagListDataBase.dat` flags that must be OFF (tutorial rows: 3312/3313) | low |
| 0x1CE | `set_on1` | u16 | → `flag:FlagListDataBase.dat` flags set ON before playback (Suicidal Decision: 1650; skits: 8000+n read flag) | medium |
| 0x1D0 | `set_on2` | u16 | → `flag:FlagListDataBase.dat` flags set ON before playback (Suicidal Decision: 1650; skits: 8000+n read flag) | medium |
| 0x1D2 | `set_on3` | u16 | → `flag:FlagListDataBase.dat` flags set ON before playback (Suicidal Decision: 1650; skits: 8000+n read flag) | medium |
| 0x1D4 | `set_on4` | u16 | → `flag:FlagListDataBase.dat` flags set ON before playback (Suicidal Decision: 1650; skits: 8000+n read flag) | medium |
| 0x1D6 | `set_off1` | u16 | → `flag:FlagListDataBase.dat` flags cleared before playback (Tragic Ending: 1650) | medium |
| 0x1D8 | `set_off2` | u16 | → `flag:FlagListDataBase.dat` flags cleared before playback (Tragic Ending: 1650) | medium |
| 0x1DA | `set_off3` | u16 | → `flag:FlagListDataBase.dat` flags cleared before playback (Tragic Ending: 1650) | medium |
| 0x1DC | `set_off4` | u16 | → `flag:FlagListDataBase.dat` flags cleared before playback (Tragic Ending: 1650) | medium |
| 0x1DE | `req_on_count` | u8 | number of used req_on entries (0..4) | high |
| 0x1DF | `req_off_count` | u8 | number of used req_off entries (0..4) | high |
| 0x1E0 | `set_on_count` | u8 | number of used set_on entries (0..4) | high |
| 0x1E1 | `set_off_count` | u8 | number of used set_off entries (0..4) | high |

## Sound_BGM_0.dat

Layout `fixed` · 144 bytes per row · key `id` · 5 of 5 fields named

Index/loop table for the music archive Data/Sound_BGM/Sound_BGM_0.dat. When adding a new track: add NNN.ogg to the archive (d5lib.sound.build_track), a row here (loop_start = intro samples, loop_end = intro+loop samples) and a music.dat row if it should appear in the jukebox.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | file number: member "<id>.ogg" of Data/Sound_BGM/Sound_BGM_0.dat (PS_FS_V1); all 78 match the archive 1:1.… | high |
| 0x4 | `label` | str(128) | internal JP label (e.g. "ＢＧＭ０１", "配信ＢＧＭ１－１" = DLC, "ＪＩＮ" = jingles) | high |
| 0x84 | `volume` | u32 | playback volume 40..80 (mostly 40-60) | medium |
| 0x88 | `loop_start` | u32 | loop start in samples (48 kHz) = sample count of the intro Ogg stream (verified on all tracks); 0 for jingles | high |
| 0x8C | `loop_end` | u32 | loop end in samples = intro + loop stream samples (verified; outro never played) | high |

## Sound_Msg_EN_0.dat

Layout `fixed` · 8 bytes per row · key `id` · 2 of 3 fields named

EN dub story voice (dialogue) index for Sound_Msg_0.dat (msg_en/ members). FAFULLFS header: "FAFULLFS", u32 file count, u16 0x6c, u16 0x6c, u64 name-table offset, u64 name-table size, u64 end of data; data starts at 0x200.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | message voice id: member "msg_en/%09d.ogg" of Data/Sound_Msg/Sound_Msg_0.dat (FAFULLFS archive; directory… | high |
| 0x4 | `volume` | u16 | volume 11..150 | medium |

## Sound_Msg_JP_0.dat

Layout `fixed` · 8 bytes per row · key `id` · 2 of 3 fields named

JP dub story voice (dialogue) index for Sound_Msg_0.dat (msg_jp/ members). FAFULLFS header: "FAFULLFS", u32 file count, u16 0x6c, u16 0x6c, u64 name-table offset, u64 name-table size, u64 end of data; data starts at 0x200.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | message voice id: member "msg_jp/%09d.ogg" of Data/Sound_Msg/Sound_Msg_0.dat (FAFULLFS archive; directory… | high |
| 0x4 | `volume` | u16 | volume 11..150 | medium |

## Sound_SE_0.dat

Layout `fixed` · 16 bytes per row · key `id` · 5 of 6 fields named

Sound effect index (per .vag in Sound_SE_0.dat archive).

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | sound effect number: member "%04d.vag" of Data/Sound_SE/Sound_SE_0.dat (DSARC FL); all 381 match the archive | high |
| 0x4 | `flag_004` | u8 | 1 for ids 1-909 (battle/field SE), 0 for 1001-1061 (UI/system?), 0 for 1062+2000-2011 | low |
| 0x5 | `loop` | u8 | 1 for id 1062 and 2000-2011 (long ~10 s ambient sounds) -> probably looping | low |
| 0x8 | `rate` | u32 | sample rate, always 48000 (= .vag header rate) | high |
| 0xC | `volume` | u32 | volume 13..50 (372 rows = 25) | medium |

## Sound_Voice_EN_0.dat

Layout `fixed` · 48 bytes per row · key `id` · 38 of 39 fields named

EN dub battle/unit voice index for Sound_Voice_0.dat (voice_en/NNNNNN.dat packs). For a new unit: either reuse an existing voice group via char.dat voice id, or add a voice pack + rows.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | voice line id = group*1000 + n; the sound is member "%09d.vag" of the group file | high |
| 0x4 | `group` | u32 | voice set: file "voice_en/%06d.dat" (a DSARC FL pack) inside Data/Sound_Voice/Sound_Voice_0.dat. group =… | medium |
| 0x8 | `volume` | u16 | volume 15..100 (JP/EN dubs differ here) | medium |
| 0xA | `sit_00` | u8 | situation flag 0/1: line may be used for trigger category #0 (attack/skill/damage/KO/...; mapping not… | low |
| 0xB | `sit_01` | u8 | situation flag 0/1: line may be used for trigger category #1 (attack/skill/damage/KO/...; mapping not… | low |
| 0xC | `sit_02` | u8 | situation flag 0/1: line may be used for trigger category #2 (attack/skill/damage/KO/...; mapping not… | low |
| 0xD | `sit_03` | u8 | situation flag 0/1: line may be used for trigger category #3 (attack/skill/damage/KO/...; mapping not… | low |
| 0xE | `sit_04` | u8 | situation flag 0/1: line may be used for trigger category #4 (attack/skill/damage/KO/...; mapping not… | low |
| 0xF | `sit_05` | u8 | situation flag 0/1: line may be used for trigger category #5 (attack/skill/damage/KO/...; mapping not… | low |
| 0x10 | `sit_06` | u8 | situation flag 0/1: line may be used for trigger category #6 (attack/skill/damage/KO/...; mapping not… | low |
| 0x11 | `sit_07` | u8 | situation flag 0/1: line may be used for trigger category #7 (attack/skill/damage/KO/...; mapping not… | low |
| 0x12 | `sit_08` | u8 | situation flag 0/1: line may be used for trigger category #8 (attack/skill/damage/KO/...; mapping not… | low |
| 0x13 | `sit_09` | u8 | situation flag 0/1: line may be used for trigger category #9 (attack/skill/damage/KO/...; mapping not… | low |
| 0x14 | `sit_10` | u8 | situation flag 0/1: line may be used for trigger category #10 (attack/skill/damage/KO/...; mapping not… | low |
| 0x15 | `sit_11` | u8 | situation flag 0/1: line may be used for trigger category #11 (attack/skill/damage/KO/...; mapping not… | low |
| 0x16 | `sit_12` | u8 | situation flag 0/1: line may be used for trigger category #12 (attack/skill/damage/KO/...; mapping not… | low |
| 0x17 | `sit_13` | u8 | situation flag 0/1: line may be used for trigger category #13 (attack/skill/damage/KO/...; mapping not… | low |
| 0x18 | `sit_14` | u8 | situation flag 0/1: line may be used for trigger category #14 (attack/skill/damage/KO/...; mapping not… | low |
| 0x19 | `sit_15` | u8 | situation flag 0/1: line may be used for trigger category #15 (attack/skill/damage/KO/...; mapping not… | low |
| 0x1A | `sit_16` | u8 | situation flag 0/1: line may be used for trigger category #16 (attack/skill/damage/KO/...; mapping not… | low |
| 0x1B | `sit_17` | u8 | situation flag 0/1: line may be used for trigger category #17 (attack/skill/damage/KO/...; mapping not… | low |
| 0x1C | `sit_18` | u8 | situation flag 0/1: line may be used for trigger category #18 (attack/skill/damage/KO/...; mapping not… | low |
| 0x1D | `sit_19` | u8 | situation flag 0/1: line may be used for trigger category #19 (attack/skill/damage/KO/...; mapping not… | low |
| 0x1E | `sit_20` | u8 | situation flag 0/1: line may be used for trigger category #20 (attack/skill/damage/KO/...; mapping not… | low |
| 0x1F | `sit_21` | u8 | situation flag 0/1: line may be used for trigger category #21 (attack/skill/damage/KO/...; mapping not… | low |
| 0x20 | `sit_22` | u8 | situation flag 0/1: line may be used for trigger category #22 (attack/skill/damage/KO/...; mapping not… | low |
| 0x21 | `sit_23` | u8 | situation flag 0/1: line may be used for trigger category #23 (attack/skill/damage/KO/...; mapping not… | low |
| 0x22 | `sit_24` | u8 | situation flag 0/1: line may be used for trigger category #24 (attack/skill/damage/KO/...; mapping not… | low |
| 0x23 | `sit_25` | u8 | situation flag 0/1: line may be used for trigger category #25 (attack/skill/damage/KO/...; mapping not… | low |
| 0x24 | `sit_26` | u8 | situation flag 0/1: line may be used for trigger category #26 (attack/skill/damage/KO/...; mapping not… | low |
| 0x25 | `sit_27` | u8 | situation flag 0/1: line may be used for trigger category #27 (attack/skill/damage/KO/...; mapping not… | low |
| 0x26 | `sit_28` | u8 | situation flag 0/1: line may be used for trigger category #28 (attack/skill/damage/KO/...; mapping not… | low |
| 0x27 | `sit_29` | u8 | situation flag 0/1: line may be used for trigger category #29 (attack/skill/damage/KO/...; mapping not… | low |
| 0x28 | `sit_30` | u8 | situation flag 0/1: line may be used for trigger category #30 (attack/skill/damage/KO/...; mapping not… | low |
| 0x29 | `sit_31` | u8 | situation flag 0/1: line may be used for trigger category #31 (attack/skill/damage/KO/...; mapping not… | low |
| 0x2A | `sit_32` | u8 | situation flag 0/1: line may be used for trigger category #32 (attack/skill/damage/KO/...; mapping not… | low |
| 0x2B | `sit_33` | u8 | situation flag 0/1: line may be used for trigger category #33 (attack/skill/damage/KO/...; mapping not… | low |
| 0x2C | `enabled` | u8 | 1 for 19485 lines, 0 for 111 (e.g. 130010002..) | low |

## Sound_Voice_JP_0.dat

Layout `fixed` · 48 bytes per row · key `id` · 38 of 39 fields named

JP dub battle/unit voice index for Sound_Voice_0.dat (voice_jp/NNNNNN.dat packs). For a new unit: either reuse an existing voice group via char.dat voice id, or add a voice pack + rows.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | voice line id = group*1000 + n; the sound is member "%09d.vag" of the group file | high |
| 0x4 | `group` | u32 | voice set: file "voice_jp/%06d.dat" (a DSARC FL pack) inside Data/Sound_Voice/Sound_Voice_0.dat. group =… | medium |
| 0x8 | `volume` | u16 | volume 15..100 (JP/EN dubs differ here) | medium |
| 0xA | `sit_00` | u8 | situation flag 0/1: line may be used for trigger category #0 (attack/skill/damage/KO/...; mapping not… | low |
| 0xB | `sit_01` | u8 | situation flag 0/1: line may be used for trigger category #1 (attack/skill/damage/KO/...; mapping not… | low |
| 0xC | `sit_02` | u8 | situation flag 0/1: line may be used for trigger category #2 (attack/skill/damage/KO/...; mapping not… | low |
| 0xD | `sit_03` | u8 | situation flag 0/1: line may be used for trigger category #3 (attack/skill/damage/KO/...; mapping not… | low |
| 0xE | `sit_04` | u8 | situation flag 0/1: line may be used for trigger category #4 (attack/skill/damage/KO/...; mapping not… | low |
| 0xF | `sit_05` | u8 | situation flag 0/1: line may be used for trigger category #5 (attack/skill/damage/KO/...; mapping not… | low |
| 0x10 | `sit_06` | u8 | situation flag 0/1: line may be used for trigger category #6 (attack/skill/damage/KO/...; mapping not… | low |
| 0x11 | `sit_07` | u8 | situation flag 0/1: line may be used for trigger category #7 (attack/skill/damage/KO/...; mapping not… | low |
| 0x12 | `sit_08` | u8 | situation flag 0/1: line may be used for trigger category #8 (attack/skill/damage/KO/...; mapping not… | low |
| 0x13 | `sit_09` | u8 | situation flag 0/1: line may be used for trigger category #9 (attack/skill/damage/KO/...; mapping not… | low |
| 0x14 | `sit_10` | u8 | situation flag 0/1: line may be used for trigger category #10 (attack/skill/damage/KO/...; mapping not… | low |
| 0x15 | `sit_11` | u8 | situation flag 0/1: line may be used for trigger category #11 (attack/skill/damage/KO/...; mapping not… | low |
| 0x16 | `sit_12` | u8 | situation flag 0/1: line may be used for trigger category #12 (attack/skill/damage/KO/...; mapping not… | low |
| 0x17 | `sit_13` | u8 | situation flag 0/1: line may be used for trigger category #13 (attack/skill/damage/KO/...; mapping not… | low |
| 0x18 | `sit_14` | u8 | situation flag 0/1: line may be used for trigger category #14 (attack/skill/damage/KO/...; mapping not… | low |
| 0x19 | `sit_15` | u8 | situation flag 0/1: line may be used for trigger category #15 (attack/skill/damage/KO/...; mapping not… | low |
| 0x1A | `sit_16` | u8 | situation flag 0/1: line may be used for trigger category #16 (attack/skill/damage/KO/...; mapping not… | low |
| 0x1B | `sit_17` | u8 | situation flag 0/1: line may be used for trigger category #17 (attack/skill/damage/KO/...; mapping not… | low |
| 0x1C | `sit_18` | u8 | situation flag 0/1: line may be used for trigger category #18 (attack/skill/damage/KO/...; mapping not… | low |
| 0x1D | `sit_19` | u8 | situation flag 0/1: line may be used for trigger category #19 (attack/skill/damage/KO/...; mapping not… | low |
| 0x1E | `sit_20` | u8 | situation flag 0/1: line may be used for trigger category #20 (attack/skill/damage/KO/...; mapping not… | low |
| 0x1F | `sit_21` | u8 | situation flag 0/1: line may be used for trigger category #21 (attack/skill/damage/KO/...; mapping not… | low |
| 0x20 | `sit_22` | u8 | situation flag 0/1: line may be used for trigger category #22 (attack/skill/damage/KO/...; mapping not… | low |
| 0x21 | `sit_23` | u8 | situation flag 0/1: line may be used for trigger category #23 (attack/skill/damage/KO/...; mapping not… | low |
| 0x22 | `sit_24` | u8 | situation flag 0/1: line may be used for trigger category #24 (attack/skill/damage/KO/...; mapping not… | low |
| 0x23 | `sit_25` | u8 | situation flag 0/1: line may be used for trigger category #25 (attack/skill/damage/KO/...; mapping not… | low |
| 0x24 | `sit_26` | u8 | situation flag 0/1: line may be used for trigger category #26 (attack/skill/damage/KO/...; mapping not… | low |
| 0x25 | `sit_27` | u8 | situation flag 0/1: line may be used for trigger category #27 (attack/skill/damage/KO/...; mapping not… | low |
| 0x26 | `sit_28` | u8 | situation flag 0/1: line may be used for trigger category #28 (attack/skill/damage/KO/...; mapping not… | low |
| 0x27 | `sit_29` | u8 | situation flag 0/1: line may be used for trigger category #29 (attack/skill/damage/KO/...; mapping not… | low |
| 0x28 | `sit_30` | u8 | situation flag 0/1: line may be used for trigger category #30 (attack/skill/damage/KO/...; mapping not… | low |
| 0x29 | `sit_31` | u8 | situation flag 0/1: line may be used for trigger category #31 (attack/skill/damage/KO/...; mapping not… | low |
| 0x2A | `sit_32` | u8 | situation flag 0/1: line may be used for trigger category #32 (attack/skill/damage/KO/...; mapping not… | low |
| 0x2B | `sit_33` | u8 | situation flag 0/1: line may be used for trigger category #33 (attack/skill/damage/KO/...; mapping not… | low |
| 0x2C | `enabled` | u8 | 1 for 19485 lines, 0 for 111 (e.g. 130010002..) | low |

## SPC_Delivery.dat

Layout `fixed` · 6116 bytes per row · key `id` · 14 of 14 fields named

Special delivery (DLC content viewer) list, full 5-language version (56 rows). type-3 rows link a DLC to the char.dat unit it adds (used for the stats viewer).

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | entry id (1..27 top level; 1001.. / 2001.. children of scenario packs) | high |
| 0x4 | `dlc_id` | u32 | DLC content id of top-level entries (1000xx char, 3000xx scenario, 6000xx items/money); 0 for child entries | high |
| 0x8 | `type` | u32 | entry type | high |
| 0xC | `value` | u32 | type 3: char.dat id (char offset 208; 550 Girl Laharl, 510 Laharl, 860.. Helper Prinnies); type 2: help… | high |
| 0x10 | `name (×5 languages)` | str(196) |  | high |
| 0x3E4 | `desc (×5 languages)` | str(1024) |  | high |

## SPC_Shop.dat

Layout `fixed` · 17688 bytes per row · key `id` · 37 of 37 fields named

Special (DLC) shop list, full 5-language version (26 rows x 17688 bytes). Only text + DLC content id; what a DLC grants is decided by code from dlc_id.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | DLC shop entry id (1, 9..36) | high |
| 0x4 | `dlc_id` | u32 | DLC content id: 1000xx characters, 3000xx scenarios, 6000xx item/money packs (same numbering as… | high |
| 0x8 | `name (×5 languages)` | str(196) |  | high |
| 0x3DC | `desc (×5 languages)` | str(1024) |  | high |
| 0x17DC | `detail1 (×5 languages)` | str(260) |  | high |
| 0x1CF0 | `detail2 (×5 languages)` | str(260) |  | high |
| 0x2204 | `detail3 (×5 languages)` | str(260) |  | high |
| 0x2718 | `received (×5 languages)` | str(768) |  | high |
| 0x3618 | `not_received (×5 languages)` | str(768) |  | high |

## StringListDataBase.dat

Layout `pool` · 44 bytes per row · key `id` · 11 of 11 fields named

Generic localized UI/system string table (pool layout: id + 5 x (char count, string pointer)). Texts are looked up by id from code (e.g. 0x2c10/0x2c11 = 11280/11281). PC exe picks the slot by game language (0=JP,1=EN,2=FR); language ids 3/4 are remapped to slot 1, so CHN/KOR slots appear unused on PC. Field names kept as name_* (used by test_roundtrip edit test).

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | string id, sorted (exe binary search 0x140139ed0). Ranges: 10001-12789 system/UI messages, 12798-13084,… | high |
| 0x4 | `name_JP_len` | u32 | character count of name_JP (auto-computed on write) | high |
| 0x8 | `name (×5 languages)` | strptr |  | high |
| 0xC | `name_EN_len` | u32 | character count of name_EN (auto-computed on write) | high |
| 0x14 | `name_FR_len` | u32 | character count of name_FR (auto-computed on write) | high |
| 0x1C | `name_CHN_len` | u32 | character count of name_CHN (auto-computed on write) | high |
| 0x24 | `name_KOR_len` | u32 | character count of name_KOR (auto-computed on write) | high |

## SugorokuMapSetting.dat

Layout `fixed` · 244 bytes per row · 5 of 66 fields named

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x4 | `name (×5 languages)` | str(20) |  | medium |

## TextureDataBase.dat

Layout `fixed` · 124 bytes per row · key `id` · 31 of 31 fields named

UI sprite atlas: rectangle of a UI texture (.txf in Start_*.dat/Texture*) per sprite id, with one rect set per language (75 rows differ: EN/FR text-bearing graphics). PC exe picks the slot by game language (0=JP,1=EN,2=FR); language ids 3/4 are remapped to slot 1, so CHN/KOR slots appear unused on PC. Relevant for new units: face icons live in wf_*_48px/64px.txf with ids 10836-12469 (which id a unit uses is computed by code, no char.dat field holds a TextureDataBase id). Adding a row beyond 13172 or a new texture requires an exe patch; editing rects/pixels of existing ids does not. Rect maxima exceed some Texture_opt txf sizes (those are downscaled variants).

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | sprite id 10000..13172. Row index = id-10000 (the exe indexes rows directly, table must stay contiguous… | high |
| 0x4 | `x (×5 languages)` | u32 | JP: source rect left (texture pixels) | high |
| 0x8 | `y (×5 languages)` | u32 | JP: source rect top | high |
| 0xC | `w (×5 languages)` | u32 | JP: source rect width | high |
| 0x10 | `h (×5 languages)` | u32 | JP: source rect height | high |
| 0x14 | `draw_w (×5 languages)` | u32 | JP: drawn width (callers may override; equals w except 1 row) | high |
| 0x18 | `draw_h (×5 languages)` | u32 | JP: drawn height | high |

## THIEF.dat

Layout `fixed` · 232 bytes per row · key `id` · 7 of 9 fields named

Names of intangible things stolen when Steal takes something that is not an item (Dreams, Affection, Allowance ...), grouped by 'group' (11 = money: Piggy Bank, Avarice, Allowance).

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x4 | `id` | u32 | entry id 1..78 | high |
| 0x8 | `group` | u16 | category; probably the stat that is stolen (8 groups + 10, 11) | low |
| 0xA | `name (×5 languages)` | str(44) |  | high |

## Torture.dat

Layout `fixed` · 888 bytes per row · key `id` · 12 of 12 fields named

Interrogation (torture room) methods: 94 rows, id/name/description + a small category value. Ids 100+ are unit-specific methods (e.g. "Sister's Story", "Too Many Asagis").

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | interrogation method id: 1..4 generic, 100..189 character-specific | high |
| 0x4 | `name (×5 languages)` | str(52) |  | high |
| 0x108 | `desc (×5 languages)` | str(124) |  | high |
| 0x374 | `category` | u32 | values 0,1,3,5 (25/21/24/24 rows); probably method type/reaction group; unverified | low |

## TownEditMessageDataBase.dat

Layout `pool` · 76 bytes per row · key `id` · 19 of 19 fields named

Lines spoken by units you assign as facility NPCs in the pocket netherworld (Netherworld Edit), per facility role x personality (x variant). 3 text lines x 5 languages. Pool table.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | row id 0..5319 | high |
| 0x4 | `role` | u32 | facility / NPC role whose line this is: 0 Dimension Guide, 10 Carnage, 20 Item World, 30 Chara World, 40… | medium |
| 0x8 | `personality` | u32 | → `charPersonal.dat` speaker personality: 101..126 / 201..221 = charPersonal.dat personality id (u16 at charPersonal +4); 1..80… | medium |
| 0xC | `situation` | u32 | 0..2 = message situation/variant (3 variants for 101..221 personalities) | low |
| 0x10 | `line1 (×5 languages)` | strptr |  | high |
| 0x24 | `line2 (×5 languages)` | strptr |  | high |
| 0x38 | `line3 (×5 languages)` | strptr |  | high |

## TownEtcCenter.dat

Layout `fixed` · 52 bytes per row · key `id` · 10 of 15 fields named

Misc interactive objects placed in the pocket netherworld base (13 rows): position, message id, flag window.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | object id 1..13 | high |
| 0x4 | `pos_x` | u32 | x position on the base map | medium |
| 0x8 | `pos_y` | u32 | y position on the base map | medium |
| 0xC | `pos_z` | i32 | -159..-42; height or angle | low |
| 0x10 | `msg_id` | u32 | 7000000 + 50*n: talk/event message id | low |
| 0x18 | `obj_type` | u32 | 7/10/12/14 object/model type | low |
| 0x20 | `show_flag` | u32 | → `flag:FlagListDataBase.dat` object shown when flag ON (used when show_enabled) | medium |
| 0x28 | `hide_flag` | u32 | → `flag:FlagListDataBase.dat` object hidden when flag ON (used when hide_enabled) | medium |
| 0x30 | `show_enabled` | u8 | 1 = show_flag is checked | medium |
| 0x31 | `hide_enabled` | u8 | 1 = hide_flag is checked | medium |

## TownFuncCharCenter.dat

Layout `fixed` · 384 bytes per row · key `id` · 18 of 25 fields named

NPCs in the pocket netherworld base: facility shopkeepers, residents and story party members standing around. char_id -> char.dat, placement, facility type and a show/hide flag pair.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | NPC slot id 0..50 (20 missing) | high |
| 0x4 | `char_id` | u32 | → `char.dat` character shown: 3001..3033 shop NPCs, 190 Yellow Prinny, 10/40/.. party uniques. char.dat id (u32 at… | high |
| 0x8 | `level` | i32 | NPC level per old scheme (30, 210, ...; -1 for party uniques = use roster unit) | low |
| 0xC | `direction` | i32 | facing angle in degrees (-180..165, multiples of 15) | medium |
| 0x10 | `pos_x` | u32 | x position on the base map | medium |
| 0x14 | `pos_y` | u32 | y position on the base map | medium |
| 0x18 | `msg_id` | u32 | talk/facility message id (2002000.., residents 2110000+25*n) | low |
| 0x1C | `shop_type` | u32 | facility/menu opened (0 none, 1 equipment, 2 items, 3 hospital, 4 strategy, 5 dimension guide, 6 item… | medium |
| 0x20 | `name (×5 languages)` | str(64) | NPC/facility label (JP-only internal names for residents) | high |
| 0x164 | `npc_kind` | u32 | NPC kind | medium |
| 0x16C | `show_flag` | u32 | → `flag:FlagListDataBase.dat` NPC appears when flag ON (if show_enabled): facility flags 14001.., scenario flags, DLC flags | high |
| 0x174 | `hide_flag` | u32 | → `flag:FlagListDataBase.dat` NPC disappears when flag ON (if hide_enabled); e.g. old Item Worlder replaced after 14011… | high |
| 0x17C | `show_enabled` | u8 | 1 = show_flag is checked | medium |
| 0x17D | `hide_enabled` | u8 | 1 = hide_flag is checked | medium |

## TownPartyCharCenter.dat

Layout `fixed` · 28 bytes per row · key `id` · 5 of 7 fields named

Spots in the pocket netherworld where roster (party) characters can be placed/stand. No char ids: the occupant is chosen at runtime.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | spot id 0..40 | high |
| 0x4 | `direction` | i32 | facing angle in degrees | medium |
| 0x8 | `pos_x` | u32 | x position on the base map | medium |
| 0xC | `pos_y` | u32 | y position on the base map | medium |
| 0x10 | `occupied_flag` | u32 | → `flag:FlagListDataBase.dat` FLAG_TOWN_PARTYCHARA_nn (6601..6641): "a party character is placed here" | high |

## TownTreasureCenter.dat

Layout `fixed` · 36 bytes per row · key `id` · 8 of 9 fields named

Treasure chests in the pocket netherworld base, 3 per chapter, visible in a story-flag window.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | chest id 0..50 | high |
| 0x4 | `direction` | i32 | facing angle (-180/-90/0/90) | medium |
| 0x8 | `pos_x` | u32 | x position on the base map | medium |
| 0xC | `pos_y` | u32 | y position on the base map | medium |
| 0x10 | `opened_flag` | u32 | → `flag:FlagListDataBase.dat` FLAG_TOWN_TREASURE_START(2600)+id: set when the chest is opened | high |
| 0x14 | `show_flag` | i32 | → `flag:FlagListDataBase.dat` chest appears after this scenario flag (-1 = from start), e.g. 199 = ep.1 clear | medium |
| 0x18 | `hide_flag` | i32 | → `flag:FlagListDataBase.dat` chest disappears after this scenario flag (-1 = never) | medium |
| 0x1C | `treasure_id` | u32 | always equal to opened_flag (2600+id); contents lookup id? | low |

## Trophy.dat

Layout `fixed` · 8768 bytes per row · key `id` · 28 of 30 fields named

Trophies/achievements list (51 rows). Name, condition text, up to 3 flavor lines, grade and hidden flag. Unlock logic is hard-coded (conditions are text only).

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | trophy id 1..51 | high |
| 0x4 | `name (×5 languages)` | str(196) |  | high |
| 0xDEC | `condition (×5 languages)` | str(260) | unlock condition text ("Condition：...") | high |
| 0x1300 | `flavor1 (×5 languages)` | str(260) | flavor text / quote, line 1 | high |
| 0x1814 | `flavor2 (×5 languages)` | str(260) | flavor text line 2 (continuation; only a few rows, mostly EN) | high |
| 0x1D28 | `flavor3 (×5 languages)` | str(260) | flavor text line 3 (empty in all rows) | medium |
| 0x223C | `grade` | u8 | trophy grade (verified: row 1 "all trophies" = platinum, story clears bronze, Damage-oholic gold) | high |
| 0x223D | `hidden` | u8 | 1 = hidden/secret trophy (story-episode trophies, Carnage, Item World legend ones) | medium |

## WishDataBase.dat

Layout `pool` · 128 bytes per row · key `id` · 30 of 32 fields named

Dark Assembly bills ("wishes", 議題): 164 rows. Name/description, difficulty, Mana cost, approval bias and a flag program: listed if req_flags ON and block_flags OFF; on passing set_flags are turned ON and clear_flags OFF. Effects without flags (stat/item bills, party bills 201..243) are hard-coded by id. No bill creates/unlocks characters or classes directly (only party relation bills 201..243).

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | bill id: 1..28 utility (names/colours/reincarnate/subclass), 31..72 misc, 101..128 temporary effects,… | high |
| 0x4 | `name (×5 languages)` | strptr |  | high |
| 0x18 | `desc (×5 languages)` | strptr |  | high |
| 0x2C | `difficulty` | u32 | 0..5; 0 for menu-like bills (no vote), 5 for the hardest (strategy/MOV/range) | medium |
| 0x30 | `mana_cost` | u32 | Mana cost (e.g. Martial Training 1000/5000/10000/50000/100000; Raise Max Item Level 9999999) | medium |
| 0x34 | `vote_modifier` | i32 | -100..100: approval bias (10 typical; -100 "Will surely be denied" / strategy bills; negative for strong… | medium |
| 0x40 | `req_flag1` | u32 | → `flag:FlagListDataBase.dat` bill only listed if this flag is ON (0 = none), e.g. 16029 FLAG_WISH_UNIQUE_EVILITY, 1736… | high |
| 0x44 | `req_flag2` | u32 | → `flag:FlagListDataBase.dat` bill only listed if this flag is ON (0 = none), e.g. 16029 FLAG_WISH_UNIQUE_EVILITY, 1736… | medium |
| 0x48 | `req_flag3` | u32 | → `flag:FlagListDataBase.dat` bill only listed if this flag is ON (0 = none), e.g. 16029 FLAG_WISH_UNIQUE_EVILITY, 1736… | medium |
| 0x4C | `req_flag4` | u32 | → `flag:FlagListDataBase.dat` bill only listed if this flag is ON (0 = none), e.g. 16029 FLAG_WISH_UNIQUE_EVILITY, 1736… | medium |
| 0x50 | `block_flag1` | u32 | → `flag:FlagListDataBase.dat` bill not available while this flag is ON (0 = none); mutually exclusive bills list each other's flags… | high |
| 0x54 | `block_flag2` | u32 | → `flag:FlagListDataBase.dat` bill not available while this flag is ON (0 = none); mutually exclusive bills list each other's flags… | medium |
| 0x58 | `block_flag3` | u32 | → `flag:FlagListDataBase.dat` bill not available while this flag is ON (0 = none); mutually exclusive bills list each other's flags… | medium |
| 0x5C | `block_flag4` | u32 | → `flag:FlagListDataBase.dat` bill not available while this flag is ON (0 = none); mutually exclusive bills list each other's flags… | medium |
| 0x60 | `set_flag1` | u32 | → `flag:FlagListDataBase.dat` flag turned ON when the bill passes (0 = none): e.g. 14520 FLAG_GROUP_OPEN_EXP, 5211… | high |
| 0x64 | `set_flag2` | u32 | → `flag:FlagListDataBase.dat` flag turned ON when the bill passes (0 = none): e.g. 14520 FLAG_GROUP_OPEN_EXP, 5211… | medium |
| 0x68 | `set_flag3` | u32 | → `flag:FlagListDataBase.dat` flag turned ON when the bill passes (0 = none): e.g. 14520 FLAG_GROUP_OPEN_EXP, 5211… | medium |
| 0x6C | `set_flag4` | u32 | → `flag:FlagListDataBase.dat` flag turned ON when the bill passes (0 = none): e.g. 14520 FLAG_GROUP_OPEN_EXP, 5211… | medium |
| 0x70 | `clear_flag1` | u32 | → `flag:FlagListDataBase.dat` flag turned OFF when the bill passes (0 = none), e.g. "Cancel Prinny Day" clears 13001 | high |
| 0x74 | `clear_flag2` | u32 | → `flag:FlagListDataBase.dat` flag turned OFF when the bill passes (0 = none), e.g. "Cancel Prinny Day" clears 13001 | medium |
| 0x78 | `clear_flag3` | u32 | → `flag:FlagListDataBase.dat` flag turned OFF when the bill passes (0 = none), e.g. "Cancel Prinny Day" clears 13001 | medium |
| 0x7C | `clear_flag4` | u32 | → `flag:FlagListDataBase.dat` flag turned OFF when the bill passes (0 = none), e.g. "Cancel Prinny Day" clears 13001 | medium |

## WorldMapHelpDataBase.dat

Layout `pool` · 64 bytes per row · 15 of 16 fields named

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x4 | `name (×5 languages)` | strptr |  | medium |
| 0x18 | `desc (×5 languages)` | strptr |  | medium |
| 0x2C | `text2 (×5 languages)` | strptr |  | medium |

## zukan.dat

Layout `fixed` · 21288 bytes per row · key `id` · 282 of 282 fields named

Character encyclopedia (Records/zukan), 94 entries (story + generic classes + DLC guests). Each entry: name, char.dat id, 5 pages x 5 unlock-flag slots, and 5 pages x 10 lines x 5 languages of 84-byte text slots starting at +0x120 (line-major, language-minor interleave). Baseline string detection was misaligned (it started at the EN line); fixed here.

| Offset | Field | Type | Meaning | Conf. |
|---|---|---|---|---|
| 0x0 | `id` | u32 | entry id 0..93 (= row index) | high |
| 0x4 | `name (×5 languages)` | str(36) |  | high |
| 0xB8 | `char_id` | u32 | → `char.dat` character shown for this entry: char.dat id (u32 at char.dat record +0xD0; e.g. 10=Killia,… | high |
| 0xBC | `page1_flag1` | i32 | → `flag:FlagListDataBase.dat` unlock flag for encyclopedia page 1 (flag id, -1 = none). Slot 1 holds scenario flags (e.g. 101, 701,… | medium |
| 0xC0 | `page1_flag2` | i32 | → `flag:FlagListDataBase.dat` additional unlock flag for page 1 (always -1) | low |
| 0xC4 | `page1_flag3` | i32 | → `flag:FlagListDataBase.dat` additional unlock flag for page 1 (always -1) | low |
| 0xC8 | `page1_flag4` | i32 | → `flag:FlagListDataBase.dat` additional unlock flag for page 1 (always -1) | low |
| 0xCC | `page1_flag5` | i32 | → `flag:FlagListDataBase.dat` additional unlock flag for page 1 (always -1) | low |
| 0xD0 | `page2_flag1` | i32 | → `flag:FlagListDataBase.dat` unlock flag for encyclopedia page 2 (flag id, -1 = none). Slot 1 holds scenario flags (e.g. 101, 701,… | medium |
| 0xD4 | `page2_flag2` | i32 | → `flag:FlagListDataBase.dat` additional unlock flag for page 2 (always -1) | low |
| 0xD8 | `page2_flag3` | i32 | → `flag:FlagListDataBase.dat` additional unlock flag for page 2 (always -1) | low |
| 0xDC | `page2_flag4` | i32 | → `flag:FlagListDataBase.dat` additional unlock flag for page 2 (always -1) | low |
| 0xE0 | `page2_flag5` | i32 | → `flag:FlagListDataBase.dat` additional unlock flag for page 2 (always -1) | low |
| 0xE4 | `page3_flag1` | i32 | → `flag:FlagListDataBase.dat` unlock flag for encyclopedia page 3 (flag id, -1 = none). Slot 1 holds scenario flags (e.g. 101, 701,… | medium |
| 0xE8 | `page3_flag2` | i32 | → `flag:FlagListDataBase.dat` additional unlock flag for page 3 (always -1) | low |
| 0xEC | `page3_flag3` | i32 | → `flag:FlagListDataBase.dat` additional unlock flag for page 3 (always -1) | low |
| 0xF0 | `page3_flag4` | i32 | → `flag:FlagListDataBase.dat` additional unlock flag for page 3 (always -1) | low |
| 0xF4 | `page3_flag5` | i32 | → `flag:FlagListDataBase.dat` additional unlock flag for page 3 (always -1) | low |
| 0xF8 | `page4_flag1` | i32 | → `flag:FlagListDataBase.dat` unlock flag for encyclopedia page 4 (flag id, -1 = none). Slot 1 holds scenario flags (e.g. 101, 701,… | medium |
| 0xFC | `page4_flag2` | i32 | → `flag:FlagListDataBase.dat` additional unlock flag for page 4 (always -1) | low |
| 0x100 | `page4_flag3` | i32 | → `flag:FlagListDataBase.dat` additional unlock flag for page 4 (always -1) | low |
| 0x104 | `page4_flag4` | i32 | → `flag:FlagListDataBase.dat` additional unlock flag for page 4 (always -1) | low |
| 0x108 | `page4_flag5` | i32 | → `flag:FlagListDataBase.dat` additional unlock flag for page 4 (always -1) | low |
| 0x10C | `page5_flag1` | i32 | → `flag:FlagListDataBase.dat` unlock flag for encyclopedia page 5 (flag id, -1 = none). Slot 1 holds scenario flags (e.g. 101, 701,… | medium |
| 0x110 | `page5_flag2` | i32 | → `flag:FlagListDataBase.dat` additional unlock flag for page 5 (always -1) | low |
| 0x114 | `page5_flag3` | i32 | → `flag:FlagListDataBase.dat` additional unlock flag for page 5 (always -1) | low |
| 0x118 | `page5_flag4` | i32 | → `flag:FlagListDataBase.dat` additional unlock flag for page 5 (always -1) | low |
| 0x11C | `page5_flag5` | i32 | → `flag:FlagListDataBase.dat` additional unlock flag for page 5 (always -1) | low |
| 0x120 | `p1_line01 (×5 languages)` | str(84) | page 1, text line 1 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x2C4 | `p1_line02 (×5 languages)` | str(84) | page 1, text line 2 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x468 | `p1_line03 (×5 languages)` | str(84) | page 1, text line 3 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x60C | `p1_line04 (×5 languages)` | str(84) | page 1, text line 4 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x7B0 | `p1_line05 (×5 languages)` | str(84) | page 1, text line 5 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x954 | `p1_line06 (×5 languages)` | str(84) | page 1, text line 6 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0xAF8 | `p1_line07 (×5 languages)` | str(84) | page 1, text line 7 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0xC9C | `p1_line08 (×5 languages)` | str(84) | page 1, text line 8 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0xE40 | `p1_line09 (×5 languages)` | str(84) | page 1, text line 9 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0xFE4 | `p1_line10 (×5 languages)` | str(84) | page 1, text line 10 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x1188 | `p2_line01 (×5 languages)` | str(84) | page 2, text line 1 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x132C | `p2_line02 (×5 languages)` | str(84) | page 2, text line 2 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x14D0 | `p2_line03 (×5 languages)` | str(84) | page 2, text line 3 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x1674 | `p2_line04 (×5 languages)` | str(84) | page 2, text line 4 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x1818 | `p2_line05 (×5 languages)` | str(84) | page 2, text line 5 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x19BC | `p2_line06 (×5 languages)` | str(84) | page 2, text line 6 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x1B60 | `p2_line07 (×5 languages)` | str(84) | page 2, text line 7 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x1D04 | `p2_line08 (×5 languages)` | str(84) | page 2, text line 8 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x1EA8 | `p2_line09 (×5 languages)` | str(84) | page 2, text line 9 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x204C | `p2_line10 (×5 languages)` | str(84) | page 2, text line 10 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x21F0 | `p3_line01 (×5 languages)` | str(84) | page 3, text line 1 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x2394 | `p3_line02 (×5 languages)` | str(84) | page 3, text line 2 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x2538 | `p3_line03 (×5 languages)` | str(84) | page 3, text line 3 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x26DC | `p3_line04 (×5 languages)` | str(84) | page 3, text line 4 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x2880 | `p3_line05 (×5 languages)` | str(84) | page 3, text line 5 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x2A24 | `p3_line06 (×5 languages)` | str(84) | page 3, text line 6 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x2BC8 | `p3_line07 (×5 languages)` | str(84) | page 3, text line 7 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x2D6C | `p3_line08 (×5 languages)` | str(84) | page 3, text line 8 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x2F10 | `p3_line09 (×5 languages)` | str(84) | page 3, text line 9 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x30B4 | `p3_line10 (×5 languages)` | str(84) | page 3, text line 10 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x3258 | `p4_line01 (×5 languages)` | str(84) | page 4, text line 1 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x33FC | `p4_line02 (×5 languages)` | str(84) | page 4, text line 2 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x35A0 | `p4_line03 (×5 languages)` | str(84) | page 4, text line 3 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x3744 | `p4_line04 (×5 languages)` | str(84) | page 4, text line 4 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x38E8 | `p4_line05 (×5 languages)` | str(84) | page 4, text line 5 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x3A8C | `p4_line06 (×5 languages)` | str(84) | page 4, text line 6 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x3C30 | `p4_line07 (×5 languages)` | str(84) | page 4, text line 7 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x3DD4 | `p4_line08 (×5 languages)` | str(84) | page 4, text line 8 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x3F78 | `p4_line09 (×5 languages)` | str(84) | page 4, text line 9 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x411C | `p4_line10 (×5 languages)` | str(84) | page 4, text line 10 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x42C0 | `p5_line01 (×5 languages)` | str(84) | page 5, text line 1 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x4464 | `p5_line02 (×5 languages)` | str(84) | page 5, text line 2 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x4608 | `p5_line03 (×5 languages)` | str(84) | page 5, text line 3 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x47AC | `p5_line04 (×5 languages)` | str(84) | page 5, text line 4 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x4950 | `p5_line05 (×5 languages)` | str(84) | page 5, text line 5 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x4AF4 | `p5_line06 (×5 languages)` | str(84) | page 5, text line 6 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x4C98 | `p5_line07 (×5 languages)` | str(84) | page 5, text line 7 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x4E3C | `p5_line08 (×5 languages)` | str(84) | page 5, text line 8 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x4FE0 | `p5_line09 (×5 languages)` | str(84) | page 5, text line 9 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |
| 0x5184 | `p5_line10 (×5 languages)` | str(84) | page 5, text line 10 (lines are interleaved per language: JP,EN,FR,CHN,KOR) | high |

## Sources

D5 Mod Kit by eraser4521 (MIT): table schemas in `d5lib/schemas/`, described in [DATABASE.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/DATABASE.md).
