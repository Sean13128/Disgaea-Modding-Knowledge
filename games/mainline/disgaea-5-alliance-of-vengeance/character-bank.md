# Disgaea 5: moving units between saves (the character bank)

How a unit travels from one save to another, which game rules limit it, and what was seen in game. The tool is the D5
Mod Kit development fork's character bank (a command line and a page); this page records the game facts it relies on.
Addresses are build **B** ([functions](functions.md#which-build-an-address-belongs-to)).

## Why a bank exists

A save holds **128 characters** in total (army + reserve): the unit creator `0x1402EA3E0` refuses beyond that
([functions](functions.md#where-the-128-unit-cap-lives)). Units on the story's own rows cannot be dismissed, and neither
can mod-added units on rows below ID 1000 ([functions](functions.md#army-size-and-dismiss)). A bank is a shelf for units
that do not fit, and the way to remove such a unit from a save.

## What a bank file holds

One unit is one `0x4750`-byte roster record (**Verified offline**: 301 units round-tripped inside one save and across
saves, 0 failures).

| Part | What happens |
|---|---|
| Carried as it is | The five equipped items' links, name, title, level, class, skill list (`+0xCC0`, 16 bytes each), weapon mastery, the 125-byte learned-evility bitmap, innate evilities, sub-class `+0x35AC`, class-mastery counters |
| Re-addressed on import | Serial `+0x3060` (a fresh one from the save's counter), roster index `+0x2E31` (end of the army), `char.dat` row index `+0x2E0A` (56 of 301 differed: a save may hold the vanilla table index, the import writes the installed table's) |
| Dropped on import | Squad and group membership (26 of 301 units had a group) |
| Not carried, nothing refuses it | The New Game+ / base cast table (64 serials in the state at `+0x8810A0`, written by `0x1406A5950`; readers skip a serial they cannot find); a sub-class or graphics ID (`+0x2E36`) that the target `char.dat` lacks (only the main class is checked) |

Learned evilities of mod units live in a companion file in the Kit (`<save>.d5learn.json`), keyed by serial and bound to
the save's size and SHA-256; a stale companion is ignored, never paired silently.

## Refusals

An import changes nothing and refuses when the unit's class has no row in the installed `char.dat`, when army + reserve
is already 128, or when the unit is a story-locked row. The story lock is the game's own Dismiss rule (`0x140654F20`,
see [functions](functions.md#army-size-and-dismiss)); it locks 56 vanilla rows. Reserve units are refused on export by
the tool (story hand-overs live there). An import also refuses a unit the save already holds, unless told to allow a
duplicate.

## Gear is never duplicated

A bank file carries no items. When a unit is moved out, its five equipped items go to the source save's bag the way the
Equip menu's remove does (`0x14027BC80` → add `0x1401C9BA0`, then `0x1401C9B70` empties the slot). The item store is one
list for bag and warehouse: 2,000 blocks of `0x258` bytes at state `+0x482FD8`, a u16 count at `+0x5A7F58`. A block
carries its innocents and Item World index and names no owner, so it is copied as it is (**From decomp**). If the bag
cannot take every item, the export is refused. Whether an item with the "cannot remove" byte (`item.dat +0x458`) is
moved too was not checked.

## DLC guests

Dismissing a DLC character also clears its "recruited" game flag (class ID below 1000 maps to flag 18300–18353, read by
the guest room `0x140142580`; routine `0x140652580`). A move does the same and an import sets it, since every DLC
character in the army had it set in the four test saves (**Verified offline**).

## Writing to a real save

The Kit's bank copies the save and its companion to a backup folder before it writes a save in the game's folder,
refuses while the game runs, reads the written save back and checks it, and puts the backup back on a mismatch. A bank
file is created exclusively (a name clash gets `-1`, `-2`), a partial file is removed, and a failed move never leaves a
unit in both places. These are tool rules (**Verified offline**, failure-injection tests), not game rules.

## Seen in game

Two saves written by the bank were loaded and played (**Verified in game**, D5 Mod Kit development fork, modified
executable, 2026-11-13): no crash, no repair, no blank roster entry; imported records were byte-identical to the save
file; faces of imported-game classes showed; the game re-pointed the vanilla `char.dat` index of its own native units on
load; a unit hired afterwards got the next serial (12285). The Recruiter's Dismiss list did not offer the imported
low-ID units (see [functions](functions.md#army-size-and-dismiss)).

**Unknown:** a save and reload inside the game of a bank-written save, an attack by an imported-game unit, the base
walker and followers, squad arrangement, and the use of a state table at `+0x881098` for imported uniques.

## Sources

D5 Mod Kit development fork: `docs/CHARACTER_BANK.md`; this project's offline round-trip tests and the live load of
2026-11-13.
