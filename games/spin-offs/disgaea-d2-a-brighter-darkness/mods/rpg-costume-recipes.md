# Disgaea RPG costume recipes (D2 native port)

- **Author:** Sean13128 · **Game:** Disgaea D2, through the [native port](https://github.com/Sean13128/disgaea-d2-recomp) (BLUS31313, 1.40)
- **Get it:** `costume-recipes/*.d2costume.json` in the port's repository · **License:** MIT

This page documents what the feature does and how it interacts with the game.

## What it does
Brings Disgaea RPG outfits into D2 as selectable appearances. A **recipe** names which RPG costume becomes which D2
appearance, with its display name, Status illustration, crops and Choose Color slot, plus SHA-256 fingerprints of the
source files. It contains **no artwork**: each player rebuilds the costume from their own D2 dump and their own RPG
files (`tools/costume import <recipe>`, then `tools/costume play`).

## How it loads
Only in the native port. The port adds overlay sprite data alongside the game's `ANM_HI` archives and hooks the
game's actor construction to pick the chosen appearance.

## What it touches

| Part of the game | How |
|---|---|
| Sprites | New bodies built from RPG atlases, encoded in D2's anm format and checked with the game's own decoder |
| Faces | The face atlas is widened and a row appended; the port routes the game to it (the game's fixed face tables don't find new rows) |
| Actors | Hooks on the actor constructor (and on the temporary actors skills spawn) select the costume's resource |
| Castle menu | *Game → Appearance* switches costumes live in the castle; choices persist across launches |
| Saves | Unchanged: tests checked save files stayed byte-identical |

## Status (Oct 2026)
**Verified in game** for Etna (two outfits): idle, movement, equipment screens, the Prinny Raid skill, castle switching,
and carrying a costume into battle. Other characters, full action sets, portraits and battle-time switching aren't done.

## Lessons for modders
- Shareable recipes sidestep redistributing game art.
- In D2, appended face rows need code routing; data alone isn't enough.

## Sources
The native port's `costume-recipes/README.md` and importer reports.
