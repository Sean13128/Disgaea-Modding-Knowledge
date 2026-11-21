# Your first Disgaea PC mod (pg_disatools)

Two small mods for **Disgaea PC** (Steam, 2016): replace one sprite, and make a recolored copy of a class. Both use
pg_disatools' mod framework, so the game's archives stay untouched. **Built from pg_disatools' documentation; not
tested in game by this project.** Back up first ([setup and backups](setup-and-backups.md)), and **back up your saves**:
the framework writes extra metadata into them when custom characters are in your party.

## 1. Install the tools

1. Download the latest release of [pg_disatools](https://github.com/ProgSys/pg_disatools) and unzip it to a folder
   where it can stay (the game will load files from it).
2. Run `DisaModManager.exe` and point it at `dis1_st.exe` (Steam: right-click the game → *Manage* → *Browse local files*).
3. Confirm. It patches the executable and adds a `mod/` folder ([what it changes](../games/mainline/disgaea-1-hour-of-darkness/functions.md#how-pg_disatools-gets-into-the-game)).
   If it won't start, install the Microsoft Visual C++ 2015–2022 redistributables, **both x86 and x64**.

Don't use Pretty Prinny with custom characters: pg_disatools reports it crashes the game.

## 2. Get a sprite to work on

1. Open `SUBDATA.DAT` in the **File Manager**, step into `START.DAT` (decompress it if asked; the game accepts it
   uncompressed), then into `ANM00.DAT` (most sprites are there).
2. Export the `.SH` sprite you want. Sprites are numbered; the Prinny is sprite 2210.
3. Edit it in the **Sprite Sheet Editor**. Sprites use colour tables (16 colours by default); to add a bigger or
   HD drawing, add a new sheet, then a layer and keyframes on the animations you want
   ([how sprites are built](../formats/disgaea-pc-containers.md#sh-sprite-sheet)).

## 3. Package it as a mod

Make a zip, for example `MyPrinny.zip`:

```
MyPrinny.zip
  character.json
  sprites/
    prinny_hd.SH
```

`character.json`, the smallest useful form (field names from pg_disatools' *Adding Custom Characters* page):

```json
{
  "id": "<a new UUID you generate>",
  "name": "My Prinny",
  "author": "you",
  "sprites": [
    { "identifier": "disa::2210", "file": "prinny_hd.SH" }
  ]
}
```

Using the game's own identifier, `disa::2210`, **overrides** the original sprite. File names are case-sensitive and
must match the zip exactly.

## 4. Install and test

In DisaModManager, **Import Mod** and pick the zip (don't unzip it). Close the manager; it doesn't need to run. Start the
game from Steam and find a Prinny.

## 5. A new character (next step)

Add a `characters` list to the same kind of file. A character needs its own `identifier`, a `spriteIdentifier` and a
`faceIdentifier` (portraits go in `img/` as `.TX2`, SD and HD versions), a `name` and `title`, and the `CHAR_E.DAT`
fields you want ([field list](../games/mainline/disgaea-1-hour-of-darkness/data.md#character-table-disgaea-pc)).
`deriveFrom` copies an earlier definition so you only list what changes: an existing class with a different
`paletteIndex` is the simplest new character. `recruitmentType` and `recruitGroupName` control how it appears in the
Dark Assembly. Skills go in a `skills` list of `{identifier, level}`; new skills go in a `magic` list with the
`MAGIC.DAT` fields.

Give every mod its own UUID and keep it across versions: saves and the load order use it.

## If something goes wrong

- **Crash on load after editing archive files directly:** the data outgrew the game's fixed buffer. The File Manager
  can enlarge it ([why](../games/mainline/disgaea-1-hour-of-darkness/functions.md#fixed-memory)). Mods loaded through the
  framework don't hit this.
- **Uninstall:** the documented way is to delete everything in the game folder **except `SAVEDATA`**, then let Steam
  *Verify integrity of game files* restore the rest.

## Sources
[pg_disatools wiki](https://github.com/ProgSys/pg_disatools/wiki): How to install a Mod, Adding Custom Characters,
Replacing a sprite with a HD version, Map Editor, memory allocation (read 2026-11-04). **Community-reported.**
