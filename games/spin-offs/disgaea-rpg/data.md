# Disgaea RPG — Data Tables

**Version:** JP Android client (October 2026). **Stored in:** 267 `masters/*` AssetBundles, each a `MonoBehaviour`
`DataList` with named fields. 261 have rows, 3 are empty and 3 are stubs.

**Evidence:** this project's extraction (UnityPy). Labels: **VERIFIED** = read directly from values; **PROBABLE** =
inferred by matching many rows to their own description text; **UNKNOWN** = numeric enum with no recoverable names.

**There are no evilities.** No table is named for them and the word エビリティ appears nowhere in the masters or text.
The closest thing is the **leader skill**: a per-character, party-wide passive.

## Main tables

| Master | Rows | What it is | Conf. |
|---|---:|---|---|
| `character` | 952 | Roster, stats, voice performer (`cv_name`), leader-skill IDs | VERIFIED |
| `command` | 5,562 | Every skill and spell: names, category, element, SP cost, effects, description | VERIFIED |
| `charactercommand` | 1,510 | Character → personal skill (1–3 each) | VERIFIED |
| `charactermagiccommand` | 4,170 | Character → magic / support skill, by weapon-mastery level | VERIFIED |
| `weapontypecommand` | 36 | Weapon type + mastery level → shared weapon skill | VERIFIED |
| `commandlevel`, `commandrank` | 50, 36 | Skill EXP curve; rank scaling | rows VERIFIED, meaning PROBABLE |
| `leaderskill` | 4,445 | Passives: trigger, target, effect, values, description | structure VERIFIED, enums UNKNOWN |
| `enemyleaderskill` | 3,715 | Same, for enemies | shape VERIFIED |
| `enemyaicommand` | 6,462 | Enemy → skill lists | VERIFIED |
| `memory`, `memoryeffecttype`, `memoryeffectvalue`, `memorylevel` | 165, 34, 298, 9 | Equippable memory cards; `memoryeffecttype` has format strings (`{0}のATK{1}`, `+{0}%`) — the cleanest effect vocabulary in the client | VERIFIED |
| `potentialkind`, `potentialkindrank` | 3, 2,250 | Stat-growth potentials | VERIFIED |
| `potentialclass`, `potentialclassrank`, `potentialclasscondition` | 6, 36, 108 | Advancement classes (King, Pawn, Rook, Bishop, Knight, Queen) | VERIFIED |
| `characterclassname` | 4,620 | Class titles per character and rarity | VERIFIED |
| `characterintroduction` | 644 | Roster blurbs | VERIFIED |
| `characterboost`, `boost` | 1,509, 34 | Timed event buffs | shape VERIFIED |
| `equipmenteffect*`, `weaponeffect*`, `innocent*` | 31–488 | Equipment and innocent effects | shape VERIFIED |

## `command` (skills)

| Field | Meaning | Conf. |
|---|---|---|
| `id` | `100001+` weapon skills, `200001+` shared magic, `3000000 + char×10 + n` character uniques, 1–512 generic | VERIFIED |
| `name`, `name_battle` | Names (battle banner with a line break) | VERIFIED |
| `description` | Flavour text | VERIFIED |
| `description_effect` | Effect summary with tokens: `ATK・DEF-#PER2#%(3T)[敵単]`; `#PERn#` = effect slot *n*'s value | format VERIFIED |
| `command_category` | 1 generic damage, 2 pure status/buff, 3 uniques and other damage | PROBABLE |
| `attr` | 0 none, 1 fire, 2 water, 3 wind, 4 star | PROBABLE |
| `command_types` | Effect type per slot (128 distinct; table below) | partly PROBABLE |
| `effect_values_min`, `_max_30`, `_max_40`, `_max_50` | Magnitude at level 1 and at caps 30/40/50 | VERIFIED |
| `effect_targets` | 1 enemy, 2 all enemies, 3 ally, 4 all allies, 5 self | PROBABLE |
| `trigger_types`, `trigger_type_params`, `trigger_targets` | Conditional activation (16 codes) | UNKNOWN |
| `sp_min`, `sp_max_30/40/50`, `gain_sp` | SP cost (goes **down** with level) and SP generated | VERIFIED |
| `max_lv`, `circle_flg` | Level cap; area flag | VERIFIED |

### Effect types decoded so far (26 of 128)

| ID | Effect | | ID | Effect |
|---:|---|---|---:|---|
| 1 | Damage | | 15 | INT down |
| 2 | HP heal | | 16 | RES down |
| 3 | Status cure | | 17 | SPD down |
| 4 | Poison | | 18 | HL steal |
| 5 | Paralysis | | 19 | Revive |
| 6 | Sleep | | 21 | Action gauge down |
| 7 | Forget | | 32 | SP restore |
| 8 | ATK up | | 44 | Max-HP-ratio attack |
| 9 | DEF up | | 63 | Crit rate up |
| 10 | INT up | | 65 | Crit damage up |
| 11 | RES up | | 101 | All damage dealt up |
| 12 | SPD up | | 123 | Overheal |
| 13 | ATK down | | 134 | Thorns |
| 14 | DEF down | | | |

## `leaderskill` (the passive layer)

Character *N*'s leader skill is usually ID `N × 10 + 1` (Laharl 1 → 11 「魔王の威厳」: party's non-HP base
stats +X%). Fields: `timing_type` (27 values), `trigger_*`, `target_type`, `effect_type` (83 values),
`effect_value_min/max`, `limit_*`, `bad_effect_*`, and a readable description on 4,439 of 4,445 rows. 248 characters
have none (mostly generics and story-only IDs). `character` links them through `m_leader_skill_id` (main) and
`m_leader_skill_id_sub_1..3` (learned later), each with an optional `additional_*` continuation row.

**Field values (2026-11), decoded from the rows' own Japanese descriptions** (the IL2CPP metadata is encrypted, so there
are no enum names; **Verified offline** against the text, not against client code):

| Field | Values |
|---|---|
| `effect_type` | 1 stat percent, 2 enemy stat percent down, 3 damage dealt, 4 damage taken, 9 enemy heal amount percent down, 14 ailments nullified, 21 HP heal percent, 22 SP gain (points), 23 / 24 timed stat buff / enemy debuff, 48 auto revive (HP 100 %), 49 / 50 timed dealt / taken buff, 79 intercept, 90 invincible for N hits; 17, 18, 74 action gauge |
| `effect_type_params` | For 1 / 2 / 23 / 24 the stat: 1 HP, 2 ATK, 3 DEF, 4 INT, 5 RES, 6 SPD, 7 CRT, 8 CRD. For 3 / 4 / 49 / 50 the damage kind: 1 all, 2 normal attack, 3 skill, 5 magic, 7 non-elemental, 8 elemental, 9 fire, 10 water, 11 wind, 12 star, 13 single-target, 14 all-target |
| `target_type` | 1 party, 2 party females, 3 party humanoid (1) / monster (2), 4 party by equipped weapon, 8 party except self, 9 self, 23 self while equipped, 11 all enemies, 12 enemy males, 13 enemy humanoid / monster, 17–20 by action-gauge order, 26 potential type (`target_params` lists the types), 28–30 highest / lowest stat unit, 33–36 by HP / buff state, 99 rewards |
| `timing_type` | 1 always (battle start for timed effects), 2 turn start, 3 turn end, 4 turn end after a kill, 5 wave start, 6 when attacked, 7 on own defeat, 11 party damaged, 12–15 party uses a fire / water / wind / star skill, 16 party critical |
| `trigger_*` | (4, 12) all of the party's favourite weapon types differ; (7, 16, p) an enemy has HP at most p %; (1, 3, w) holder has weapon type w; (1, 15, p) HP at least p %; (1, 16, p) HP at most p %; (1, 18, p) HP below p %; (2, 21, kind, n) party has n humanoids / monsters; (6, 5, n) n party members alive |
| `limit_damage` | The other side: 1 male, 2 female, 3 humanoid, 4 monster, 6–13 by its weapon |
| Weapon types | 1 sword, 2 fist, 3 spear, 4 bow, 5 gun, 6 axe, 7 staff, 8 monster physical, 9 monster magic (Disgaea 5's `item.dat` category is 1 fist, 2 sword, then the same 3–9) |

**Skills (`command`, `charactercommand`, `charactermagiccommand`).** The game has no map: a command effect's target is
one enemy, all enemies, one ally, all allies or self (`effect_targets` 1–5); there is no range, area, cooldown or charge
gauge. Cost is SP (`sp_min`, falling to `sp_max_50`). `relational_param` 2 = ATK-based, 4 = INT-based.
`charactercommand.lv` is a character level (1 / 50 / 200); `charactermagiccommand.weapon_mastery_lv` is a weapon
mastery level (4, 9, 15, 22, 30, 40).

**More leader-skill facts (2026-11-12 to 11-14, Verified offline from the JP masters and the `help` master; not tested in the client):**

- **Potential type and favourite weapon are per-character fields.** `character.m_potential_class_id` holds the potential
  type (`potentialclass`: 1 King, 2 Pawn, 3 Rook, 4 Bishop, 5 Knight, 6 Queen) and `best_weapon_type` the favourite
  weapon (1–9, as in the weapon-type row above). Across the 235 looks (146 base looks and 89 costume looks; a costume
  inherits its owner's values) the potential types split King 27, Pawn 37, Rook 36, Bishop 42, Knight 40, Queen 53.
- **Party conditions:** several passives apply only to party members of a stated potential type (target 26), and three
  apply only while no two party members share a favourite weapon type (trigger (4, 12)); with 10 or more members that
  can never hold, since there are 9 weapon types.
- **Invincible N hits (effect 90, timing 1):** the next N hits deal no damage; other effects of those hits still
  apply, a multi-strike combo counts as one hit, and it does not stack (`help` master). Whether poison, falls or field
  damage use it up is **Unknown**.
- **Auto revive (effect 48, value 100, timing 8):** revives at full HP, taking effect after the revived unit's own turn
  ends (`help` master). Where the unit reappears isn't stated.
- **Heal-amount modifier (effect 9, target 11, timing 5):** the master has no wording separating "healing received"
  from "healing given", only "enemies' healing amount". Timing 5 (wave start) reads as always on in D5.

## Class titles (English names)

`characterclassname` holds a title per character and star rarity, and `character` holds up to four per-rarity title
slots (`class_name_1`–`class_name_4`; the slot for a character's base rarity is 3 for four-star and 2 for three-star
base characters). The JP client carries Japanese text only. **Community-reported:** the community
[Disgaea RPG Wiki](https://disgaea-rpg.fandom.com/) (Fandom, readable through its MediaWiki API; the HTML pages sit
behind a bot check) prints English titles in each character's infobox and keeps them in one template
(`Template:Enum/JP/Titles`, edited by ArctiaBot; read 2026-11-19). Cross-checked against an archived Global-client
master (489 rows), 9 English titles are documented by the wiki with the same Japanese slot (7 verbatim; 2 where the wiki's
rarity slot is the only independent evidence), 2 titles are named differently by the wiki and the archive, and the
other 135 characters have no documented English title in the sources checked (81 have no row in the archive, 34 have
empty name columns, 18 carry another title set). The wiki covers many more characters than the archive; a full sweep
wasn't done.

## Passives in Disgaea 5

Disgaea 5 has few map-wide evilities, so the D5 Mod Kit development fork carries each character's
leader skills as D5 evilities with its own aura and condition rows. **Verified offline** (the kit's emulated proofs, not
the game), 2026-11-12 to 11-14: 129 of the 143 non-sample characters first got their main passive as an own
evility (369 evilities in all; 16 characters have 1, 47 have 2, 61 have 3, 19 have 4). Of the other 14 mains, 13 were
built later (9 from the potential-type and favourite-weapon conditions above, 4 as adapted rules: enemy heal amount,
"an enemy at 50% HP" damage buff, N-hit invincibility, once-per-battle revive); one (Piyori XENO's intercept) has no D5
counterpart. Source numbers stack: ten "+15% party ATK" carriers give +150%. **Verified in game** (in D5, exe f4878c29,
2026-11-12, test save at level 1): the aura raised a unit's ATK from 15 to 22 with three other carriers on the map; the
mains were present on 4 of 6 test units and **absent on 2, cause not diagnosed**.

## Voices

`character.cv_name` (the performer, 825 of 952 filled) is the only voice field in any table. No table maps a
character to a voice bank, and none is needed: **the bank's folder name is the character master ID** (948 of 952 IDs
have one; a costume ID has its own). A `cv_name` of "-" marks a character whose bank holds only a silent placeholder.
Bank layout and cue names: [assets.md](assets.md).

## Open questions
- The other 102 `command` effect types; the remaining `leaderskill` values. The IL2CPP metadata is encrypted.
- How the Global client differs.

## Sources
This project's Disgaea RPG table extraction report (2026-10-27).
2026-11 additions (`leaderskill` values, skill tables, voice folders): D5 Mod Kit development fork `docs/EVILITIES.md`, `docs/IMPORT_GATES.md`, `docs/SOURCE_VOICES.md`.
2026-11-12 to 11-20 additions (potential type and weapon conditions, more effect codes, class titles, passives in Disgaea 5): the D5 Mod Kit development fork's offline reads of the JP masters, its kit docs, and the community wiki cited above.
