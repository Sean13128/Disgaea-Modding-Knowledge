# Disgaea 7 — Data Tables

**Version:** Disgaea 7: Vows of the Virtueless, PC (Steam app 2250600, build 13254462). **Stored in:** `data.dat` at
the install root.

**How to read and edit them:** [Makai Kit](https://github.com/HybridEidolon/makaikit) (HybridEidolon) defines
records as Rust structs (`databases-d7`) and unpacks tables to JSON; its D7 mod loader repacks tables from mods' JSON
at startup. So in D7 you work with **named fields** in JSON, not byte offsets. Field names below are Makai Kit's.

**Which tables:** the public GitHub source (last commit 2023-11-01) defines and repacks **37 tables** (listed below).
`data.dat` holds more tables than that; their names and fields aren't documented here yet (**Unknown**).

**On disk** (Makai Kit's `databases-serde`; **Verified offline**, source read and this project's readers): each table is
a member `data/database/<name>.dat`, usually `YKCMP_V1`-compressed. Decoded: `u32` record count, then each record as
`u32` byte size + the record. Inside a record, fields are little-endian in struct order; a string is a `u32` length
counting its terminating NUL (0 = empty) then UTF-8; a list is a `u32` count then its elements. Records are therefore
variable-length: you can't seek to row *n* without walking the ones before it. Makai Kit sorts records by ID when it
writes a table.

**Evidence:** table list and field names from Makai Kit's public structs. **Community-reported.**

## Tables in the public loader

`act`, `acteffect`, `actfeature`, `actlearn`, `actmap`, `ai`, `aiparts`, `anime`, `animebank`, `archive`, `area`,
`battleflag`, `bgm`, `bu`, `characlass`, `character`, `charafeature`, `charazukan`, `cheatsetting`, `doping`, `drink`,
`dungeon`, `evility`, `hospitalgacha`, `innocent`, `innocentaffinity`, `item`, `itemcustom`, `itemfeature`,
`itemshoplineup`, `itemstrengthen`, `itemtype`, `job`, `stage`, `string`, `wish`.

Text fields hold six languages.

## `character`

| Section | Fields |
|---|---|
| Identity | `enumName`, `name`, `tribalName`, `refID`, `classID`, `jobID`, `jobRank`, `charaRank`, `sex`, `looks`, `feature`, `featureAdd`, `personal`, `helpID`, `helpIndex` |
| Base stats | `baseHP`, `baseSP`, `baseParam` (stats), `equipRateParam` (aptitudes) |
| Combat | `moveRange`, `moveType`, `jump`, `attackRange`, `counter`, `throwRange`, `critical`, `attributeRate` (Fire, Wind, Ice, Star), `weaponResist`, `weaponMastarly` (sic), `goodWeapon`, `weaponHandType` |
| Evilities and skills | `uniqueEvilityID`, `evility[]`, `normalAttack`, `normalAttack_Giant`, `attack_Giant`, `act[]`, `actAdd[]`, `giantEffectID`, `makaiJingiID` |
| Growth and rewards | `levelUpNeedExpCorrect`, `expCoefficient`, `moneyCoefficient`, `manaCoefficient`, `jobCoefficient`, `togatherRate` |
| Appearance | `modelID`, `colorID`, `partsColorID`, `exColorID`, `scale2D`, `windowFaceID`, `cutInID`, `width`, `height`, `cellSize`, `cellType`, `hitRadius`, `activateRadius`, `isPileUp` |
| Voice and scripts | `voiceID`, `talkID`, `deadEffectScriptModule`, `deadEffectScriptEntry` |

Enum meanings for `moveType`, `looks` and `weaponHandType` are still guesses.

## `evility`

`name`, `help`, `icon`, `sortID`, `cost` (slots), `mana`, `type` (list categories), flags `isUnique`, `isShop`,
`isEnemyOnly`, `isNotExport`, `notRankBattle`, `condOnFlag` / `condOffFlag` (story flags), and `effect[]`: the effect
blocks (`EffectInfo` in Makai Kit's struct). What each effect, value and condition code means isn't documented here yet.

## `act` (skills)

`type`, `attackType`, `depend` (power stat), `attribute` (element), `weaponType`, `targetType`, `refID`, `actMap`
(area), `iconID`, `helpText`, `requiredFlag`, `validFeature` / `invalidFeature`, `effect[]` (→ `acteffect`).
A skill's animation and logic are Lua scripts (`data/script/*.lua`); see [modifiable.md](modifiable.md).

## `item`

`help`, `refID`, `type` (→ `itemtype`), `iconID`, `feature`, `baseHP`, `baseSP`, `baseParam`, `attributeRate`,
`moveRange`, `moveType`, `critical`, `price`, `actID` (granted skill), `useItemActID`, `modelID`,
`forceWeaponHandType`.

## `stage`

`refID` (copied from), `type`, `mapID`, `mapType`, `areaID`, `stageMission`, `bgmNo`, `texID` (1920 × 1080 preview
still), `conditionText`, `startDemoNo`, `endEventNo`, `loseEventNo`, `startPos` (base panel), `goalPos`,
`sortieDirection`, `sortieStock` (max deployed), `placementBattleID` (enemy group), `placementEnemyBasePanelID`,
`placementTownID`, `geoPanelID` (32 × 32 colour grid), story-flag requirements and results, and victory / defeat
condition lists.

## Open questions

- Field meanings for most smaller tables.
- `sortieDirection` and the tables outside the public loader.

## Sources

- [Makai Kit](https://github.com/HybridEidolon/makaikit) by HybridEidolon (GPL-3.0): `databases-d7`.
