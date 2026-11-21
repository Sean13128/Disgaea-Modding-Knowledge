# Disgaea 7: Vows of the Virtueless

- **Released:** 2023 · **Developer/publisher:** Nippon Ichi Software / NIS America
- **Versions covered here:** PC (Steam app 2250600, build 13254462). Switch, PS4/PS5 and the Complete edition aren't covered
- **Modding status:** good. A mod loader (Makai Kit) exists, with JSON table mods
- **Research on hand:** Strong

## Start here

- **I want to install mods:** install Makai Kit's loader, then drop mods into `mods/` ([tools.md](tools.md)).
- **I want to make a mod:** read [modifiable.md](modifiable.md).
- **I want to understand the game:** [INDEX.md](INDEX.md) → [data.md](data.md).

## Four things to know first

1. **Mods are JSON and files**, loaded at startup by Makai Kit. Vanilla files stay untouched.
2. **Evilities are data.** An evility record lists effect blocks (`effect[]`), so behaviour can change without code.
3. **Skills are Lua.** Skill animation and logic scripts are plain files the loader can replace.

## Pages

| Page | What's there |
|---|---|
| [INDEX.md](INDEX.md) | Files, and what a mod install adds |
| [data.md](data.md) | The 37 public-loader tables; key records by field name |
| [functions.md](functions.md) | Function mapping status, loader hook points |
| [modifiable.md](modifiable.md) | Loader rules, methods, mod conflicts |
| [assets.md](assets.md) | Model, texture, map and voice formats |
| [tools.md](tools.md) | Makai Kit and other tools |
| [mods/](mods/) | Overlord Edition |

## Shares code or formats with

**Format pages:** [NLSD](../../../formats/nlsd.md) · [FAFULLFS](../../../formats/fafullfs.md) · [textures](../../../formats/textures.md) · [engine families](../../../engines/README.md)

- **Disgaea 6:** same loader family (Makai Kit), shared model formats.
- **Disgaea 5:** about 3,000 closely matching functions.

## Open questions

- Save format; byte formats of models, textures and maps.

## Sources
See each page.
