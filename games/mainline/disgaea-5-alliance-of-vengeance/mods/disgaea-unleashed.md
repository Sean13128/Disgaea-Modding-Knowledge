# Disgaea Unleashed

- **Author:** ninakoru · **Game:** Disgaea 5 Complete, Steam
- **Get it:** the author's Steam guide ([link](https://steamcommunity.com/sharedfiles/filedetails/?id=2926099213)), now removed from public view · **License:** none stated

This page documents what the mod does and how it interacts with the game. It does not reproduce the mod.

## What it does
- Clearer evility descriptions in English (for example PWR instead of ATK where that's the stat affected).
- Higher caps: most Innocents 50,000 → 200,000; stats 99,999,999 → 199,999,999.
- Small power-ups for weaker evilities and lower slot costs for many.
- Item stat changes (for example Revenge weapons get stats in every attribute).

## How it loads
No loader. It replaces two game files: `disgaea5.exe` in the game folder and `Data/Database/Database_0.dat`.
Uninstall with Steam's *Verify integrity of game files*.

## What it touches
| Part of the game | How | Details |
|---|---|---|
| Executable | Replaced whole | Where the caps are enforced. Which patches it made isn't documented |
| `Database_0.dat` | Replaced whole | Evility (`Skill.dat`) and item (`item.dat`) rows; English text |

## Compatibility
- Overwrites any other mod's changes to the executable or the database.
- A replaced executable is tied to one game build.
- The author says some values were edited with "third party tools", not named.

## Lessons for modders
- Stat and Innocent caps are in the executable, not the tables.
- Shipping whole game files is fragile and carries risk: the guide that distributed this mod was removed for breaking
  Steam's guidelines (the exact reason isn't stated). Patch-based installers avoid both problems.

## Sources
- [Steam guide](https://steamcommunity.com/sharedfiles/filedetails/?id=2926099213) (page content as seen 2026-11-04). **Community-reported.**
