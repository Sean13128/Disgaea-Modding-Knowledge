# Disgaea Mayhem — Data Tables

**Evidence:** **Community-reported**, from [triforce-disgaea](https://github.com/triforce-heroes/triforce-disgaea)
(triforce-heroes, Apache-2.0, first commit 2026-10-12): a TypeScript library that extracts and rebuilds the text in
Disgaea Mayhem's database tables, for translation. Its test data names Mayhem's protagonist (N.A., "Lone
Mercenary") and carries Spanish text, which the earlier games don't ship. It doesn't say which platform's files it
was built against or which archive holds the tables. No copy has been scanned for this project.

## Record encoding

The same family as Disgaea 6 and 7 ([D7 data.md](../../mainline/disgaea-7-vows-of-the-virtueless/data.md)):

- A table is `u32` record count, then each record as `u32` byte size + record.
- A string is a `u32` length that counts its terminating NUL, then UTF-8.
- **Text comes in six languages, in this order:** Japanese, English, French, **Spanish**, Traditional Chinese, Korean
  (D6 and D7 have Simplified Chinese where Mayhem has Spanish).
- A "dialog" field is a `u32` line count, then for each line one string per language.
- Most records start with a `u32` ID and a **key** string, an enum-style name such as `CHARA_PLAYER_0001_00`.
  Some tables (`vkey`, `framework_vkey`, `randomName`) are keyed by number instead.
- Non-text fields are kept as raw bytes by the tool; their meanings aren't documented there.

## Tables (49)

Grouped by topic. In brackets: what each record holds besides its ID and key, as the tool reads it.

| Topic | Tables |
|---|---|
| Characters | `character` (two name fields), `nonPlayerCharacter`, `demoCharacter`, `characterZukan` (encyclopedia), `senator`, `randomName` |
| Character World | `characterWorld_Act`, `_BattleLog`, `_CellType`, `_Character`, `_Difficulty`, `_EventType`, `_Rewards` |
| Combat | `act`, `act_Enemy` (actions: name, description), `evility` (three text fields + description), `cmd`, `framework_cmd` |
| Items | `item`, `itemType`, `itemStrengthen`, `itemWorldRewards`, `innocent` |
| World and progress | `area`, `stage`, `mission`, `missionList`, `taskList`, `wish` (bills), `record`, `trophy`, `eventViewer`, `DLC_delivery` |
| Text and UI | `string`, `framework_string`, `notice`, `question`, `tutorial` (pages of dialog), `gameSetting`, `cheatSetting`, `vkey`, `vkeyCategory`, `framework_vkey`, `framework_vkeyCategory` |
| Media | `bgm` (two file-name strings), `musicZukan`, `modelSequenceType` |
| Scripts | `talk`: two `u32` lists, then commands. Each command: `u32` opcode, two `u32` lists, two strings, 40 bytes; opcode 2 is a dialog line (with a 4-byte suffix per line), the others carry choice text |
| — | `dummy` |

The `characterWorld_*` tables suggest a Character World feature like Disgaea 5's; the `wish` and `senator` tables
suggest a Dark Assembly. Neither is confirmed in game.

## Open questions
- Which archive and folder hold these tables on PC.
- Every non-text field.

## Sources
[triforce-disgaea](https://github.com/triforce-heroes/triforce-disgaea) source (`src/types/Database.ts`, `src/Extract.ts`)
and test snapshots, read 2026-11-04; [Gematsu: Disgaea Mayhem](https://www.gematsu.com/games/disgaea-mayhem) (protagonist).
