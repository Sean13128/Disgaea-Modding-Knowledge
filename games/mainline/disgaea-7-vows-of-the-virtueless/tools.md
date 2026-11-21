# Disgaea 7 — Tools

| Tool | Does | Method | Status | License |
|---|---|---|---|---|
| [Makai Kit](https://github.com/HybridEidolon/makaikit) (HybridEidolon) | Mod loader for D6 and D7: file replacement; JSON table patching (D7: 37 tables in the public source); Lua script repacking (D6); archive and format libraries | `dinput8.dll` proxy + `mkplugins/` | Public source last updated 2023-11-01; no GitHub releases | GPL-3.0 (mods don't inherit it) |
| [makaikit-vits](https://github.com/twelvehouse/makaikit-vits) (twelvehouse) | Fork of Makai Kit that adds a mod loader for *Village in the Shade* (Nippon Ichi's 2026 farming sim; that the loader carried over suggests a shared engine, **Unknown**); the D6 and D7 parts are Makai Kit's | Same as Makai Kit | Fork | GPL-3.0 (inherited) |
| [Disgaea-7-Trainer](https://github.com/JellyfishCraft86/Disgaea-7-Trainer) (JellyfishCraft86) | Trainer with in-game overlays (HUD, targeting helpers); v2.6 at the time of writing | Memory + overlay | The repo's download link points to a build hosted in a different user's repository: check what you download | No licence stated |
| Cheat Engine tables | Memory edits | Memory | [Oct 2023 table](https://www.gamepressure.com/download/disgaea-7-vows-of-the-virtueless-cheat-table-v6102023-mod/zf14a81) | — |

## Recommended setup

1. Install Makai Kit's D7 loader (`dinput8.dll` in the game folder, the loader DLL in `mkplugins/`).
2. Put mods in `mods/`.
3. Write table changes as JSON in a mod's `databases/` folder ([modifiable.md](modifiable.md)).

## Sources
The linked pages.
