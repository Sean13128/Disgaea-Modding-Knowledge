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

### Sprite and animation chain (Switch v0, 2026-11-24 and 25)

How a Disgaea 1 Complete character becomes pixels. **Evidence:** **Verified offline** where the game's own function was run in an
emulator on the Switch `main` (NSO) with the game's own data, or the whole dataset was checked; **From decomp** where the code
was read but not run. Nothing was watched in the running game. Function addresses are `0x71xxxxxxxx` in the Switch v0 executable
(SHA-256 `8c6d9c85ae80f896b774dc49cf3562607520934ecd5e920090b577d606165d31`, image base `0x7100000000`).

**Classic or HD art.** Which draw path a unit uses depends on its **character ID only**: `0x7100045A70` is 1 (HD animation owner)
for every ID except 2300 to 2302 (the three Cannons, sprite field 1) and 2917, 3200 to 3202 (sprite fields 3601 and 3620 to
3622, no HD block), which use the classic sprite script (`0x71000C6BCC`). A second integer test, `0x7100046000`, says whether the
ID is a **humanoid** (IDs 1 to 9, 1000 to 1999 and some others). Both were run over all 363 character IDs (**Verified offline**).

**HD body block.** The art block is *sprite × 10* when the sprite field is below 10, else the sprite field (Gordon 4 gives 40,
Dark Flonne 3 gives 30, Jennifer 5 gives 50; `0x7100048EF8`). A **humanoid** unit's owner animation set is the shared **block 1**
(105 motions, 124 cells on one 16 × 16 texture, no art of its own) with the body block bound to node key 1 and weapon blocks to
keys 10 to 16; the body block supplies the textures, palettes and the idle stubs `6001` / `6007`. A **monster's** owner is its
own body block, which holds 39 to 54 motions of its own in the same numbering as block 1. Sprite 1 (block 10) is the exception:
with motion 5000 or `0x138D` requested it is not given the shared rig (`0x71000477AC`). The unit's **palette byte** (row `+0x49`)
**replaces** the cell's own palette index (0xFF keeps the cell's); it is below the block's palette count for all 46 rows checked.
Palette entry 1 of body 1140 is (0, 0, 0, 128) (**From decomp**).

**Weapon blocks (humanoids).** Block *9001 + type × 100 + (ID mod 20)* for a weapon ID below 60, else *9021 + type × 100 + (ID mod
20)*, for weapon types 2 to 7; palette *ID / 20* (or *(ID / 20) mod 3* from ID 60). The formula yields exactly 240 of the 241 blocks
numbered 9000 to 9999 (the 241st, 9901, is bound by motions 2630 to 2639). The block is bound to node ID 11 to 16 through the key
table `{1, 10 to 16}` at `0x71001EB8A0` (`0x71000473BC`, `0x7100049040`); a cell on a named node (ID other than 0) draws only while
that ID is bound, so an unbound mount (10 to 16, 111 to 116, 14) draws nothing (**Verified offline**).

**Which motion number plays** (`0x71000463AC`; **Verified offline**, machine code against a model over the full grid):
*number = ±(base[state] + weapon term + direction suffix)*.

- The unit's action **state** (unit `+0x254`, 0 to 12) indexes the base table at `0x71001EB8B0`: 1000, 1010, 1020, 1030, 1500,
  1060, 1070, 1080, 1090, 1050, 1610, 1620, 2670.
- When the weapon type (unit `+0x28A`) is above 0 and the state is not 8 or 12, the base gains 1000, and for state 4 also
  10 × weapon type. The unit bytes `+0x290` / `+0x28F` can alter the state (0 becomes 12, or 5 is forced).
- Facing: the angle minus the camera yaw gives a quadrant 0 to 3; table `{1, 7, -7, -1}` at `0x71001E9B88` gives the suffix
  (front, back, then the same two mirrored). A negative number plays the animation of the absolute ID mirrored in X.
- A number the owner block lacks plays animation index 0 of that block (and of the body block for nested keys).
- Script path: opcode `0x67` (`0x7100098370`: `u16` target, `s16` state, `u8` facing) stores the state and calls the same
  function; the number from a script *family* `s` is the direction plus 10 × `s` (negative directions: minus 10 × `s`)
  (**From decomp**).

**What the states are.** The code does not name them. **Found writers (offline):** state 0 is the reset target; state 5 (base
1060) and state 9 (base 1050) are set by battle code with a 10-tick and a 60-tick timer (unit `+0x266`) and fall back to 0; the
battle path walker `0x710001CA7C` writes states 1, 3, 7, 10 and 11 (**Verified offline**: the walker and the hub branch were
run). So: state 2 (1020) is the hub run (stick magnitude above 110; in the hub, facing mode 2 resolves it to family 20, not
1021) and scripted fast travel; state 3 (1030) a hop between tiles of different height; state 6 (1070) a cast pose on the caster
and a story gesture (no native writer found); state 7 (1080) the fly-up to a higher tile, movement type 1 only (six bodies, all
holding 1081 / 1087); states 10 (1610) and 11 (1620) warp out and in, movement type 2 only (bodies 2580 and 6001; 6001 lacks
1611 / 1621 and would use index 0); state 12 (2670) is derived. Of 316 state-3 script requests, 56 have no label; the story uses
of state 6 are unlabelled; whether a shipped stage moves a type-2 unit was not searched. The shared block 1 holds the pairs
1001 / 1007, 1011 / 1017, 1021 / 1027, 1031 / 1037, 1501 / 1507, 1051 / 1057, 1061 / 1067, 1071 / 1077, 1091 / 1097 and
2671 / 2677 plus weapon variants (2001 up, 2511 to 2571), and lacks 1081 / 1087 and 1611 / 1621. For human-readable motion names
see [motion ids across games](../../../formats/motion-names.md).

**Drawing rules the emulator run fixed** (the game's ANM engine was run on the real pack: all 669 blocks, with the draw packets
compared against an independent reader): one tick per call of the step function `0x71000E4AE0` (the main loop calls it once per
iteration, one tick per displayed frame at 60 Hz present interval 1 (**From decomp** for the cadence)); time is Q16.16 with at
most 0x10000 added; control flags 4 (event gate) and 5 (end) hold, 2 and 3 loop back to the flag-1 key, and motion 1501 holds at
its hit key until game logic releases it. Cell flags (`u16` at cell `+6`) are an x-flip byte and a y-flip byte; zero-size cells
and cells whose page is not among the (bound) block's textures queue nothing. Rectangle nodes queue vertices TL, TR, BL, BR;
mesh nodes their four points. Nested keys on node ID 1 of a humanoid owner play the body block's animation (an unknown ID plays
the body's index 0); on anonymous nodes they start a child actor with its own clock. Vertex colour words are R, G, B truncated and
alpha × 128 / 255. The animation origin (feet) is drawn at the unit's world position; there is no per-character anchor, and the
owner scale is 1.0 in battle (about 0.25 on menu figures).

**Classic sprites (the three Cannons).** `start.dat` member 71 (11,419,984 bytes) holds 216 classic sprites: `u32` count, `u32`
offsets, `u16` IDs (9101 to 9720 weapons, and 1 to 5), then records (loader `0x71000C6034`, parser `0x71000C6744`, run on all
216 records). A record header has four `s16` counts (motions, pictures, palette sets, textures), four `u32` offsets, then the
motion list `(s16 first frame, u16 motion ID)`, picture list `(s16 first part, s16 part count)`, palette sizes (units of 16 colours,
each set expanded to a 256-entry buffer) and 12-byte texture records. Frames are 8 bytes (`u16` picture, `u8` duration, `u8`
command: 1 frame, 2 and 3 loop back, `0x0F` counted repeat). Textures are 4 bits per pixel (low nibble first) or 8; palette
entries are the file bytes 2, 1, 0, 3. The Cannons draw sprite 1 (record index 157, 109 motions, eight 256 × 256 textures, palette
0). The **28-byte part layout is inferred from data only**: the classic draw and sort routine (`0x71000C5F44`) executes an
undefined instruction word when run with one or more queued records, so how (or whether) this build draws the Cannons' classic art
is **Unknown**.

**Voices (Verified offline for tables, From decomp for the pick).** Script opcode `0x87` (`u16` operand, `u8` volume) plays a clip:
slot = operand / 10,000 (0 to 3), character = operand mod 10,000 (0 means the actor). A **selector** at `0x7100232D58` holds 66
records of 40 bytes (`i64` ID, four list pointers; each list is `s16` record IDs ending in -1); IDs above 999 are rounded down to
a multiple of ten (2216 is read as 2220); Maderas (2550) and Don Joaquin (2600) have null lists. A random index picks a record
(`0x71000496A4(100) mod count`); ID 0 or an unknown ID plays nothing. The **records** (205 × 12 bytes at `0x71002323B8`) hold `s16`
ID, type, clip index, gain and a `u32`: type 2 is bank `voice01`, type 3 `voice02`, type 1 a sound effect looked up in a 131 × 10
byte table at `0x7100231E9A`; volume = script volume × gain × 255 >> 15. Clip names come from four name tables in the executable
(15 bytes per entry). The English banks are `start.dat` members 64 (62 clips), 66 (50), 68 (107) and 70 (78), each a `DSARC FL`
archive (48-byte entries); 68 and 70 are byte-equal to `voice01_en.pbd` and `voice02_en.pbd`. A clip is a 28-byte wrapper, a 96-byte
Nintendo DSP header and ADPCM frames (16, 22.05, 44.1 or 48 kHz). No code reference to the Japanese `voice01.pbd` / `voice02.pbd` was
found. Of the 46 characters this project studied, 25 have no selector entry (silent through this route; whether another route
plays a line for them is **Unknown**), 4 name only a shared sound effect and 2 have null lists. The selector functions are called
only from opcode `0x87`; explicit records come from opcode `0x88` and `0x71000AB514`.

### Disgaea PC animation sets (Community-reported)

The pg_disatools wiki's [SCRIPT.DAT page](https://github.com/ProgSys/pg_disatools/wiki/SCRIPT.DAT) (read 2026-11-25) gives the
only documented PC roles for `SetActorAnimationSet` (`67`) set IDs 0 to 11, with a combat base and a cutscene base each: 0 idle
standing (1000), 1 walking (1010), 2 fast walking (1020), 3 jump (1030), 4 punch attack (1500), 5 hurt (1060), 6 victory pose (1070),
7 skill (1080, monster only), 8 throw (1090), 9 victory jump (1050), 10 punch freeze frame (1610, monster only), 11 skill alt (1620,
monster only). Sets 3 to 11 share one base in both tables; only 0 to 2 differ (cutscene bases 0, 10, 20). A negative animation ID
in `SetActorAnimation` (`66`) mirrors the sprite, and `ContinueActorAnimation` (`68`) resumes an animation parked at a marker (the
sprite-sheet keyframe marker list is 0 empty, 1 loop start, 2 loop end, 3 auto-pause). The wiki's mode table contradicts its own
set table (combat mode "uses the cutscene table"), so treat the combat column as documentation-grade. **These bases equal, number
for number, the Complete state table above (states 0 to 11)**, which suggests Disgaea 1 Complete kept the PC numbering and added
state 12; the code-found roles in the Complete section fit the PC names for states 1, 2 and 3 and are not contradicted for 0 and 5, but
state 7 ("skill, monster only" on PC) is the fly-up in the Complete walker and the rest have no code-found role, so the PC names are a lead, not a label. Everything here is
Disgaea PC; the wiki's sprite-sheet parser code was not reached.

## Open questions

- Which of the two accepted executables (for example DRM-wrapped and unwrapped) each MD5 is.
- The combat state machine (pg_disatools reversed most of it for its Better Controls mod; not written up).
- Everything else for Disgaea 1 Complete.
- How many animation steps run per displayed frame and the exact update rate; which screen reads the older `start.dat` member 73 face set.
- Whether the Japanese voice banks are read, and whether the 25 characters with no selector entry have any other voice route.

## Sources
2026-11-24/25 source-format work (sprite, animation and voice chains of the Switch v0 executable): this wiki's offline checks of the retail files, including the game's own functions run in an emulator.
[pg_disatools](https://github.com/ProgSys/pg_disatools) by ProgSys: wiki pages Addresses, DisaPartyCharacter,
DisaKeyStates, DisaTScriptManager, DisaRenderObjectManager, BGM, Sprite offset list archive, memory allocation; source
`PG_DisaInstaller.cpp`. Read 2026-11-04. This project's private decomp index (function counts only).
