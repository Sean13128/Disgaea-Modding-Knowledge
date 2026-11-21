# Disgaea 7 — Engine Functions

**Executable:** `Disgaea7.exe`, x64, Steam build 13254462 (scanned for this project). Ships with `NmplDLL.dll`,
`NmplDLLMemoryInterface.dll` and `lz4.dll`.

**Evidence:** **From decomp**. Not tested in the running game by this project.

## What's mapped

D7 has about 60,600 functions. Function matching links about 4,800 of them to Disgaea 5's, about 3,000 closely:
D5, D6 and D7 share much engine code, so D5's [function map](../disgaea-5-alliance-of-vengeance/functions.md) is a
useful guide. Only about 1,600 D7 functions have names, so most of D7's engine is still unlabelled. The function index
itself is private (derived from the game), not in this repo.

## Mod loading hook points

Makai Kit's loader runs inside the game through a `dinput8.dll` proxy and intercepts the game's file requests
(serving mods' `files/`) and database loading (serving the repacked tables). Which game functions it hooks is in its
source ([modloader-d7](https://github.com/HybridEidolon/makaikit)).

## Open questions

- Names and roles for battle, AI, menus, saves and archive loading.
- Which D7 functions the closely matched D5 ones correspond to (needs a public, non-game-derived write-up).

## Sources

- This project's function-matching research (private).
- [Makai Kit](https://github.com/HybridEidolon/makaikit).
