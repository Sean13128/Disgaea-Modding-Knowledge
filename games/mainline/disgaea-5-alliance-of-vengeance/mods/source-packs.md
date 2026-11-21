# Source packs: classes and characters from other Disgaea games

- **Author:** Sean13128 (with AI agents), built on D5 Mod Kit · **Game:** Disgaea 5 Complete PC (Steam build)
- **Status (October 2026):** Disgaea 4 pack installed and playtested; Disgaea 1 Complete, Disgaea D2 and Disgaea RPG
  packs built, partly installed · **License:** the tools are MIT; packs are built from each user's own game copies

This page documents what the packs do and how they interact with the game. It does not reproduce them.

## What they do

| Source | Built | State |
|---|---|---|
| **Disgaea 4** | 45 generic classes (270 ranks) and 26 unique characters, plus voices | Installed and playtested 2026-10 |
| **Disgaea 1 Complete** | 13 classes and 22 unique characters | Built, not installed |
| **Disgaea D2** | One class (Shark Dragon) and one monster (Xenolith) as a test | Installed, not confirmed |
| **Disgaea RPG** | Costumes for 25 characters | Installed (looks only) |

## How they load
As D5 Mod Kit mods: archive overlays and the Kit's executable patches. The class and evility extensions come from the
Kit's development fork.

## What they touch

| Part of the game | How |
|---|---|
| `char.dat`, `JobRank.dat` | Six rank rows per class with the source game's stored stats, aptitudes, titles, movement, jump, throw, counter, crit and elemental resistances. Weapon mastery, weapon resistances and animation timing come from a D5 template class (D4 has no per-class equivalents). Star resistance 0 (D4 has no Star element) |
| Class IDs | Reserved "Dummy" families first, then new families outside the game's own ranges, with the inlined "is generic" tests widened ([D5 modifiable](../modifiable.md#generic-classes)) |
| Unlock flags | `17283 + 6 × family index + rank`, in the game's class-unlock block |
| Class mastery | Spare mastery slots 47–52 in the unit record (six classes at a time), leased per family for good |
| `Skill.dat` (evilities) | New rows owned by the class. The source's innate evility is built in; rank evilities become **character-type** Skill Shop entries (`source = 4`, since `source = 3` crashed the shop for classes without mastery). Each new evility **works like** a mapped D5 evility with D5's numbers, labelled exact / adapted / lookalike |
| `magic.dat` (skills) | New rows copied from the mapped D5 skill and its animation, learned at the source game's levels |
| Sprites, faces, cut-ins | Source sprites converted to D5's anm format and fitted into the template's animations; ten palettes per class from the source; white backgrounds made transparent |
| Voices | D4 lines converted to D5's format; six D4 situations mapped so far |

## Problems found and fixed
These became general rules on [D5 modifiable](../modifiable.md#crash-causes-found-in-practice):
- A stray `%` in an evility description crashed the Skill Shop (descriptions are printf formats).
- A long class description froze the Recruiter (fixed 256-byte field).
- Male classes given female vanilla voice sets spoke with female voices.
- Class titles over 18 characters overran their box.
- Imported classes and Overlord's both claimed unlock flags 17283+, mastery slots 47–52 and rows 1350–1355.

## Lessons for modders
- Stats can be copied faithfully; evilities and skill animations mostly can't, because D5's evility behavior and skill
  scenes are code or game-specific scripts. Label every adaptation.
- Most hard work was in the art: fitting another game's complete poses into D5's multi-part animations.

## Sources
This project's prior-art map (2026-10-26), D4 → D5 porting handoff, playtest triage reports; D5 Mod Kit development fork
`docs/D4_IMPORT.md`, `docs/CLASSES.md`, `docs/MASTERY.md`.
