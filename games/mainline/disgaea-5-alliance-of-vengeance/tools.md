# Disgaea 5 Complete — Tools

PC version. What each tool is for, the builds it supports, and how it touches the game. Tools aren't stored here;
follow the links.

| Tool | Does | Builds | Method | Status | License |
|---|---|---|---|---|---|
| [D5 Mod Kit](https://github.com/eraser4521/D5ModKit) (eraser4521) | Full modding studio: characters, classes, outfits, skills and animations, Overloads, maps, database, dialogue, scripts, music, textures, voices; mod manager with profiles; one-file installers for players | Oct 2018 launch build and Jan 2019 Steam update | Rebuilds archives and patches the executable; backs up originals | Active (v1.5.8) | MIT |
| [D5tools](https://github.com/BTAxis/D5tools) (BTAxis) | Command-line extract/repack: NISPACK archives (`databaser`), music (`musicDBer`, `musicer`), sound effects (`vagger`), database ↔ CSV (`dat2csv.pl` with `.scheme` layouts) | PC | Files on disk | Older | Not stated |
| Overlord Edition (Cryswar, Aryuto on Reddit) | Rebalanced characters and evilities, playable bosses unlocked by Strategy Assembly bills, innocent names that show their effect, editable stat caps, table editors (**Community-reported**: [D5 v1](https://reddit.com/r/Disgaea/comments/1our5w7/disgaea_5_overlord_edition_mod/), [D5 v2](https://reddit.com/r/Disgaea/comments/1ur6wvs/disgaea_5_overlord_edition_mod_v2/) release posts) | Steam build | Its own loader with exe hooks, plus data overlays | Active | No licence stated; ask the author |
| [disgaea5autoplay](https://github.com/Grid487/disgaea5autoplay) (Grid487) | Auto-battle (F1), stage repeat (F2), custom party (F3) | Steam exe with SHA-256 `49a7530b…db282ce` only | Runtime hooks through an `XINPUT9_1_0.dll` proxy | Experimental | MIT |
| Disgaea5Fix (Lyall) | Ultrawide and narrower aspect ratios | PC | Display fix | — | — |
| Cheat Engine tables (e.g. the [Fearless Revolution thread](https://fearlessrevolution.com/viewtopic.php?f=4&t=6692)) | Memory edits; also a public source of memory addresses | Check each table's build | Memory | Various | — |

## Recommended setup

- **Playing with mods:** use the installer `.exe` the mod's author exported from D5 Mod Kit, or D5 Mod Kit itself. Both
  check the game build and refuse a game already changed by other tools.
- **Finding mods:** D5 Mod Kit has a built-in community list (**Browse Mods**; post with **Post my mod…**). The list
  itself is a public GitHub repository ([D5ModKit-Mods](https://github.com/eraser4521/D5ModKit-Mods)) holding
  `index.json` and each mod's zip as a release; the Kit checks every download's size and SHA-256 and refuses packages
  that contain programs or game-folder files. Posting needs a GitHub account; browsing doesn't. As of 2026-11-06 the
  list had no published mods yet (one submission open).
- **Making mods:** start from a clean Steam install (Verify integrity), install D5 Mod Kit, and keep its backups.
- **Combining Overlord with D5 Mod Kit:** see the conflict notes on [modifiable.md](modifiable.md#mod-conflicts-to-know-about).

## Reference data shipped in D5 Mod Kit

The public Kit's `d5lib/data/` folder holds machine-readable research a modder can use directly (MIT):

| File | What it is |
|---|---|
| `script_opcodes.tsv` | All 567 registered script commands: opcode, operand layout, handler address, a name or hint (for example `0104` CallScript) |
| `script_operand_types.json` | Operand types per command (563 entries) |
| `choreo_commands.json` | Skill-choreography commands (events 15xxxxxx / 12000xxx) with how sure each meaning is (tested in game / clear from the code / guessed); positions are in tenths of a dot relative to an actor, actor 1256 = the caster |
| `effect_names.json` | Names for 383 effects |
| `identity_sites.json` | The 95 places the game asks "is this unit character X?" by comparing the unit's class ID (`+0x2E04`) with a fixed ID (47), a register (33) or a searched-for class (15). Anything that changes a unit's class (outfits, new classes) has to keep these seeing the base character |
| `exe_map_9460a6411699.json` | Address map from the launch build (A, `436eefd2…`) to the Steam build (B, `9460a641…`) for 1,111 functions, each marked exact or aligned. The Kit's own addresses are build A; this is how they're translated |

## Testing tools

How this project tests mods without a person playing (file gates, the game's own code in an emulator, a model of the
installed game, and a scripted live harness over the Kit runtime's named pipe) is written up in
[Automated testing of Disgaea 5 mods](../../../guides/automated-testing-disgaea-5.md).

## Sources

The linked project pages; Disgaea5Fix from its [GamePressure listing](https://www.gamepressure.com/download/disgaea-5-complete-disgaea5fix-v081-mod/z214fd3); Overlord features from its public release posts; Cheat Engine thread from a [Steam discussion](https://steamcommunity.com/app/803600/discussions/0/1742227898993622261).

## Community

The **Death's Requiem** Discord (often just "Requiem") is where D5 PC modders gather; several mods credit it. **Community-reported** ([Steam thread](https://steamcommunity.com/app/803600/discussions/0/2950376208535140648), [CD5E post](https://steamcommunity.com/app/803600/discussions/0/1648792158828778141/)).
