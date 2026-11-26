# Save Files

Where each game keeps its saves, and what's known about the format. Back up saves before using mods or editors.

| Game | Location (PC unless noted) | Format | Tools |
|---|---|---|---|
| **Disgaea PC (D1)** | `SAVEDATA/` in the install folder (Steam Cloud) | 4-byte XOR key, then `YKCMP_V1` ([layout below](#disgaea-pc-layout)) | HenryEx's QuickBMS script (decode and re-pack); [Xkeeper0's disgaea-pc-tools](https://github.com/Xkeeper0/disgaea-pc-tools) (PHP, MIT: decode, edit objects) |
| **Disgaea 1 Complete** (Switch) | Console save | **Unknown** | EdiZon config |
| **Disgaea 2 PC** | `SAVEDATA/` in the install folder (Steam Cloud) | Same as Disgaea PC | HenryEx's QuickBMS script |
| **Disgaea 2** (PSP) | Memory stick | Encrypted; the editor needs decrypted saves | [lmc's save editor](https://github.com/lmc/disgaea-2-save-editor) |
| **Disgaea 3** (Vita) | `Data/savedata/` | **Unknown** | — |
| **Disgaea 4 Complete+** | `%APPDATA%\Nippon Ichi Software, Inc\Disgaea 4 Complete+\<Steam account ID>\SaveData\`; `config.ini` and `keysettings.bin` one level up (PCGamingWiki, SteamDB). The install folder also has `Data/SAVEDATA/<lang>/` | **Unknown** | — |
| **Disgaea 5 Complete** | `%APPDATA%\Nippon Ichi Software, Inc\Disgaea 5 Complete\<Steam account ID>\SaveData\` (`Save_001.sav` …, `AutoSave.sav`, `SystemSave.sav`) | Nibble-obfuscated header (magic `NIS_DISGAEA5_COMPLETE_SAVEDATA_`), then u64 XOR, then `YKCMP_V1` type 4 → the whole `0x89B8C8`-byte game state. **No checksum** | D5 Mod Kit save tools |
| **Disgaea 6 Complete** | `%APPDATA%\Nippon Ichi Software, Inc\Disgaea 6 Complete\<user ID>\` with `Setting.ini` (PCGamingWiki). Proton: `steamapps/compatdata/1749750/pfx/` | **Unknown** | — |
| **Disgaea 7** | `%APPDATA%\Nippon Ichi Software, Inc\Disgaea 7\<user ID>\`; `Setting.ini` in the `Disgaea 7` folder (deleting it resets settings only) | **Unknown** | — |
| **Disgaea D2** (PS3) | `NPUB31321_NORMAL_xx` save folders | Sony PFD protection (AES with console keys) | [pfdtool](https://github.com/SteffenL/pfdtool), [Apollo](https://github.com/bucanero/apollo-ps3); the D2 native port imports retail saves. `SAVEDATA.DAT` is a protected secure file of **1,498,152** bytes (1,498,160 encrypted, padded); decrypting it needs the game's secure file ID, which the game's save callbacks load from its own data (tools like Apollo ship it) |
| **Disgaea RPG** | Server-side | Out of scope | — |

## Disgaea PC layout

From Xkeeper0's disgaea-pc-tools (2016) and pg_disatools (**Community-reported**):

| Offset | Size | Field |
|---|---|---|
| 0x00 | 0x20 | Header, not encrypted (unknown) |
| 0x20 | 4 | XOR key. The key and everything from 0x30 on are XORed with it, repeating every 4 bytes (so a decoded file has key `00 00 00 00` and is still a valid save) |
| 0x24 | 2 + 2 | Unknown |
| 0x28 | 4 | Length ÷ 4 |
| 0x2C | 4 | Length |
| 0x30 | 8 | `YKCMP_V1` |
| 0x3C / 0x40 | 4 / 4 | Compressed / decompressed size |
| 0x44 | — | Compressed data |

The decompressed save is a **copy of the game's memory**: 128 unit records of 1,720 bytes from `0xBB8` (the same
record pg_disatools documents, see [Disgaea PC functions](../games/mainline/disgaea-1-hour-of-darkness/functions.md#unit-record-and-saves)),
512 Dark Assembly senators of 32 bytes from `0x367B8`, the unit count at `0x44830`, volume settings from `0x44878`, and
three extra NPC unit records from `0x45188`. Disgaea 2 PC uses the same container.

## Disgaea 5: how the game writes a save

Build **B** (from the D5 Mod Kit fork's research; **Verified offline** on copies of real saves):

- One function (`0x1407436D0`) builds the name (`Save_%03d.sav`, or `AutoSave.sav` for slot 0), writes the
  **0x1C0-byte header** and then the encoded block, and returns 0 on success. On a short write it deletes the partial
  file and returns 1.
- The header starts with `NIS_DISGAEA5_COMPLETE_SAVEDATA_` with every byte nibble-transformed (`((~b) << 4) | (b >> 4)`);
  the block's first 8 bytes are the container magic `0xE5B2688ACB279CA6`, untransformed. The block is encoded by
  `0x140744730`.
- **Saves run on a new thread** each time: the manual-save worker (`0x140754C30`) and the autosave worker
  (`0x140744F20`) are queued as jobs that end in `_beginthreadex`. A hook there must not touch game state.
- A separate function deletes a slot (`0x140743090`, via a `DeleteFileW` wrapper at `0x1401194EC`). Don't mistake it for
  the writer: one of the Kit's own early hooks watched the delete and so never saw a real save.

## Disgaea 5: the file, byte by byte

Decoded from three real saves, all round-tripping byte for byte (D5 Mod Kit fork research, October 2026; **Verified
offline**):

| Offset | Size | What |
|---|---|---|
| 0x000 | 0x1C0 | Header, obfuscated: XOR `0xF0` then swap nibbles (decoding = the reverse). Decoded `+0x38` is a u64: the length of everything after the header (header + it = file size) |
| 0x1C0 | 8 | Marker, bytes `A6 9C 27 CB 8A 68 B2 E5` |
| 0x1C8 | — | Every **complete** 8-byte block XORed with `37 6A D4 F6 E3 CB 6F E7` (as a little-endian u64: `0xE76FCBE3F6D46A37`); a **partial last block is left plain** |
| inside | — | `YKCMP_V1` type 4 ([YKCMP](ykcmp.md)); decompresses to exactly `0x89B8C8` bytes: the game state |

**Inside the game state** (same research; the state sits at `0x144FCCB00` in memory on build B):

| Offset | What |
|---|---|
| `+0x23B2B8` | 128 unit records × `0x4750`. **Army** units fill slots from 0 (count, s16, at `+0x5A7F9C`); **reserve** units fill from slot 127 down (count at `+0x5A7F9E`). Slots in between can hold stale copies: don't treat every non-empty record as a unit |
| `+0x482FD8` | Inventory: 2,000 item records × `0x258` (ends at `+0x5A7F58`) |
| `+0x5A7F60` | u64 counter for new unit serials |
| `+0x87B7F0` | 25 squads × `0xB8`: twenty u64 member serials at `+0`, progress counters `+0xA0` / `+0xA4`, unlocked member slots `+0xA8`, leader serial `+0xB0` |

Inside a unit: five equipped items inline at `+0x10` (`0x258` each); squad at `+0x30A0` (signed; −1 = none), list count
`+0x30A1`; rank `+0x2E08` (unique characters −1); serial `+0x3060`. Inside an item: eight innocents at `+0x4`
(`0x14` each: `HABIT.dat` key, strength, …, an appearance character ID at `+0xC`), `item.dat` key at `+0x128`, innocent
capacity at `+0x148`, a 16-byte special-innocent bitmap at `+0x1BC`. `HABIT.dat` has 110 rows of `0x430` bytes, keyed by
the value at row `+4`. Link units by serial, never by slot.

## Disgaea 5: what's inside, and what breaks

The best-documented save. Unit records are `0x4750` bytes inside the game state; each caches its `char.dat` **row
index**, which the game doesn't recompute on load. So mods must only **append** table rows with new IDs above the
maximum, or old saves show the wrong class data. Details: [D5 functions](../games/mainline/disgaea-5-alliance-of-vengeance/functions.md#unit-record-in-the-game-state) (more offsets: [unit record, more offsets](../games/mainline/disgaea-5-alliance-of-vengeance/functions.md#unit-record-more-offsets-compiled-from-the-kits-save-reader-and-decomp-reading))
and [D5 modifiable](../games/mainline/disgaea-5-alliance-of-vengeance/modifiable.md).

## Sources
Game pages' INDEX files; [ZenHAX save thread](https://www.zenhax.com/viewtopic.php@t=4698.html); PCGamingWiki via
search results ([D7](https://gamecytus.netlify.app/host-https-www.pcgamingwiki.com/wiki/Disgaea_7:_Vows_of_the_Virtueless),
[D6](https://gamecytus.netlify.app/host-https-www.pcgamingwiki.com/wiki/Disgaea_6_Complete),
[D4](https://gamecytus.netlify.app/host-https-www.pcgamingwiki.com/wiki/Disgaea_4_Complete%2B));
[SteamDB: D4 cloud saves](https://steamdb.info/app/1233880/ufs); [Steam patch note (D7 Setting.ini)](https://steamdb.info/patchnotes/12359398);
[Disgaea 6 save guide](https://www.gamenguides.com/disgaea-6-complete-save-game-and-config-file-location).
