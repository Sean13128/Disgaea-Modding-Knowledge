# Disgaea 6 — What Can Be Modified

**Evidence:** **Verified offline** by reading [Makai Kit](https://github.com/HybridEidolon/makaikit)'s public source
(last commit 2023-11-01); not tested in game by this project.

## Makai Kit's D6 loader

Install: `dinput8.dll` in the game folder and the D6 loader DLL in `mkplugins/`; mods in `mods/` (`_generated` is
reserved).

**Which mod wins:** the loader lists the folders in `mods/` in directory order (alphabetical on Windows' NTFS), then
**reverses** the list, and for each file takes the first match. So when two mods replace the same file or script, the
**alphabetically last** mod wins. Anything in `mods/_generated/` beats every mod. The loader only redirects the game's
data mount (mount 5); user data such as saves (mount 2) is never redirected.

| Feature | D6 loader | How |
|---|---|---|
| **Loose files** | Yes | Hooks the game's "get file from archive" function (found by byte-pattern search) and switches it to load loose files from `mods/_generated/` and each mod's `files/` |
| **Lua scripts** | Yes, D6 only | At startup, walks each mod's `scripts/` folder for `.lua` files and repacks `data/script.dat` into `mods/_generated/data/script.dat`. Each `.lua` replaces the archive entry of the same name (lower-cased, with a `.lub` extension); unreplaced scripts are copied from the original. `script.dat` is a [DSARC FL](../../../formats/dsarc.md) archive. When two mods ship the same script, the one that comes first in the loader's list wins and the other is skipped with a warning |
| **Table patching** | **No** | The README describes JSON table patching in general, but the public D6 loader doesn't implement it (only D7's does) |

## Other tools

Text: `Disgaea6StringTool` rebuilds `string.dat` from a text file; other text-holding `.dat` files gave errors with it
(**Community-reported**: [ZenHAX](https://www.zenhax.com/viewtopic.php@t=17081.html)).

## Open questions
- Save format; limits and crash causes.
- Does a newer, unpublished loader add table patching for D6, as one did for D7?

## Sources
[Makai Kit](https://github.com/HybridEidolon/makaikit) (`modloader-d6/src/win.rs`, `scriptrepack.rs`); [ZenHAX thread](https://www.zenhax.com/viewtopic.php@t=17081.html).
