# Your first Disgaea 7 mod (Makai Kit)

A minimal table mod: change one evility's slot cost. **Built from Makai Kit's source and documentation; not tested in
game by this project.** Back up first ([setup and backups](setup-and-backups.md)).

## 1. Install the loader

Get [Makai Kit](https://github.com/HybridEidolon/makaikit)'s D7 build (build it from source, or use a distribution zip).
Put `dinput8.dll` in the game folder (beside `Disgaea7.exe`) and the D7 loader DLL in `mkplugins/`.

## 2. Make the mod folder

```
Disgaea 7 Vows of the Virtueless/
  mods/
    my_first_mod/
      databases/
        evility/
          <ID>_<ENUM>.merge.json
```

- Don't start a folder name with `_`. If two mods touch the same record, the alphabetically **first** folder's patch
  wins (files go the other way: see [conflicts](../games/mainline/disgaea-7-vows-of-the-virtueless/modifiable.md#mod-conflicts)).
- The file name picks the record: if it starts with a number and `_`, the number is the record **ID**; otherwise the
  whole name is the record's **enum name**.

## 3. Write the patch

A `.merge.json` is a JSON Merge Patch (RFC 7386): only the fields you list change. To make an evility cost one slot:

```json
{ "cost": 1 }
```

Field names come from Makai Kit's record structs (camelCase; IDs are `ID` and `sortID`). An evility record has `ID`,
`enumName`, `name`, `help`, `icon`, `sortID`, `cost`, `mana`, `isUnique`, `isShop`, `isEnemyOnly`, `isNotExport`,
`notRankBattle`, `condOnFlag`, `condOffFlag`, `type`, `effect`, `version`, `region`, `product`
([D7 data](../games/mainline/disgaea-7-vows-of-the-virtueless/data.md)).

To find the ID and enum name, dump the vanilla tables (Makai Kit's database tools).

## 4. Run the game

At startup the loader rebuilds the table into `mods/_generated/data/database/evility.dat` and serves it in place of
the original. Check the loader's log if the change doesn't appear.

## Next steps
- Whole new records: a full `.json` with every field.
- Precise list edits: a `.patch.json` (RFC 6902 JSON Patch).
- Not every table can be patched by every loader version: [D7 data](../games/mainline/disgaea-7-vows-of-the-virtueless/data.md).
