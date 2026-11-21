# D5 Mod Kit: Outfit System and bundled outfits

- **Author:** eraser4521 · **Game:** Disgaea 5 Complete PC (both builds)
- **Get it:** [D5 Mod Kit releases](https://github.com/eraser4521/D5ModKit) (the outfits ship in the release zip, not the repository) · **License:** MIT (Kit code)

This page documents what the mods do and how they interact with the game. It does not reproduce them.

## What it does
Adds an **Outfit** command for unique characters: Characters → a character → Outfit → *Default* or a named outfit.
The Kit ships 175 outfits for 26 characters (most drawn from Disgaea RPG) and a new playable character, Thunderlady.
Every outfit mod requires the base mod **Outfit_System**.

## How it loads
Installed by D5 Mod Kit: archive overlays plus executable patches in new code sections. Players can get it as one
installer `.exe` the Kit exports.

## What it touches

| Part of the game | How | Details |
|---|---|---|
| `char.dat` | Rows | An outfit is a **copy of the character's row** with its own look (`id`, `sprite_id`, `color_id`). At install, every other field is re-copied from the character, so other mods' changes (stats, skills, evilities) apply in any outfit. Uses the empty "Unique Dummy" rows (470, 480, 490, 950–990) first, then **inserts** rows 471–479, 481–489, 491–497 |
| Saves | Runtime fix | Inserted rows shift `char.dat` row indices, which saves cache. The Kit's runtime re-runs the game's own fix routine (`0x14015EB80`) for any unit whose stored row no longer matches its class. Units with more than three looks keep their colours in `d5looks.bin` |
| Sprites, faces, portraits | Archive overlay | `anmN` (sprite), `anmN+2` (standing picture), `anm(10000+N)` (portrait / cut-in); faces in the face atlas (enlarged as needed); list icons in an enlarged icon atlas |
| Unit menu | Exe patch | `0x140263875` adds *Outfit*; `0x140263432` handles the choice (sets the class, refreshes the unit and sprites) |
| Party preview, status, battle face | Exe patch | `0x14067E7D1`, `0x1402AE881`, `0x14043088E`, `0x14021E9D0` |
| "Is this unit character X?" checks | Exe patch (95 sites) | The game recognises story characters by class ID. An outfit changes the class ID, so without this a check like "find Killia" failed and **the game crashed after a won battle**. The patch makes these checks see an outfit as its character |
| Sprite scale | Exe patch | The game draws animation blocks 10000–19999 at two-thirds size (meant for portraits); the Kit exempts its own sprites numbered 10000+ |
| Skill scripts, event poses | Exe patch | Scripts see an outfit's sprite as its character's; event poses use the outfit's look |

Addresses are for the October 2018 build; the Kit translates them for the Steam update at install.

## Own values and the title bug (2026-11-18)

**Own values.** An outfit can keep values of its own for whole groups instead of following its character: evilities,
base stats, aptitudes, weapon mastery, movement (move, jump, counter, throw, crit), elemental resistances and weapon
resistances. Without them an outfit follows the character as described above. An outfit with no own values behaves
exactly as before.

The game keeps its own copies of a class row's values in the unit record, filled when the unit is made (`0x14021B510`,
stats `0x140218590`), ranks up (`0x14021EEE0`) or reincarnates (innate evilities, `0x140219C40`). A plain switch only
writes the class ID and refreshes the row index (`0x14015F930`), so it changed nothing in those copies. The Kit's
switch now adds (new row − old row) to the unit's copy for every group either outfit owns, then runs the game's
recalculation (`0x14021BFB0`). Switching back adds the opposite difference. Unit-record fields:

| Group | Unit field | Rule |
|---|---|---|
| Growth and base stats | growth bytes `+0x2DA0` (u8, clamp 1–255); base stats `+0x2D18` (i64, + difference × (level+1)/2) | the rank-up's own clamp |
| Aptitudes | `+0x3078` (u16, clamp 0–65,535) | |
| Weapon mastery | `+0x2DAE`, 9 weapons (u8, clamp 0–127) | |
| Movement | move `+0x2E1C`, jump `+0x2E1A`, counter `+0x2E1E`, two more from row `+0x37D` / `+0x380` into `+0x2E20` / `+0x2E22` (u8) | |
| Resistances | `+0x2E12`…`+0x2E15` (i8) | |
| Innate evilities | five u16 from `+0x2EEE`, count `+0x2EF8`, 9999 = empty | an outfit's own evility takes the slot of the character's it replaces; learned evilities are never touched |
| Weapon resistances | none: read from the current row at every damage calculation (`0x14015C490`) | changes at once |

**Verified in game** (D5 Mod Kit development fork, modified executable `f9b6551b…`, 2026-11-18): Killia (level 9999) in an
outfit with own values, switched from the Characters menu. Read from the unit record and the Status screen: ATK growth
52 → 92 and ATK 36,079,158 → 36,339,158, INT aptitude 100 → 160, Gun and Staff mastery C → S, move 5 → 8, fire
resistance −25 → 50, sword weapon resistance 20 → 60, and her unique evility changed. Back to Default, every group
returned exactly; the only difference was six sword skills (11161–11166) that the recompute grants from her unchanged
sword level, which any battle or level-up would grant too.

A save made while wearing such an outfit loaded with every group intact (the save stores the unit record whole, and
the load's recompute does not re-derive these from the row).

**Limits.** A clamp that bites is not undone on the way back. Base stats move by (level+1)/2 × difference at the level
of the switch, so a unit that levels up in the outfit returns with its own gains. Own values are compared against the
current install: switch units back to their normal look before changing an outfit's own values or removing its pack.

**Title lost on load.** A unit's title text (`+0xBFC`, 0x44 bytes) is not stored: every load, base entry, New Game+ and
battle start copies it from the `char.dat` row and then `0x1401C3A90` replaces it for some class IDs, such as a
story character's earned title. An outfit has its own class ID, so the replacement fell through and a unit saved in an
outfit came back with the plain row title. The Kit now maps the class ID given to that routine through the outfit
table, like the 95 identity sites. **Verified in game** (modified executable `b4dd297e…`, 2026-11-18): a save holding
Killia in an own-values outfit loaded with her earned story title, and kept it at the base and in the character list;
before the fix it showed the row title. An outfit's own row title shows only for characters with no story title.

## Compatibility
- Other mods that change a character's row: compatible (outfits follow the character, except for groups an outfit keeps its own values for).
- Other mods that insert `char.dat` rows or change the same code sites: likely to conflict.
- Uninstalling while a saved unit wears an outfit: the unit expects the outfit's row to exist. Keep a copy of saves; the Kit has a save-check tool.

## Idle timing of Disgaea RPG costumes
Costumes converted from Disgaea RPG used to borrow the base character's idle clock, which skipped or held source
poses and made costumes standing together look too fast. An optional override mod (*RPG Original Idle Timing*) rebuilds
the costumes' idle loops (6001 / 6007) from the RPG animation clips' own pose order, transition ticks and loop length,
moving attached pieces (hands, equipment) on the same clock. It's generated from the player's own Disgaea RPG files and
leaves the original mods, palettes and textures unchanged; the Kit reports 179 costumes rebuilt and checked in game.

## Lessons for modders
- Class ID doubles as identity in D5: changing a unit's class breaks story-character checks unless they're patched.
- Inserting table rows is possible if you fix cached row indices at runtime, but appending is far safer.

## Sources
D5 Mod Kit [OUTFITS.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/OUTFITS.md), [RPG_IDLE_TIMING.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/RPG_IDLE_TIMING.md), [README](https://github.com/eraser4521/D5ModKit). **Community-reported** (the Kit reports in-game tests for the crash fix and outfit switching). The 2026-11-18 section is from this project's live tests of the development fork.
