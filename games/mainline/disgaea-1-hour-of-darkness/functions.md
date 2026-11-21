# Disgaea: Hour of Darkness — Engine Functions

| Edition | Executable | Notes |
|---|---|---|
| Disgaea PC | `dis1_st.exe`, 32-bit Windows, image base `0x00400000` | May be wrapped by Steam DRM; unwrap before analysis |
| Disgaea 1 Complete (Switch) | ARM64 | |
| Disgaea 1 Complete (PS4) | `eboot.bin` | |

## Disgaea PC

About 2,400 functions, few with names. The public map comes from pg_disatools: its wiki pages and its
*DisaReverseEngineering* module, a DLL the tools load into the game to hook the renderer, text, menus and combat.
Addresses are for the current Steam executable; the installer accepts the two MD5s below, which is the best
identification of the build available.

**Evidence:** **Community-reported** (pg_disatools, used in its released mods). Not checked by this project.

### How pg_disatools gets into the game

DisaModManager (or the Map Editor's setup) **patches `dis1_st.exe`** with an install script (`disaInstall.cscript` in the
release). It first checks the executable's MD5 against two accepted values (`1235eb61e0f31ad211b1fc441bef121d`,
`c4ebae3bc277fe4198274a70dbb1fe6d`), so it only patches builds it knows. Then it moves the import table into a new
section (`.IIDKing`, the classic import-injection approach) with an added `kernel32.dll!LoadLibraryA` import, and
redirects one start-up function pointer to a small stub that calls the original function and then loads **`j.dll`**
(the DisaReverseEngineering module). It also unpacks its files into the game folder, creates `mod/`, and
writes a `config.ini` pointing at the tools' `resources` folder. Once installed, the tools don't need to be running.
Uninstalling means restoring the original executable (Steam's *Verify integrity of game files*) and removing the
added files; keep `SAVEDATA`.

### Addresses

| What | Address | Notes |
|---|---|---|
| Global game-state bits | `0x00619330` | The bit array TALK filters and `Set`/`Clear` use |
| Player units | `0x00619E98` | 1,720-byte records; count (u16) at `0x0065DB10` |
| Level EXP table | `0x005E9B28` | 10,000 × u64, built at start-up by `0x0048CC90` |
| Key states | `0x00778D98` | 16 bytes, one per logical button; 2 = pressed |
| Dungeon list (from `DUNGEON.DAT`) | `0x00664020` (count `0x00664024`) | Teleport menu entries; displayed list `0x0088AC90` |
| Teleport panel X / Z | `0x0065DAE8` / `0x0065DAEC` | |
| Dark Assembly senators | `0x0064FA98` | 512 × 32 bytes: level, character ID, attendance, times you killed them, name text group / index, Shift-JIS name (`+0x0D`), **favor** (`+0x1E`, signed; starts between −20 and 10; shown from Loathe to Love); count at `0x0065DB14` |
| Map units | `0x00738710` | `0x42C` bytes each: sprite and animation state, position / velocity / move target, gravity (0.163 by default), status icons, colour, HP bar, character ID, sprite ID, palette, pose (0 idle, 1 walk, 3 jump, 5 held, 8 lifting), facing in degrees |
| Assembly unit slots | `0x00619598` | 288 slots `{kills, character ID, sum of killed levels}`. For character IDs over 2000 (monsters) the kill count scales the creation mana cost, counting down from 29 to 0; lookup list at `0x0088BA10` (size `0x0088B588`) |
| Enemy killed | `0x004D63B0` | |
| Name / title text refresh after loading | `0x004CD1B0` | Covers the 128 party slots, the three extra units at `0x0065E468` and the senators |
| Sprite loader buckets | ending at `0x00749AFC` | Searched by sprite ID |
| Music list | `0x00531870` | Names and loop points |
| Talk-script runner | `0x004BF7E0` | |
| Unit stat computation | `0x004DAFC0`, HP scaling `0x004DB610` | |
| Sprite lookup / pre-process | `0x004DE880` / `0x004DE780` | |
| Text box, text, string width | `0x004DD3F0`, `0x0050C4D0` (old: `0x004F4D20`), `0x004F3930` | What translation mods hook |
| Images, cutouts, background box | `0x0050E5D0`, `0x0050E9E0`, `0x004F6A40` | |
| Play a sound | `0x004DE090` | |
| List scrolling, mouse tests | `0x004D0B70`; `0x0050F590`, `0x0050F7C0`, `0x0050F950` | |
| Render-object list | `0x00426770` | Draws objects by layer (UI, characters, map) |

### Levels and stats

- **EXP per level:** a curve computed once at start-up for levels 0–9,999, with a soft cap above level 98; each
  character's `CHAR.DAT` **EXP scale** multiplies it. (The original loop also writes one value before the table, a
  harmless out-of-bounds write.)
- **Base stat at load:** `((r mod 7 + 47) × base stat × (level + 1)) / 100`, with `r` from the game's RNG. Laharl
  (base HP 18) at level 10 gets 93–104 HP.
- **HP scaling:** HP is then multiplied by `100 + 3 × (level − 1)` percent up to level 34, `100 + level + 65` above
  that, capped at **400%**.
- **RNG:** a linear congruential generator, `seed = seed × 877 + 57377`.

### Unit record and saves

A party unit is **1,720 bytes**: total EXP (u64), four equipment slots (weapon + three items, 144 bytes each: 16
resident innocents, stats, base stats, ID, level, rarity, rank, range …), name and title, ailment and element
resistances, **96 skill slots** (EXP, `MAGIC.DAT` ID, level), current HP/SP, active and fixed stats, weapon mastery,
mana, bonus stats, level, character ID, sprite ID, `CHAR.DAT` row, jump, movement, counter, palette, learn speed.

**The save file is a straight copy of this memory**: the first unit starts at `0xBB8` in a decoded save (after
XOR + `YKCMP_V1` decoding; see [saves](../../../formats/saves.md)). Full structure:
[DisaPartyCharacter](https://github.com/ProgSys/pg_disatools/wiki/DisaPartyCharacter).

### Fixed memory

The game allocates **one fixed-size buffer** for loaded game data at start-up, not per file. Data larger than the
buffer crashes the game, which is behind the "SUBDATA too big" crashes. Disa PC File Manager can raise the buffer
size in the executable (location found by Kelebek1). Mods loaded by pg_disatools' own module use separate memory and
aren't affected.

## Disgaea 1 Complete

About 2,400 functions (Switch). Function matching against Disgaea 5 finds few matches, partly because this build is
ARM64 and D5 is x64. Its *data* is close to D5's: sprites use D5's anm format and monsters use D5's action IDs (see
[assets.md](assets.md)). The function index is private (derived from the game), not in this repo.

**Named so far (2026-11, From decomp; Switch build):** `FUN_7100012ea4` builds the 82 target-panel lists at start-up
(PC twin `FUN_004430d0`); `FUN_710001d338` and `FUN_71000afde0` read them (PC `FUN_0044a3b0`, `FUN_004cd5c0`). On PC,
`FUN_0046fdc0` is the special-attack value and `FUN_0046f670` the normal one. Details on [data.md](data.md). D1's own
body files hold no 40x (weapon attack) animation.

Face lookup (2026-11, From decomp, Switch v0): `FUN_7100006af8` builds `swf%04d` from sprite + palette for the HD faces in
`anmdat.fad` ([data.md](data.md#face-portraits-disgaea-1-complete)); the script voice opcode is `0x87` (`7100098370`), and
`7100020794` chooses skill ID + 2,000,000 or + 2,050,000 as the skill event.

## Open questions

- Which of the two accepted executables (for example DRM-wrapped and unwrapped) each MD5 is.
- The combat state machine (pg_disatools reversed most of it for its Better Controls mod; not written up).
- Everything else for Disgaea 1 Complete.

## Sources
[pg_disatools](https://github.com/ProgSys/pg_disatools) by ProgSys: wiki pages Addresses, DisaPartyCharacter,
DisaKeyStates, DisaTScriptManager, DisaRenderObjectManager, BGM, Sprite offset list archive, memory allocation; source
`PG_DisaInstaller.cpp`. Read 2026-11-04. This project's private decomp index (function counts only).
