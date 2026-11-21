# disgaea5autoplay

- **Author:** Grid487 · **Game:** Disgaea 5 Complete, Steam, exe SHA-256 `49A7530BAA1C3E15E4BF9C63C56FEFD71D7E51A6E641BCE80280B37B2DB282CE`
- **Get it:** [GitHub](https://github.com/Grid487/disgaea5autoplay) · **License:** MIT (the mod's code)

This page documents what the mod does and how it interacts with the game. It does not reproduce the mod.

## What it does
- **F1:** automates combat.
- **F2:** repeats supported stages.
- **F3:** picks and orders a custom party of up to 10.

## How it loads
An x64 proxy DLL named `XINPUT9_1_0.dll` placed beside `disgaea5.exe`. The game loads it at startup; it forwards
normal XInput calls to Windows. The executable on disk isn't modified. Its install script backs up any existing DLL
and checks hashes.

## What it touches
| Part of the game | How | Details |
|---|---|---|
| Files | None changed | One DLL added |
| Functions | Hooked in memory | Native battle and menu routines; reads battle state and submits commands through the game's own systems |
| Save data | Not described | |

## Compatibility
- Offsets are for one executable build (hash above). Other builds are untested.
- Uses a different proxy name from Overlord (`dinput8.dll`), so both DLLs can be present; whether their hooks collide is **Unknown**.

## Lessons for modders
- A proxy DLL is the least invasive way to add behavior: nothing on disk changes, and removing one file uninstalls it.
- Its planner scores actions (heal, skills, attacks, movement) from the current battle state: a useful reference for
  where battle state and command submission live.

## Sources
- [README](https://github.com/Grid487/disgaea5autoplay). **Community-reported.**
