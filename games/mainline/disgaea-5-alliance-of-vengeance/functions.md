# Disgaea 5 Complete — Engine Functions

**Executable:** `disgaea5.exe`, x64, image base `0x140000000`, not packed, runs with ASLR.

## Which build an address belongs to

There are two PC builds, and code moved between them:

| Build | SHA-1 of `disgaea5.exe` | Notes |
|---|---|---|
| **A** – October 2018 launch | `436eefd2a29466e2ecaa5a57fd65255009896581` | D5 Mod Kit's reference build. Most published addresses are for this one |
| **B** – Steam, January 2019 update (adds Chinese and Korean) | `9460a6411699a256faaf0daed6b90d903a7d6491` | What Steam installs today. Seen with SHA-256 `49a7530baa1c3e15e4bf9c63c56fefd71d7e51a6e641bce80280b37b2db282ce` (disgaea5autoplay, Overlord research); that the two hashes are the same file is **Unknown** |

Unless a row says **B**, addresses are for build **A**. D5 Mod Kit doesn't hard-code build-B addresses: it
fingerprints the functions around each build-A address and finds them in your exe at install time
(`d5lib/exemap.py`). Do the same, or re-find addresses with the function names below, before patching build B.

Patches must use RIP-relative addressing: the game runs with ASLR, so an absolute `movabs 0x140...` crashes
(Kit CLASSES.md).

**Evidence:** everything here is **From decomp** (static analysis by the D5 Mod Kit author, eraser4521, unless noted).
Rows the Kit also confirmed in the running game say **Verified in game (Kit)**.

## Boot and file loading

| Function | Address | What it does |
|---|---|---|
| Data init | `0x14070EE30` | Opens the archives, creates one loader job per database table, loads script/talk |
| `Archive::Open` | `0x140408650` | Opens an archive set: `(archive, paths, count, multiLang, decrypt)`. Up to 12 slots per archive |
| Archive open job step | `0x1400AE8F0` | Reads the 0x3C-byte NISPACK header, checks the magic, reads the whole directory into memory |
| `Archive::Find` | `0x140408430` | Name lookup: linear scan, case-insensitive **prefix** match, first match wins |
| `Archive::Read` | `0x140408940` | Like Find, but skips entries with offset `0xFFFFFFFF` (tombstones) |
| De-obfuscate on read | `0x1403DA970` | `b = (~b >> 4) \| (b << 4)` on every byte read |
| Language setting | `[0x140C059D0] + 4` | `TextLanguage`: 0 JP, 1 EN, 2 FR, 3 CHN, 4 KOR (CHN/KOR use the EN archives) |
| `config.ini` reader | `0x1403DF5A0` | `[ConfigSettings]` and `[DbgSettings] EnableCrashReport` |

Archive objects: Start `0x141E53590`, Anime `0x141E561A0`, Map `0x141E552F0`, Object `0x141E57050` (three
language slots each), Database `0x141E54440`, Skit `0x1469CE6D0` (one slot).

**Lookup order** for multi-language archives: the text-language archive, then JP, then the rest.

**Start-up, build B** (read from original code in the D4 → D5 expansion research): `WinMain` (`0x140864970`) reserves
one **512 MiB heap** (published at `0x147B59E30`) that the game's own allocator (`0x14086D030`) carves up. Before that, `WinMain` installs its crash filter `0x140864A80` (at `0x140864A07`), which captures a stack trace with `RtlCaptureStackBackTrace`; hooks without proper unwind data confuse it. Start-up
`0x14013DDF0` then creates a named mutex (one running copy), initializes Steam (`RestartAppIfNecessary` with app ID
`0xC4310` = 803600), and allocates the `0xA40`-byte input / configuration manager (pointer at `0x140BD7B30`), whose
set-up reads `config.ini` and the window settings. The script manager is a separate `0x848`-byte object (pointer at
`0x140A67CF0`) with 256 root script slots. Script waits read the input manager, which is why scripts can't run before
start-up finishes.

## Database

| Function / data | Address | What it does |
|---|---|---|
| Table loader constructors | `0x1400C0040`–`0x1400C4400` | One per table |
| `char.dat` loaded callback | `0x1400C4200` | `qsort` by ID |
| `*DataBase.dat` parse | `0x140139D10` | Builds an ID index **in file order** (no sort) |
| `char.dat` table object / records / count | `0x140A31878` / `[0x140A31880]` / `[0x140A31888]` | |
| `char.dat` binary search (row / pointer) | `0x140061FA0` / `0x140061F20` | |
| `magic.dat` / `item.dat` binary search | `0x140062010` / `0x140061EC0` | |
| `comb.dat` binary search | `0x1400A0F90` | Unsorted IDs make it run away and hang startup (Kit CLASSES.md) |
| `OverlordSkill.dat` lookup | `0x1400987D0` | |

## Sprites

| Function | Address | What it does |
|---|---|---|
| `AnmCache::Request` | `0x1403FFEB0` | Checks the resident set, then loads `anm%05d.lzs` from the Anime archive. 97 call sites |
| Resident anm set | `0x140301730` | `0x401` slots of `0x150` bytes |
| Battle/field sprite | `0x14014DC60` → `0x14014DA30` | `anm = char.dat +0xD4 + colour variant (+0x379)` |
| Portrait / cut-in | `0x14021B290`, `0x14021B370` | `anm = 10000 + sprite_id (+ variant for generic IDs)`; special-cases generic ranges 1000–1310 and 2010–2300 |
| Portrait cache on unit | `0x14021E9D0` | Stores the portrait anm at unit `+0x2E36` |
| Item sprite | `0x14016BC40` | Uses `item.dat +0x56` |
| `Anime_TableDataBase` reader | `0x1400B2A90` | Its only reader; not used for character sprites |

## Saves

| Function | Address | What it does |
|---|---|---|
| Save write / load | `0x140749000` / `0x140748A60` | See [INDEX.md](INDEX.md#save-files) for the format |
| Save folder | `0x140749EF0` | `%APPDATA%\Nippon Ichi Software, Inc\Disgaea 5 Complete\<Steam account ID>\SaveData\` |
| Row index recompute | `0x14015EB80`, `0x1401DCCCC`, `0x140220E03` | Only on class change, never on load |
| New game: stage list | `0x1401B7970` | Fills the 400 stage slots from `dungeon.dat` |

## Script VM (`Data/Script/script.dat`)

| Function / data | Address | What it does |
|---|---|---|
| Script manager | `[0x140A95B90]` | 0x848-byte object; 0x100 thread slots of 0x268 bytes |
| Header parse | `0x14018E9B0` | `count`, `offset[count]`, `scriptId[count]`, bytecode |
| `StartScript` | `0x14018F4A0` | Linear search of IDs; the **last** match wins |
| `ScriptThread::Run` | `0x1401910C0` | Two-byte big-endian opcodes, binary-searched in the command table |
| Second dispatcher | `0x140162500` | Same table |
| Command table | `0x141D9D1A0` | 567 entries, opcodes `0x0100`–`0x8C10` |
| Table initializer | `0x14000F300` | |
| Variable resolvers | `0x1401920A0` (float), `0x140192AE0` (int/special, 251 cases) | |
| `CallScript` handler | `0x14008A000` | `u24 id`, `u8 argc`, ≤ 16 args |
| `If` handler | `0x14018C360` | |

The Kit decodes all 19,053 shipped scripts with this table (420 distinct opcodes). Opcode families: `0x01xx` flow,
`0x0Axx` variables, `0x14xx` flags, `0x46xx` sound, `0x50xx` maps, `0x64xx`–`0x66xx` actors, `0x6Exx` message
windows and portraits (`0x6ED1` plays a movie), `0x72xx` effects, `0x78xx`/`0x7Cxx` items and game state.

## Classes, recruiting and reincarnation

| Function | Address | What it does |
|---|---|---|
| "Is this class rank unlocked" | `0x1401C5790` (A) · `0x1401C6110` (**B**) | Starts with the generic-ID range test (`0x1401C611F`); anything outside 1000–1310 / 2010–2300 returns 0. Then the `char.dat` row's `dlc_group` must be ≤ 150 (`0x140141DE0`), and the class → flag lookup (`0x14008E0B0`, called at `0x1401C614F`) gives a flag ID from a table at state `+0xA1A4C4`; the answer is that bit of the game-flag array (`0x144FCCB0E`). Each rank is one flag (for example 17331–17336 for one family) |
| Class → unlock flag lookup | `0x14008D3F0` | |
| Unlock a rank | `0x1401C5920` | |
| Class carousel builder | `0x1406673B0` | Lists 47 fixed families (Hire Character, Reincarnation). Corrected 2026-11-20: the Strategy Assembly's Set a Sub Class screen has its own 47-family builder (`0x1404707E0`, below), so the kit's 512-entry carousel does not lengthen it (**Verified in game**, [below](#sub-class-list-seen-in-play-and-the-current-sub-class-panel)) |
| Carousel descriptions | `0x140667C61` | From `StringListDataBase.dat` |
| Class tab family list | `0x1402A36B6` | |
| Hire list builder | `0x140664DF0` (**B**) | Writes `{class, highest unlocked rank}` entries; skips a family whose first rank is locked. Shared by Recruiter and Rebirth: **47 inline 8-byte entries** at window `+0x90`, count at `+0x208`, cleared (`0x178` bytes) by `0x140663B20`. D5 Mod Kit moves them to a 512-entry buffer. Draw sites that read the list (**B**): Recruiter class / neighbour / rank count `0x1406669A3` / `0x140666C4A` / `0x140667159`; Rebirth `0x140471600` / `0x14047188B` / `0x140471CE9`; wheel appearance loop `0x140666920` |
| Recruiter rank row | `0x140661E60` (**B**) | Offers exactly that many ranks |
| Recruiter description buffer | message object pointer `0x140BE41B8` (`0x107D0` bytes, built by `0x14013FA30`) | The selected class's description (looked up from its family by `0x140668650`, call `0x140663187`) is copied byte by byte with no length check (`0x140663200`) into `+0x1C2C…+0x1D2B` (256 bytes). After it: a dirty byte `+0x1D2C`, two layout floats `+0x1D30` / `+0x1D34` (17.0, 16.0), and the list-pane pointer `+0x1D38` that the menu calls through its vtable (`+0xA8`). Text past 256 bytes overwrites those |
| Reincarnation window | `0x14047F1C0` (**B**) | Reads the same count. The window is a `0x23DB8`-byte object with vtable `0x14098BC90`, created by the scene dispatcher `0x14044A270` (case `0x0D`) called from the assembly screen `0x140442BC0` |
| Party preview rebuild on class change | `0x14067E7D1` | Build not stated (Kit OUTFITS.md) |

The "is this a generic class" test is inlined in about 60 places, not one function (Kit CLASSES.md). One form of it is
`lea eax,[rcx-2010]; cmp eax,290`, i.e. monsters 2010–2300.

More build-**B** anchors from the D4 → D5 expansion research (original code run in an emulator):

| Function | Address | What it does |
|---|---|---|
| `char.dat` lookup by ID | `0x140061F20`, `0x140061F80` | Binary search of the sorted table; 0 for a missing ID |
| Unit row-cache refresh | `0x14015F930` | Re-finds a unit's row index (`+0x2E0A`) and graphics (`+0x2E36`) from its class ID |
| Status-screen portrait key | `0x14021BE10` | |
| Class carousel | — | Holds up to **512** entries; 47 vanilla families + 45 imported ones were tested to fit |

**Flag budget for added classes:** D5 Mod Kit gives each added class six unlock flags (one per rank) in the
event-flag range **17283–17999**: 717 flags, so about **119 added classes** at most before the range runs out.

## Class mastery and sub-classes

Source: D5 Mod Kit development fork (`docs/MASTERY.md`), not yet in the public Kit.

| Function | Address | What it does |
|---|---|---|
| Family → mastery slot | `0x14021EBE0` | Returns 0–46 for the 47 vanilla families, −1 for anything else. Called from 16 places: `0x1400B3CE8`, `0x14015F500`, `0x140217241`, `0x140217326`, `0x1402A3A5D`, `0x1402A3B00`, `0x1402A3DD5`, `0x1402A3E84`, `0x1402A3F34`, `0x1402A3FE4`, `0x1402A4094`, `0x1402A4144`, `0x1402A43CC`, `0x14047FFC8`, `0x1407CDA8C`, `0x1407CDAD9` |
| Mastery reader | `0x140217230` | Bound `< 0x35` (53 slots) |
| Sub-class ID check | `0x140160E20` | Accepts only 1000–1310 / 2010–2300 (the 16-byte range test at `0x140160E79`; accept `0x140160E89`, reject `0x140160E8C`) |
| Rank from class ID | `0x14021EE80` | Returns `id % 10` for any ID above 999, so it already works for added classes |
| Strategy Assembly sub-class screen | `0x1404707E0` | Builds its list from a hard-coded 47-family array on the stack (family fetch at `0x140470BD0`, loop bound `0x2F` at `0x140470DB7`). The screen object is `0x8FC0` bytes (`0x14043E820`) with **47 rows of `0x238` at `+0x2768`**, ending exactly at the saved sub-class field `+0x8FB0`, so there's no room for a 48th row |

**Six stars per class is a hard limit:** six counters per slot, the mapper's rank bound, the UI and a 3-bit clamp all
assume it; a seventh rank would need new fields. Stat caps don't apply to the mastery bonus itself, only to final stats.

**How mastery is awarded** (build **B**; original code run in an emulator, D4 → D5 expansion research, **Verified
offline**). Damage rewards (`0x1401B0910`) fan out (`0x1401516F0`) through the sub-class selector (`0x140160E20`) to
the writer `0x14015F480`:

- The amount is clamped to **0 … 999,999**; negative awards count as 0.
- If the current class is in 1000–1310 or 2010–2300 and the sub-class isn't, the **current class** gets the progress.
  Classes outside those ranges get none through this path, which is why imported classes can't earn mastery.
- Counters: `unit + 0x30A4 + 24 × slot + 4 × rank`. Thresholds come from a sorted table of 20-byte rows (threshold
  `u32` at `+4`, eight bonus bytes at `+0x0A…0x11`).
- A rank pays out when a counter **reaches** its threshold. The eight bonus bytes are added to the `u16` bonuses at
  `+0x359C`, which **wrap at 65,536**. There's no separate "already awarded" memory: lowering a counter by hand and
  refilling it pays again.
- A class with no threshold row is a null read (crash at `0x14015F5F2`); rank digits 6–9 aren't guarded.
- **Which ranks one award touches:** the writer keeps the target class ID it was given and awards ranks 0 up to
  `ID % 10` (`0x14021EE80`). A counter that doesn't complete keeps the award unchanged for the next rank, so passing a
  rank-5 ID with 7 XP adds 7 to all six counters at once; only a completion turns the award into its overflow. The game
  itself always passes the unit's current rank, so this only shows when a mod passes another ID. Pass the family base
  (`ID − ID % 10`) to fill one star at a time.
- Completing a rank has one global side effect: it marks the class as seen (`0x1401C62A0`), and that function starts
  with the generic-ID range test, so for classes outside 1000–1310 / 2010–2300 it does nothing at all (traced write by
  write in the emulator; the global flag bitmap stayed byte-identical).

## Evilities, stats and damage

The functions where evility effects are applied. Source: D5 Mod Kit development fork (`docs/EVILITIES.md`). Names here
are plain descriptions written for this wiki, not labels copied from anyone's private database.

| Function | Address | What it computes |
|---|---|---|
| Equipment potency | `0x1401E1B30` | Equipment stat potency %, clamped 10–300 |
| Percent stat evilities | `0x1401B97D0` | Percent stat evilities. Called at `0x1401B9575`, `0x14021E19B`, `0x1402AA5E3` and `0x1402B0494`; the unit recalc calls the equipment-potency pass at `0x14021CD02` |
| Growth bonus | `0x1401E9B50` | Level-up growth. Called from the unit recalc at `0x14021C15D`, **only inside its level-up loop** (level below 9,999 and enough EXP), not on every recalc |
| Stat overrides | `0x1401EEE30` | Final stat overrides (recalc call `0x14021E79F`) |
| Damage-dealt rules | `0x1401F8FD0` | Damage dealt (caller uses `100 + return`) |
| Damage-taken rules | `0x1401FB2E0` | Damage taken |
| Hit-chance rules | `0x1401F5410` | Hit chance, clamped 1–100 |
| Ailment-chance rules | `0x1401FF070` | Ailment chance |
| Nullify check | `0x1401FE0D0` | Whether a hit is nullified (called at `0x1401BF0F0`, `0x1401BF6DD`, `0x1401C0A30`, `0x14060B657`) |
| Crit-rate pass | `0x1401F60A0` (call `0x1401C1E94`) | Critical %, clamped 0–100. The caller (`0x1401C1E80`…`E94`) passes a pointer to the running value, the attacker's and defender's battle records, and the action context and target index on the stack |
| Attack / defence bonus rules | `0x1401F6300` / `0x1401F8700` | Attack bonus % (attacker battle record; its unit record at `+0x240`) / defence bonus % (defender's unit record) |
| Move range, range bonus | `0x140157D90` / `0x1401EC5C0` | Both take the unit's battle record |
| Human / monster, gender | `0x14015D440` | Reads the normalised type byte at unit `+0x2E25`; learned evility 4301 swaps the gender |
| Healing | `0x1401BF3C0` (entered from the target computation at `0x1401BE900`) | Adds a healing percentage from `0x1401FFA50` (call `0x1401BF493`), then applies map-wide hostile modifiers multiplicatively in `0x1401FFF40` (call `0x1401BF503`); a sign-flip check `0x1401C0840` (call `0x1401BF61A`; the non-healing path calls it at `0x1401BEE08`) decides whether the amount turns into damage |
| Flight-like evility check | `0x140206160`, `0x1402062F0` | Two copies |
| Counter permission | `0x1401ECE00` (site `0x140547333`) | Whether the defender may counter |
| Per-turn HP/SP | `0x140551AC0` (called at `0x140554B9E` and `0x1405FB5DF`) | Per-turn HP/SP changes, run at the round edge. After changing HP outside it, call `0x140151C50` (record, new HP) to refresh the HP bar's displayed value |
| Active evility list sort | `0x14021878C` | Runs just before the unit's list (`+0x2F30`) is sorted |

**Two consumers measured exactly** (build **B**, emulated original code):

- **Equipment-aptitude evilities** (IDs 1011 HP, 1021 DEF, 1031 INT, 1041 SP, 1081 RES, 1091 SPD, 1111 HIT, 1191 ATK)
  are applied in `0x1401E1B30`. Each one present adds its value (word at `+0x38` of its `Skill.dat` row) **once**, however
  many copies the list holds, to a 16-bit equipment-potency word in the unit (`+0x3068` HP, `+0x306A` SP, `+0x306C` ATK,
  `+0x306E` DEF, `+0x3070` INT, `+0x3072` RES, `+0x3074` HIT, `+0x3076` SPD), then clamps it to **10–300** (bounds at
  `0x140A18780` / `0x140A18788`).
- **Damage evilities such as Physical Boost (4053)** add `min(value, 200)` to one shared signed modifier
  (`0x1401FA732`). The shared modifier is clamped to **−50 … 2000** (`0x1401FB226`), then the caller adds 100
  (`0x1401C16A2`) and the damage code applies that percentage with integer truncation. Duplicates count once. (D4
  instead multiplies the damage immediately for each evility, so the same numbers give different totals: see
  [D4 functions](../disgaea-4-a-promise-unforgotten/functions.md).)
- Equipment fields that feed the active list sit at `+0x138 + n × 0x258` in the unit (five equipment records).

**Three evility mechanics, as the code does them** (installed Steam exe, read in the D5 Mod Kit fork's decompile;
**From decomp**):

- **"Defending"** (Advance Guard 1152, and Synch Guard 1151 per adjacent ally): the check `0x140157870` takes the
  battle unit and is true when its **action stack** (count, signed 8-bit at `unit+0x1080`, at most 60; 20-byte entries
  at `+0xBD0`: type, x, y, z, unit, facing, steps) holds an entry of **type 4**, or when one of three fallback flags is
  set (`+0x11C0` on a non-zero team, `+0x11E0`, or a byte in the unit's stat record). Advance Guard then multiplies the
  damage by `(100 − value) / 100` in floating point (`value` = `Skill.dat` `+0x38`, 50).
- **Kill counts** (Queen of Coffins 140): kills are tallied **per side and per battle** in six 16-bit counters at
  `0x146A5F054`, indexed by the *defeated* unit's side (record `+0x2E32`) and cleared at battle start (`0x1404AC930`).
  The evility sums all six (each capped at 200) into the shared attack modifier. Nearby counters: `0x146A5F048`
  (per killer's side, cap 9,999) and `0x146A5F060` (cap 99). Compare D4's Bloody Battle, which counts per unit index.
- **Absorption** (6032): applied once, in the defeated-unit branch of the after-hit code (`0x1401AEB10` → `0x140200D20`):
  a percentage of the defeated unit's six stats goes into a persistent per-character store (`+0x5A8` of the character's
  stat block), which the stat-override pass (`0x1401EEE30`) then adds to the record (`+0x2CE8…+0x2D10`). **It only works
  for character IDs below 650**, so imported characters with higher IDs get nothing from it.

- **Attack direction:** stored once per target when the action context is built, at target entry `+36` (context
  `+0x133C + 0x260 × target`). `0x1402D8880` computes it from the **target's facing** (`+0x2C4`, degrees) and the
  attacker's cell offset: angle within ±43° of straight on = **0 front**, 137°–223° = **2 behind**, otherwise **1 side**;
  attached objects are always 0. The hit roll, the critical multiplier and rules such as All Digger (3013, +30% from
  behind) read this value. Careful: at least one widely used annotation of this function swaps 1 and 2; the code returns
  2 for behind.

**Ailment chance and elemental resistance** (installed Steam exe, D5 Mod Kit fork research): the ailment-chance rules
(`0x1401FF070`) have a single caller, `0x1401BF810`, which is reached from target damage (`0x1401BE860`), buff
computation (`0x1401BFAE0`), the plain-attack ailment pass (`0x140220C10`) and the battle forecast (`0x1404B06D0`); each
passes the action context (target count at `+0x1310`). Elemental resistances come from the percent-stat evility
function (`0x1401F2A10`, called from the unit recalculation at `0x14021D964`): it writes the three element-resistance
words, which is how Fire / Windy / Cool Body (4008–4010) and Elemental Rise (1161) work, so a mod can add resistance
the same way.

**Where a hit lands** (build **B**, emulated original code; a map for anyone hooking kills or damage):

| Step | Address | What happens |
|---|---|---|
| Apply an action to one target | `0x1401B1FD0` | Reads the target's HP (signed 64-bit, `unit+0x2CC8`) before damage into its frame; a target already at 0 HP is marked and skipped |
| After damage | `0x1401B29AB` | Checks the new HP and flags; HP ≤ 0 goes on to the outcome step (skill kinds 5 and 6 skip it) |
| Outcome callback | `0x1401AEB10`, called at `0x1401B2A34` | Arguments: attacker actor, target actor, action, target index, flags. Kill bookkeeping starts here |
| Attack modifier clamp | `0x1401F86AF` | Just before the inner clamp (`−50 … 2000`) of the shared damage modifier |
| Effective side / group | `0x14015B910` | A unit's raw side (0 / 1) can become another group number under roster control, so don't use the effective value as a team ID |

Hooking only the outcome callback misses kills that take other routes (some skill kinds, SP deaths, flags).

### Where the game checks for evilities

A survey of the Steam build (exe SHA-256 `49a7530b…db282ce`, so **build B** addresses) found **416 functions** that test a
unit's sorted learned-evility list (unit/stat record `+0x2F30`, count at `+0x3030`) for hard-coded evility IDs:
**1,149 tests of 666 distinct IDs**. Two mechanisms:

- **611 inlined tests** in 192 functions: the compiler inlined a binary search over the list, once per ID.
- **544 calls** in 211 functions to a shared "has any of these evilities" helper at `0x1402068B0` (**B**).

The full list, by evility ID, is on [evility-consumers.md](evility-consumers.md). So adding behavior to an evility ID means patching code wherever that ID is tested, or hooking these consumers (what
the D5 Mod Kit fork's extension does). Consumers by area:

| Area | Functions | ID tests | Distinct IDs |
|---|---:|---:|---:|
| Unnamed / not yet classified | 252 | 492 | 252 |
| Damage calculation | 44 | 338 | 309 |
| AI decisions | 23 | 27 | 7 |
| Command-menu predicates | 15 | 21 | 15 |
| Carry / throw | 13 | 20 | 14 |
| Turn order and round upkeep | 12 | 21 | 18 |
| Evility stat aggregation (potency, growth, status, list rebuild) | 10 | 154 | 149 |
| Movement / pathing | 9 | 11 | 10 |
| Battle model state | 8 | 16 | 16 |
| Deployment | 7 | 7 | 5 |
| Script commands | 5 | 8 | 6 |
| Battle flow | 5 | 5 | 5 |
| Unit stat aggregation | 5 | 6 | 5 |
| Battle setup, world map, results, misc | 8 | 23 | 21 |

Every ID was re-checked against the executable's bytes (**Verified offline**); the area labels come from community
function names that aren't published here.

## Overloads and Revenge

| Function | Address | What it does |
|---|---|---|
| Overload of a unit | `0x140160810` | Override at unit `+0x309C`, else `char.dat +0xCC` (via `0x14015CE30`) |
| Overload ID → skill ID | `0x1401DD050` | IDs 1–19, 100–114, 200–213 → `magic.dat` 3000–3047 |
| Usability / cost | `0x1401DC5C0` | |
| Overload code dispatch | `0x140163BF0` | Battle record `+0xE60` = Overload in use |
| Title banner | `0x1404B2FC0` | UI block 9032, animations 100–500 |
| Revenge gauge check | `0x14015BDD0` | Unit `+0x35BC` (u16); 10000 = Revenge Mode |

## Voices

| Function | Address | What it does |
|---|---|---|
| Voice set | `0x1401602C0` | `kind × 100000 + voice_id × 10 + personality` (personality = unit `+0x2E26`) |
| Line pick | `0x140226C00` | Random row of the voice table with matching group and situation flag; linear scan, ≤ 512 lines per set |
| Line by ID | `0x1400ADF00` | Binary search: table must be sorted by ID |

## Maps and stages

| Function | Address | What it does |
|---|---|---|
| Map loader | `0x14053F0B0` | |
| `.hex` terrain loader | `0x1402272D0` | Accepts versions 3–7 (all shipped files are 6) |
| Terrain object motions | `0x140239EC0` | |
| No-panel checks | `0x140147E6C`, `0x1406FCDF0` | |
| Stage list (Dimension Guide) | `0x140647450` | Walks the 400 stage slots |
| DLC stage check | `0x140141710` | |
| Stage panel `DemoCheckDataBase` lookup | `0x14063CA4B` (**B**) | Reads without a check: a stage missing from `DemoCheckDataBase.dat` **closes the game** when the cursor reaches it |

## Effects

| Function | Address | What it does |
|---|---|---|
| Effect model preload | `0x14070EDA0` → `0x1404002C0` | Loads every `EffectTableDataBase` model into a 512-entry cache, resource slots 700–1099 |
| Palette selection (build **B**) | `0x1402FCCA0` | An effect's palette request (from the effect row) is capped at 107, then clamped to the texture's real palette count. Same rule as D4's `0x14045D790` |
| Crash past the limit | `0x1402FB302` | Room for 400 models (vanilla has 383). Past that the game crashes here at startup. The Kit's EXE_ANALYSIS says the 401st model crashes; its SKILL_ANIMATIONS says 402 did |

## Runtime state and hook sites (build B)

Addresses the D5 Mod Kit development fork's runtime (`d5native`, its auto-battle, effect cache, colour and input code)
reads or hooks. They're declared for build **B** (`D5_BUILD_9460A6411699`) and used through ASLR as
`module base + (address − 0x140000000)` (**Verified in game** by that runtime's use, not separately here).

| What | Address | Notes |
|---|---|---|
| Unit actors | `0x141BA33B0` | 256 × `0x1260`. `+0x08 / +0x0C / +0x10` position (x, height, z as floats; panel = value ÷ 12), `+0x1D0` non-null = live, `+0x240` unit record, `+0x2E8` active byte, `+0x2EE` link to the unit carrying it (lift); evility adjacency counts use Manhattan distance on these panels |
| Light / event actors | `0x141CC93B0` | 512 × `0x3E0` |
| Map cursor actor | `0x141E2CD60` | Same position layout |
| Base panel / Item World gate | `0x144FAC6D4` / `0x144FAC6DC` | x, z pairs |
| Path grid | `0x140B7AA30` | 128 × 128 bytes `[x][z]` from the selected unit: steps, `0xFE` blocked, `0xFF` unreachable |
| Path cost flood | `0x1402D5EE0` (expand step `0x1402D5390`) | When the unit's byte `+0x2E2A` is 2, every ring gets cost 1 (D4's flood, `0x140238060`, increments per ring instead) |
| Panels moved this turn | battle record `+0x61C` | Set when the player commits a move (`0x1404CF540`, from the destination's path cost) or the AI finishes one (`0x1405F9FB0`); restored on undo (`0x1404D0160`), cleared at turn end (`0x140159F50`). D5's own movement evilities read it |
| Battle map loaded / generated Item World floor | `0x144FAC6C0` / `0x144FAC6C1` | u8 each |
| Item World session / flags / floor | `0x146A07FA8` / `0x144FCD261` / `0x141E4FF80` | Flags: bit 0 on a floor, bits 2–3 on the post-boss screen. Floor number unverified |
| Scene mode | `0x146A0D000` | 2 player turn, 6 AI turns |
| Enemy-turn team picker | `0x1405FAC30` (team selection), `0x1405FACC4`, `0x1405FAD62` | Loops teams 1–5 over "team done" bytes at manager `+0x135`; the player's team 0 has its own byte at `+0x134`. Starting the loop at 0 makes the game's AI play the player's units (how auto-battle works) |
| Script start | `0x140190140` | |
| Effect slots (7000 + i) | `0x141D72C90` | 256 × `0x358` |
| Effect cache | pointer at `0x140BD7BA8` | 512 × `0x30` entries; resource manager pointer `0x146A06D58`; model instances 512 × `0x258` at `0x141DAA0D0` (`+0x218` active, `+0x180` manager slot) |
| `EffectTableDataBase` in memory | count `0x140A04ED8`, IDs `0x140A04EE8`, 8-byte rows `0x140A04EF0` | Sorted IDs |
| Show / look up an effect | `0x14018E690`; `0x1401A95E0`, `0x1401A8EC0`, `0x14008BA70` | The Kit's on-demand loader hooks these when a lookup misses |
| `char.dat` in memory | rows pointer `0x140A03880`, count `0x140A03888` | 908-byte rows, ID at `+0xD0` |
| Roster unit 0 | `0x145207DB8` | 128 × `0x4750` (build A: `0x145235C58`). Addresses this far out lie past the executable file's size, so tools that translate addresses by file mapping must handle them separately |
| Camera state / draw manager | `0x146A0CD60` / `0x146A06D48` | |
| Volume masters | `0x140BD7B60`, `0x140BD7B68`, `0x140BD7B70`, `0x140BD7B78` | Four masters, set to 1.0 at boot by their constructors (`0x1400639F0`, `0x140063BA0`, `0x140063A90`, `0x140063C50`); loading a save sets them from Options 20–23 (value ÷ 10). Setting them to 0 (memory only) silences the game without touching saved options |
| Main scene update | `0x140221D50` (first call inside it at `0x140221DBF`) | Runs every frame before the script VMs and sound; a good per-frame hook point |
| Quest table in memory | `0x140A03B50` / `0x140A03B58` | 5,636 bytes per row, in board order |
| Skit fast-forward options | `0x14699FA19` (always), `0x14699FA1A` (×2 / ×3 / ×4), multiplier `0x140A02B34`; set at `0x1404CED63`, read per frame at `0x1404CED2E` | Fast-forward draws only every *n*-th frame; skipped frames bypass the 60 fps limiter |
| Colour-change screen | vtable `0x14098B7D8`; list complete `0x1404550BB`, choice under cursor `0x140456F00`, confirmed `0x14045492C`; add list entry `0x14008B6B0`; rebuild preview `0x140148520` | Where the Kit adds "Custom Color N" (value 100 + N) |
| State-machine runner / draw one string | `0x140861E60` / `0x1403FACE0` | Every UI screen runs through the first; the second has 377 callers |
| Screen tasks | setter `0x140409470` (writes the task ID through the pointer at `0x140BDFD08`) | Each screen is a numbered task: 13 base, 9 world map, **36 Recruiter**, **7 Strategy Assembly** (Reincarnation is reached there: Call Assembly → a character → Topic List → *Reincarnation*; it isn't on the Recruiter's menu, verified in game); the Quest Shop is a sub-state (17) of the facility menu. Setting a task directly opens a half-initialised screen (see [modifiable](modifiable.md)), so prefer the player's route |
| DirectInput keyboard device | pointer at `0x147B100A0` | When it's non-null the game reads keys through DirectInput's immediate `GetDeviceState`, so a hook on `GetKeyboardState` alone won't see injected keys |
| C runtime fast-fail | `0x140116224` (inside `0x14011620C`, reached from `0x1401161DC`) | An `int 0x29` invalid-parameter fast-fail: where the open Skill Trainer crash ends up. The caller that passes the bad parameter isn't known yet |
| Texture-upload worker pool | worker `0x1400D89C0`, uploads `0x1400A70A0`, queue pointer `0x140BE41D0`, drain `0x140867F90`, binder wait loop `0x1403D4CB0` | Twelve workers. The Hire Character freeze with many class packs stalled here; the cause was byte-swapped image headers exhausting the pool ([modifiable](modifiable.md)). The frame thread reaches the binder through return addresses `0x1402235C1` (frame update) → `0x14030C888` (layer draw) → `0x1403D6D14` (shader draw command) → `0x1403D4CB0` (live Wine backtrace, 2026-10-30) |
| Texture record (what the binder waits on) | `0x58`-byte records | `+0x00` slot, `+0x08` byte size, `+0x20` width × height, `+0x24` format, `+0x30…+0x40` uploaded data, `+0x50` pending upload task (vtable `0x14098A2A0`; the task's `+0x40` points back at the record). The binder loops while `+0x50` is non-zero. The drain loops while the queue's list head (`+0x28`) isn't its sentinel (`+0x40`) or the in-flight count `+0xE4` is non-zero (live memory reads, 2026-10-31) |
| Keyboard state | `0x147B100A8` | 256 bytes the input update fills every frame (from `GetKeyboardState` or DirectInput). Read this instead of calling Windows from a hook: a call inside the menu thread hung the class-list builder |
| Imports patched | `XInputGetState` `0x1408CE710`, `XInputGetCapabilities` `0x1408CE718`, `GetKeyboardState` `0x1408CE670`, `GetForegroundWindow` `0x1408CE600`, `GetCursorPos` `0x1408CE5C0`, `CreateFileA/W` `0x1408CE050` / `0x1408CE4F8` | Import-table slots, so a mod can wrap input or file access without code patches |

**More build-B anchors from the D4 → D5 expansion research:**

| Function | Address | Notes |
|---|---|---|
| `Skill.dat` definition lookup | `0x14008D0F0` | Used by every evility consumer |
| Squad membership / squad member count | `0x1401D44F0` / `0x1401D3990` | Unit squad list at `+0x30A0`, count byte `+0x30A1`; the counter searches the 20 stored squad unit serials and counts those present in the front roster |
| Neighbour counter | `0x1401F4020` (forced slow path `0x1401F4190`) | `(out, evility ID, reference actor, relation, range, flags)` → count. The slow path walks the 256 battle records, skips inactive and (flag bit 1) carried units, compares footprints with `0x140153F30`, and for relation 2 keeps hostile teams. Passing an evility ID tests the sorted status list at `+0x2F30` (`0x1402068B0`) |
| Unit recalc → resistance evilities | `0x1401F2A10`, called at `0x14021D964` | Receives the unit, battle context, an 18-dword modifier accumulator, HP % and SP % |
| Evility definition value | `0x140200760` | `(ID, selector)`; returns raw fields, no rounding (Absorption reads it at `0x140200F27`) |
| Damage percentage | `0x1401C1650` | Calls the modifier rules (`0x1401F8FD0`) and adds 100; `0x1401BE860` applies it |
| Attack / defense stat selection | `0x1401CE1E0` | Chooses the stats from the skill's formula and sub-type. Called at `0x1401BEA2C` (attacker record, skill row or null) inside the target computation `0x1401BE860`, which serves both the forecast and the real hit. Selector 1 = attacker ATK vs defender DEF, 3 = INT vs RES; the result feeds attacker stat `0x14015D760` (call `0x1401BEA4A`) and defender stat `0x14015D6F0` (call `0x1401BEA5A`), and a separate defender override `0x140200310` can replace the defender's stat |
| Voice group lookup | `0x140160280` | Throw controller `0x1406335C0` plays situation 11 |
| Script manager create / free | `0x14013FA30` / `0x14013F1D0` | |
| Heap set-up / allocator | `0x14086B620`, `0x14086CC80` / `0x14086D030` | |
| Start-up config / display mode | `0x1403DF0F0` / `0x1403DF710` | |
| Roster: dismiss / move to end / append copy | `0x140654E00` / `0x1402E7ED0` / `0x1402E7D30` | Appending a copied record doesn't make a new serial |
| Battle-only unit copy | `0x140158A80` | Keeps only the low 16 bits of the serial |
| Serial reassignment | `0x1404D0B90` | |
| Whole-state copy (load) | `0x1400C89B0` | |
| Other mastery award callers | `0x140529760`, `0x14082A440` | Besides damage rewards |
| Active-list builder | `0x140218C90` (build A: `0x140218090`) | |
| Evility bank counts | `0x14015E0E0` | Reads `unit+0x2F24` / `+0x2F26` (signed 16-bit) |
| Stat-route modifier | `0x1401F4D80` | Basic-attack stat route; the formula's selector isn't always final |
| Skill-type query | `0x1401C1EE0` → `0x1401D78E0` | Passes the selector byte `0x6D` |
| Class-rank lookup | `0x1400744D0` | `(owner + class_order − 1)`; the Skill Shop calls it without a null check (the source-3 crash) |
| Graphics ID for a class and palette (earlier listed here as a `char.dat` row lookup) | `0x14021F4A0` | See [Graphics ID](#graphics-id-which-face-a-unit-shows) |
| Unlock helper | `0x140143720` | Reached after a rank unlock writes its flag |
| Memory copy | `0x14010E680` | Used for whole-unit and whole-state copies |
| Security-cookie check | `0x1400EFDE0` | Compiler stack check |
| Battle scene entries | `0x1404AC930` (common) / `0x1404ABC80` (normal) | The common entry runs first on every battle start |
| Record pool user | `0x140710AF0` | Its operation 11 takes an unused record from the pool at `0x144FCD538` |
| Script completion | `0x14018EC10` | Entered when a script context finishes |
| Command constructors / wrapper | `0x1400871A0` (form 0), `0x140081C30` (`65A0`); wrapper `0x140087170` | |
| Mastery writer gate | flag `unit+0x2E32` or global word `0x145574B28` | Either enables mastery writes |
| Lift controller sites | `0x1404D1589` (carrier unit from actor `+0x240`), voice `0x1404D160B`, attach `0x1404D17CE` | |
| Texture creation / binding | `0x1400A70A0`, `0x1403D4AA0` / `0x1400AB120` | |
| Effect loading chain | loader `0x1404000C0` → queue `0x140400F20` → completion `0x1400AF850` → OBF copy `0x140306760` → textures `0x1402FB7B0` → entry `0x140310970` → payload `0x14030FFF0` | The payload parser takes DDS or a 16-byte TXF header; D4's legacy `nismultitexform` textures aren't recognized here |

**Unit record fields used there:** `+0xBC8` name (UTF-8), `+0x2DFC` level (u16), `+0x2CC8` HP, `+0x2CD0` SP, `+0x2CD8` max HP,
`+0x2CE0` max SP, `+0x2CE8` onward ATK DEF INT RES HIT SPD (i64 each), `+0x2E04` class ID, `+0x2E08` AI pattern (s16; −1 =
player-controlled).

### More anchors (build B), from a sweep of the research notes

Collected 2026-11-06 from the D5 Mod Kit fork's reports; each was read from the executable or its decompilation
(**From decomp** unless marked), and the roles are described here in plain words.

| Area | Address | What it is |
|---|---|---|
| Tables sorted on load | `0x1400C1820` (20-byte rows, e.g. `JobRank.dat`), `0x1400C4D20` (`charhelp.dat`, `0x594`), `0x1400C3180` (`charPersonal.dat`, `0x15C`), `0x1400C4FC0` (`magic.dat`, `0x474`) | Each `qsort`s its table by the `u32` key at `+0`, so these tables, too, can be in any order on disk |
| Innate evilities applied | at unit set-up only: `0x14021A970` (creation), `0x140217280`, `0x140219040` (slot count read at `0x14021B9DE`) | Changing a class's innate evilities in `char.dat` doesn't change units that already exist until they're set up again |
| Unit from roster / roster moves | `0x1401C5EC0`; to reserve `0x1402EA000`, back to the front `0x1402EA200`; class → first front unit's serial `0x14021BCE0` | |
| Battle skill list | `0x1402D0820` | Walks the unit's own skill slots (`+0xCC4`) and accepts skill groups 8–10 |
| Valvatorez's skills pinned | `0x1401D68A0` | The battle command menu's skill list skips `magic.dat` 10571–10573 for anyone but character 570 (the status screen still lists them), so another character given those skills can't use them; give it copies under new IDs |
| Body-type checks | appearance `0x14014EA10` (reads `char.dat` `+0x386`), weapon-animation loader `0x14016CA30`, equipment menu `0x1402796DA`…`0x140279754`, path restrictions `0x140198990`, Magichange item lookup `0x14017A8D0` | Where humanoid vs monster vs object changes behaviour |
| Lift, carry, throw | lift `0x1404D1410` → attach `0x140154D40`; carry-stack depth `0x1401547C0`, stack root `0x1401548A0`; throw landing map `0x14053C730`; pose reset `0x140158580` | Lift sets poses 110 (carrier) and 106 (carried); while holding, the pose reset gives the carrier 113 and the carried unit 106 |
| Extra attacks | `0x14053FBAF` | The repeat / extra-attack decision; it returns an index (−1 or a tally index), not a yes/no |
| Random number | `0x140136660` | The game's random draw; percentage checks call it with 100 |
| Turn upkeep | SP recovery % `0x1401EE010` (called at `0x140552083`); end-of-round upkeep `0x1401E5AF0` (called at `0x1405F8FFC`) | |
| Voice calls | attack `0x14060B7F0` (situation 0); hop landing `0x14052E920`, `0x14052D500`, `0x140853720` (situation 13) | |
| Sound | per-frame sound update `0x1403F45A0` (inlined in the scene update at `0x140222992`); stop all sound effects `0x1403F7210`, messages `0x1403F6060`; message voice `0x1403F5B60` | After a save loads, `0x1401CC6C0` re-derives the four volume masters from Options 20–23 (÷ 10; stores at `0x1401CC716`…); the Options screen (`0x1402724A0`) is the only other writer |
| Movie player | `0x1408958D0` | Loads Media Foundation (`Mfplat.dll`, `Mf.dll`); this is what plays the opening movie that can hang under Wine |
| Archives | member lookup by hash + name `0x1408770B0`; `Effect.dat` opened at boot (`0x14070C10C`, through `0x140400320`) | |
| Sprite loading | LZS load step `0x1400AF5A0`, decoder `0x140408EA0` (on a worker thread), sprite acquire `0x1403FFCB0`, package load step `0x1402EE3D0`, release `0x1402EEE00` (waits for an outstanding decode, `0x1403D98B0`) | The load step answers "try again" with no error, so a sprite member that never arrives makes the caller wait forever instead of crashing |
| Texture uploads | pool at `0x146A06D50` (u16 count `+0x10`, `0x58`-byte records; constructor `0x1400A6FD0`, destructor `0x1400A7000`, created in `0x140310660` with the decode mutex); queue append `0x1408672B0`, flush `0x1408670D0`, draw queue set-up `0x14030BE50`, texture factory `0x14087C210` | |
| Input | key state filled by `0x14089D890`; input poll `0x1408A50C0`, called from the per-frame update `0x1402211E0` | |
| UI objects | Yes/No box vtable `0x14098A038` (slot 2 → `0x14009BD90`); OK notice vtable `0x14098A0B0` (slot 2 → `0x14009A310`); battle main task vtable `0x14098BF00` (slot 9 → `0x1404AD390`); stage selector vtable `0x14098D018` (slot 9 → `0x140646040`); end turn `0x1404A7150`; scene 8 update `0x1404A0150` | The vtable slots an automation mod would watch |
| Recruiter | enter `0x140652060` → draw `0x140652460`; hire dispatcher `0x1406649C0` (sub-state 0 = class grid); cursor commit `0x1406678D0` (portrait `color_id + 10000`); draw class / neighbour / rank count `0x14066ACE2` / `0x14066AF8F` / `0x14066B4C7` | Reincarnation's copies: `0x1406669A3` / `0x140666C4A` / `0x140667159`; its window (vtable `0x14098BC90` slot 1 → `0x140483710`) clears the same list (`0x140663B20`) and fills it with the shared builder `0x140664DF0` |
| Shops | Skill Shop enter `0x1406A3650` (calls the list builder `0x1407CDE50`; its category tab is reset in `0x1407DDC10`); Cheat Shop enter `0x1406A3970`; Quest Shop `0x1406A3400` (facility sub-state 17) | |
| Skill Shop descriptions | `0x1407CE3E0` / `0x1407D22B0` format through `0x140060630` into a 256-byte buffer; on text languages 0, 3 and 4, `0x1401360C0` formats the result **again** | This second pass is why a `%` in a description can be read as a format code ([modifiable](modifiable.md)) |
| Colour change | list `0x140455E93`, preview `0x140457BF0` | |
| Table loading | `char.dat` load `0x1400C4E00`, construct `0x1400C4E40`, sort comparator `0x140061D80`, find by ID `0x140061F80`; `Quest.dat` load `0x1400C2A40` (5,636-byte rows); `CheatShopInformation` load `0x1400C2DA0` | |
| Options volume setters | BGM `0x140226D30`, SE `0x140227320`, message voice `0x140226E30`, unit voice `0x140227640` | What the Options screen calls; they write the four volume masters |
| Units | construct `0x14021B510`, empty unit `0x140063D40`, status-block init `0x1402C07C0`, row-index predicate `0x14015FFB0`; HP-change event `0x1401B1440`; script recruit command `0x140189CC0` | |
| Class mastery UI | progress panel `0x1400B3C70` (record at `+0x228`); class-tab panels `0x1402A3970`, `0x1402A3D30`, `0x1402A4300`; mastery readers `0x140217230`, `0x1402172E0`; current-rank refresh `0x14021EEE0` | |
| Body type in animation | `0x14014E0A0` | Treats a sprite as a monster in mode 3; the Kit's own humanoid sprites (9500+) count as humanoids |
| Voices | random line for a voice set `0x1402277B0` | |
| Recruiter description lookup | `0x1406687E0` | Scans the 47-family table for `ID / 10 × 10` to find the class's description |
| `JobRank.dat` in memory | rows `0x140A03B38`, count `0x140A03B40` (filename constructor `0x1400C2B20`, post-load sort `0x1400C1820`, lookup `0x1400744D0`) | Build A: `0x1400C1F20`, `0x1400C0C20`, `0x1400738D0` (here 0xC00 lower; the shift varies across the executable) |
| Class index | `0x14021EBE0` | ID ÷ 10 (signed), − 101, range-checked against 120, then a fixed switch (a 121-entry selector table at `0x14021EDF8` into 48 targets at `0x14021ED38`): nothing is looked up from the tables. The 47-family list the class carousel and Class tab use is a table in `.rdata` at `0x140904430` |
| Class tab | per-frame update `0x14029CA50`; list builder `0x1402A36F0` | |
| Colour menu | palette loop `cmp 9` at `0x1404550B1` (so at most 10 choices); the colour lookup wrapper `0x14014EA2F` substitutes `char.dat` `+0x379` | |
| Mastery counters | read `0x140217230` (index > 52 returns 0; counter at unit `+0x30A4 + 4 × (6 × index + ID % 10)`); rank complete `0x140217290`, all six complete `0x1402172E0`; copied unit to unit as 318 dwords (53 × 6) at `0x14021AB61`; cleared with `0x508` bytes from `+0x30A4` at `0x140064106` | The counter area has room for 53 class slots |
| Unit class fields | unit creation `0x140217EB0` stores the class ID (`+0x2E04`), cached class index (`+0x2E0A`) and rank digit (`+0x2E08`); rank digit from the ID `0x14015DB20` (last decimal digit for IDs ≥ 1000, with exceptions); next rank `0x14021EEE0` (body `0x14021EF00`) | A rank with no `char.dat` row reaches code that reads a null record |
| Active evility list helpers | binary search of the list `0x140162010`; add an innate evility `0x14015E570` | |
| Skill range for the selected action | `0x140157B50`, `0x140205F50` | The range / target menus add range evilities here (where a "Bow Range UP" effect applies) |
| A unit's skill rows | `0x140061FF0` | Resolves the unit's skill slots to `magic.dat` rows |
| Overload lookup | `0x14015DBF0` | A unit's Overload, 0 for none; called from 11 places (`0x14014FD17`, `0x140161983`, `0x1401A5A48`, `0x1401DD278`, `0x1401DD369`, `0x1402A9540`, `0x14044AB2A`, `0x1404CBF0D`, `0x140554D4C`, `0x1405555FC`, `0x1405FB784`) |
| Damage pipeline call sites | in the target computation: hit `0x1401C1EE0` (called at `0x1401BE961`), damage multiplier `0x1401C1650` (`0x1401BED6B`), critical / guard roll `0x1401C13E0` (`0x1401BED84`, which calls the crit chance `0x1401C1D00` at `0x1401C144F`); attack bonus called at `0x1401C1A59`, defence bonus at `0x1401C1889`; ailment chance called at `0x1401BEC5C`, `0x1401C005D`, `0x140220F89`, `0x1404B0853` | |
| Steal on kill | victim check `0x140203A40`, called at `0x1401AF6A2` (the defeat path), `0x140166199` and `0x1404CF248` | |
| Default language | `0x1403DF710` | Reads the system locale with `GetUserDefaultLCID` |
| Skill Shop category tab | screen `+0x1B850` (tab 0–18) | A row is listed when the tab is 0 or matches its `category` (`+0x44`) or `subcategory` (`+0x48`); the list's only size check comes after the fill loop (a scroll clamp) |
| Data pointers | `char.dat` descriptor `0x140A03878` (built at boot by `0x140709FB0`); `charPersonal.dat` rows `0x140A03A90`, count `0x140A03A98`; Recruiter's 47-entry class table `0x140999380`; class → unlock-flag table `0x140A484C0`; effect cache name template `effect%04d.obf` at `0x14091A8F0` | |
| Battle state | round counter `0x145574AA8` (saturates at 999); army / reserve counts `0x145574A9C` / `0x145574A9E` (the state's `+0x5A7F9C` / `+0x5A7F9E`); path cost map `0x140B76A30` (written by `0x1402D5390`) | |
| More UI vtables | facility menu `0x14098F840`; difficulty screen `0x14098F908`; Recruiter `0x14098BD58` (slot 1 → `0x140663B20`, the list clear that leads to the builder) | |
| Script local flags | command `1418` clears one of two 32-byte banks at `0x147B45B20` / `0x147B45B40` (D4: `0x140BE61B6` / `0x140BE61D6`) | Bank 0 when there's no battle (or mode 0), bank 1 otherwise. The matching local integer variables are D4 10500–10599 = D5 20500–20599 |
| Keyboard imports | `GetKeyboardState` thunk `0x1408D5640`; DirectInput `SetDataFormat` `0x1408F7630` | |
| Saves | auto-snapshot copies `0x140745090` / `0x140745720`; load thread `0x14074D830`, apply `0x14074DAF0`; save-folder path `0x147B58F20`; file delete `0x1401194EC` (wraps `DeleteFileW`, returns 0 on success) | |

## Findings from November 2026 (build B)

All addresses in this section are for build **B**. **From decomp** unless a row says otherwise; "emulated" means the
original function was run in an emulator on the installed executable and data (**Verified offline**).

### How a unit's sprite is bound (and why a monster needs `N + 2` resident)

For a `char.dat` row with sprite ID `N`:

1. The per-unit bind (`0x14015A860`) calls the appearance setter (`0x14014EA10`), which calls the sprite setter
   (`0x14014E7E0`) with body = `N + off` and placeholder = **`N + 2`**. `off` is 1 in battle when the sprite ID is above
   2000 (test at `0x14015AD17`), so the vanilla Imp (2020) draws from 2021; below 2000 the body is `N` itself.
2. The body file `anm%05d.lzs` loads asynchronously. Until it arrives the unit is bound to the placeholder
   (`0x14014DFD0`), and that bind only finds **records that are already loaded** (lookup `0x1403020C0`; the miss branch
   is at `0x1402F4B3A`).

| Body type (`char.dat` `+0x386`) | What owns the animation while the body loads | Consequence |
|---|---|---|
| 0 humanoid | Resident block **1** (the shared humanoid motion set); `N + 2` is only a part | A loose `anm{N+2}` binds, but corrected 2026-11-20: it is the likely source of a rare class-page crash (see [freezes](modifiable.md#freezes-and-the-class-page-crash-2026-11)). All 80 vanilla humanoid sprite rows keep `N + 2` resident and none ships it loose (**Verified offline**) |
| 1 monster | **`N + 2` itself** | `N + 2` must be a block of the resident pack, or the bind misses |

All 355 vanilla monster rows with art keep `N + 2` in the resident pack. A monster whose `N + 2` is only a loose archive
member shows **no body, its motion clock never advances, and the game freezes when the unit is attacked** (**Verified
in game**; adding the block fixed it, re-checked the same day). The file's bytes don't matter: a byte copy of the Imp's
file at a new number fails the same way. This applies to any row with body type 1, unique characters included, and it
does **not** reproduce by swapping the class of an existing humanoid unit in memory; test on a freshly recruited unit.
Adding the block to the resident pack is Cryswar's design (Overlord). The lookup at `0x14021F4A0` is not on this path;
it only gives the graphics ID used by faces and cut-ins. **Unknown:** why a missed placeholder bind leaves the later
body bind invisible.

The clean `char.dat` has no row whose body `N` exists without `N + 1` (loose, or resident for six body-type-2 rows).

### Monster normal attacks

A monster's normal attack is script event `12010000 + sprite ID`, which plays motion 207 (animations 2071 / 2077). In the clean game these animations sit in the **main** file
`anm{N}`, not the companion `anm{N+1}`, for 78 IDs (54 keep them in the companion) (**Verified offline**; that the game
plays them from the main file is inferred from that layout).

### Army size and Dismiss

- A save holds **128 characters** in total (army + reserve): the unit creator `0x1402EA3E0` refuses beyond that.
- The Recruiter's Dismiss list is built by `0x140655030`, which asks `0x140654F20` per unit. A character ID of **2460
  or below 1000** is "unique" and can't be dismissed (text 10650), **unless** the ID is in the table at `0x140A0D968`
  (39 rows of 10 words, ID in word 0). That table's 36 IDs below 1000 are the DLC characters: 510–760 in steps of 10,
  800, and 860–940 in steps of 10; they're handled like generics. Any other unit is refused only while it holds items
  or is in use (in a squad, and so on).
- So a character a mod places on a spare row **below ID 1000** can't be dismissed in game. Updated 2026-11-20: partly
  checked. A save with imported characters on low IDs was loaded and the Recruiter's Dismiss list (12 rows) offered
  generic-class units and DLC-range characters but none of the low-ID imported ones (**Verified in game**, D5 Mod Kit
  development fork, modified executable; one such unit's absence was not traced further).
- The rule locks 56 vanilla rows: the story cast and story NPCs (IDs 10–460 and 500), 770, 780, 790, 810, 820–850 and
  2460 (**Verified offline**; the table was read from a modified executable's data, and the last row's trailing words
  looked overwritten, though column 0 was consistent).
- A routine at `0x140652580` also clears the "recruited" flag of a DLC character when it dismisses it (table
  `0x140A0D960`; class ID below 1000 maps to flag 18300–18353, read by the guest room `0x140142580`). How it relates to
  the Dismiss action `0x140654770` above was not traced (**From decomp**).

### Quest Shop board

The board builder is `0x1407EC450` (emulated on a save's progress bytes up to `0x1407ECA32`, before its menu calls).

- `Quest.dat` `sort_order` is the quest's **progress slot in the save**: three bytes per slot (completed, accepted,
  seen), 0x400 slots. A slot has to stay with one quest whatever mods are installed, or a save shows one quest's
  progress on another. A slot an older mod used can be left non-zero ("dirty") in a player's save; a new quest put on
  it shows as already seen, accepted or done.
- The builder walks rows in ID order (the table is sorted by ID on load, `0x1400C2A40`), **stops at the first ID of
  2000 or more**, and only looks at progress slots `0 .. row count − 1`. A quest on a higher slot is never listed
  (**Verified in game**: recruit quests on slots 709–711 and 761–781 vanished until rows were added).
- A quest is skipped when its slot is completed or it is one of the 10 accepted quests. It is listed when its
  prerequisites are completed, its story flags are set and `min_level` is at most the highest army level. A slot already
  marked seen skips the prerequisite and flag tests. No reward check and no randomness.
- When two rows share a slot the board takes the first. The builder stops at 1,000 candidates, and the display array
  holds **512 entries with no bounds check**.
- Request quests (sort 501 and up) come after the last main-list slot.

### Learned skills are stored by ID

Learned skills live in the unit record at `+0xCC0`: 16-byte entries, at most 512 (granted by `0x140160B00`), and each
is found by a binary search of `magic.dat` by ID. Row position doesn't matter, unlike learned evilities (bits by
`Skill.dat` row position). A learn list only grants: removing a link never removes a skill a saved unit already holds.

### Skill power

A damage or heal skill's power is `effectN_value + power_lv[level] − 100`; a support or debuff row (formula 5 / 6) uses
`power_lv[level]` alone; other added effects use `effectN_value` as written (`0x1401D7A20`, `0x1401D78E0`). A special's
damage against a normal hit is `(100 + effect1_value) / 100` (`0x1401BE860`, `0x1401BFAE0`).

### Evility limits

- An innate evility never needs a learned-evility bit: the unit record holds its ID as a signed 16-bit value at
  `+0x2EEE`, the active-list builder (`0x140218C90`) works from IDs, and the `Skill.dat` lookup finds the row by ID.
  So an **innate-only** row may sit past row position 999 (emulated on a 1,010-row table with the evility at position
  1,005). A **learnable** row past position 999 has no bit.
- An evility ID a unit can hold is at most 32,767 and never **9999** (the free-slot marker).
- The evility screen appends every row with `shop_rank ≥ 1` to a fixed **1,000-entry array without a bounds check**.
- While a Magichange slot is held, the active-list builder adds to the host the participant's equipped evilities that
  its `char.dat` row lists in evility 1–4, so a monster's innate reaches its wielder.

### Evility mechanics read from the game

| Evility | Where and how |
|---|---|
| Purgatory (1222) | A test inside the damage-reduction rules (`0x1401FB2E0`): when the target entry of the action context is a critical hit (byte `ctx + target × 0x260 + 0x152A`) the value is multiplied by `(100 + v) / 100` |
| First Impression (6047) | Uses the stat-record byte `+0xDF9`, "already took damage this stage": set by the damage apply, read by the nullify test, touched by nothing else. Clearing it at the round edge (`0x1401E5AF0`) makes the evility fire once per round |
| Habitual Licker (3011) | A branch of `0x1401ADA40` (called from `0x1401B1BA0` after each hit's damage): when the attacker carries 3011 it looks up 3011's own `Skill.dat` row (call at `0x1401ADDF0`), takes `floor(damage × row +0x38 / 100)` and applies it to the target's SP. The percent is **read from the row**, with no cap at 100 (emulated: 25 % vanilla) |
| Assault Attack (1020), Sonic Charge (105), Cool and Calm (62) | All read the panels-moved word at battle record `+0x61C`. The unit's end-of-turn routine (`0x140159F50`) zeroes it at the end of the unit's own turn, before the round-edge regeneration runs |
| Stat-up / stat-down statuses (Braveheart 1501 … Slow 1606) | A stat-up / stat-down status is status slots 7–12 of the stat record: active byte `+0x4E9 + slot`, signed power `+0x536 + slot`, added to the aggregate by `0x1401B97D0`. The skills that set them are `magic.dat` effect kinds 11–16 (kind `k` → slot `k − 4`, mapper `0x1401D7B30`). Evility bonuses are not statuses |

Ailment indexes (from `magic.dat` effect kinds): Poison (kind 21) 0, Slumber 1, Stun 2, Blank Out / Amnesia 3, Charm (kind 27) 6.

Other unit fields used by these rules: class / character ID `+0x2E04`; class-kind byte `+0x2E25` (human / monster and
gender); squad `+0x30A0` with gate `+0x30A1` (squad helper `0x1401D3990`); the six current stats as 64-bit values at
`+0x2CE8` (ATK … SPD); index of the worn `char.dat` row `+0x2E0A`.

### Magichange state

In the unit's stat record `SR = 0x141067DC8 + (unit +0x4348) × 0xE98`:

- The Magichange equip `0x140605E30(monster, host, slot)` writes the monster's unit ID to `SR(host) +0x4B8 + 0x10 × slot`
  (a second, dual participant at `+0x4C0 + 0x10 × slot`), the slot's remaining turns to `SR(host) +0x4E0 + slot` (3, or
  2 for the dual branch, plus the value of evility 4505) and 1 to `SR(monster) +0x4E2`.
- `0x14015F030` clears them. The turn handler `0x1406098C0` ends the Magichange when a slot's byte reads 1 and
  decrements it otherwise, so **1 is the last turn**.
- "Is a Magichange host" is the test the command-menu predicate makes: some slot 0 / 1 has `SR +0x4B8 + 0x10 × s ≠ 0`.

Read from the writers, not observed on a live unit.

### Graphics ID: which face a unit shows

`0x14021F4A0` (build B; launch build `0x14021E9D0`) turns a class ID and palette into the **graphics ID** stored at
unit `+0x2E36`. With colour = `char.dat` `+0xDE`: for a generic class ID (the ranges 1000–1310 and 2010–2300) it returns
`palette + (colour / 10) × 10`; for anything else it returns the colour unchanged, palette ignored. 19 callers write the
result (unit construction, battle setup, palette change, class change, load, spawn). Its readers are portraits only:
faces and cut-ins play animation `10000 + graphics ID`, while the list icon is keyed on `+0xDE` directly.

Consequence, **Verified in game**: a character placed in a generic-range class slot whose colour ID doesn't end in 0
shows another character's face, or a blank one when no `anm` `10000 + ID` exists (colour 643 → 640 showed the face
belonging to 640). The battle body is not affected; it takes its file from the class row. After a class ID is changed
in memory the graphics ID keeps its old value until the next refresh.

### Damage and the defeat counter

- Damage (`0x1401BE860`): `floor(T × (100 + A) / 100) − floor(U)`, with T the attacker's attack-type stat (plus flat
  bonuses), A the attacker's bonus percent (clamped −99 … 2000), `U = (D + 100) × DEF / 200`; then the skill multiplier
  (base 100). The attacker-bonus function `0x1401C1940` is used by normal attacks, skills, counters and the forecast.
- Defeat counters: six 16-bit slots at `0x146A5F054`, indexed by the defeated unit's team byte. Incremented (in
  `0x1401AEB10`) when a unit is defeated **and an attacker exists**; damage with no attacker doesn't count. Zeroed at
  battle start, never per turn, clamped at 99 per slot. Vanilla evilities 140 and 170 read them (value × count, cap
  200).
- The battle round counter is the global at `0x145574AA8`. Updated 2026-11-20: the battle-start routine `0x140523970` sets it to 1 and each round edge adds 1 (**From decomp**); live reads over two End Turns went 1, 2, 3 (**Verified in game**, modified executable).
- After a hit, `0x1401ADA40` runs a secondary pass (only caller `0x1401B1BA0`). The HP-change routine `0x1401B1440`
  picks the HP or SP pool from action-context byte `+0x276DD`. Malicious Reflect (143) uses the positive target damage,
  capped to the attacker's current HP / SP minus 1.
- The "is a special skill" test `0x140139E30` recognises only a handful of exceptional targeting handlers (IDs 10, 11,
  1901, 1906, 11091–11095, 920, 955, 957, 959), not ordinary offensive specials.
- Equipment stats (`0x140219630`): slot offset = slot × 600, item ID `+0x138`, item category `+0x156` (1–9 are weapons);
  every slot reads the shared potency at `+0x3068 + stat × 2`. The native general attack-power cap is 2000.

Unit record: current HP `+0x2CC8`, max HP `+0x2CD8` (a written current HP is clamped to max, **Verified in game**), team
`+0x2E32`, rank `+0x2E08`, palette `+0x2E2C`, five innate evility IDs (16-bit) from `+0x2EEE`, active evility list
`+0x2F30` with its count at `+0x3030`, roster index `+0x2E31`. Battle record table `0x141BA33B0`, stride 0x1260: unit
pointer `+0x240`, on-map flag `+0x2E8`, carried-by `+0x2EE` (−1 = not carried). Unit `+0x4348` is a unit-slot index.

### Where the 128-unit cap lives

Roster base `0x145207DB8`, stride 0x4750; army count at `0x145574A9C` (16-bit; save offset 0x5A7F9C), reserve count at
`0x145574A9E`. **Reserve units live at the end of the 128 slots** (index `0x80 − reserve`), army from 0, so 128 is in the
address arithmetic and not just in one test. The guard `army + reserve < 0x80` is in the unit creator and at least six
other sites (script recruit `0x140189CC0` / `0x140189F70`, the main menu's "army full" flag `0x140651030`, and others);
about ten more functions index the reserve as `0x80 − reserve`. In the save: roster at `0x23B2B8`, 128 × 0x4750, then
three 128-entry serial lists at `0x880498`; loader `0x140748A60`, writer `0x140749000`. Other parts of a save refer to
a unit by its **serial** (squads, groups, the base walker and followers). The Dismiss action itself is `0x140654770`. A
recruit quest's reward can leave two records of the class: the army unit and a copy in the reserve (**Verified in
game**).

### Loading an animation file

- `anm` files exist only in `Data/Anime/ANM_HI_{JP,EN,FR,CH,HN}.dat`: 2,006 / 97 / 89 / 77 / 78 members (**Verified
  offline**).
- The package loader (`0x1402EE3D0`) reads the four header u32s and copies the metadata from file `+0x10`; header bytes
  `+0x14..+0x20` are never read. Image headers are located from the **end** of the metadata (so they must end exactly
  there), and the last `palette count × 1024` payload bytes are taken as the palettes: palettes are 256 × 1 ARGB, last
  in the payload, with no trailing bytes.
- Image format byte: 0 or 7 → 4 bytes per pixel, 9 → 1, 10–13 → 2 (`0x140301601`).
- The record builder (`0x1402EF650`) turns each block into a 0xC0-byte record sorted by ID (`0x1402EF0F0`), which is why
  a block appended to the resident pack is still found. Record pointers: animations `+0x48`, nodes `+0x50`, keys
  `+0x58`, palette info `+0x60`, texture info `+0x68`, cells `+0x70`, transforms `+0x78`, pivots `+0x80`, colours
  `+0x88`, params `+0x90`; ID at `+0x08`, palette count `+0x10`, texture count `+0x12`.
- Pools: 1,024 managed file slots (stride 0x28), loaded slots at `0x1469B05E0` (stride 0x150), a 16,384-object upload
  list. The resident pack is one slot and has no byte cap.
- Vanilla placeholder blocks (`N + 2`) hold three animations (0, 1, 7), one small page and 5 or 10 palettes. While a
  body loads, a humanoid placeholder plays motion 101 and a monster placeholder motion 1.
- The animation clock advances only when player `+0x30`, `+0x40` and a global step mask are all non-zero.
- Missing caster motions are normal: of 2,700 learn-list skills on vanilla rows, 1,109 (41 %) name a caster motion
  the unit's file lacks, and the game falls back to a standing pose (**Verified offline** census).
- The wheel in the Recruiter shows 11 units plus a twelfth "entering" one, bound with variant `+2` (builder
  `0x140666920`).

### Window, message boxes and input

- The window is created in `0x140865440` on its own thread (style `0x80CF0000` windowed, position from the saved
  settings). The only window state the game tracks is from `WM_SIZE` (0 normal, 1 maximised, 2 minimised); the frame
  function `0x140221D50` reads it. There is no activate / kill-focus handler: focus is handled through DirectInput.
- Keyboard and mouse are DirectInput at cooperative level **foreground** (`GetDeviceState` each frame, `0x14089D890`).
  Pads are found by a hot-plug thread that rescans every second (up to 32 slots): an XInput pad is read with
  `XInputGetState` (`0x1408A06D0`), which has **no focus rule**, and any other pad with DirectInput at level
  **background**. So a pad is read whatever window is in front (**Verified in game**). The game never calls
  `GetAsyncKeyState`. For a DirectInput pad a zeroed state is not neutral (POV 0 is d-pad up, and stick centres are
  calibrated from the third read).
- Every message-box site: `0x140885E20` "Failed to create scratch vertex buffer" (PROCESS_NUMBER 301); `0x1408867D0`
  when `Present` returns device removed / reset; `0x140883EA0` device, swap-chain and buffer creation (401 / 406 /
  407); `0x1408641B0` start-up "Failed to initialize …"; `0x1403D70E0` no Shader Model 4 / 5; `0x1403EF9E0` and
  `0x14013DDF0` Steam not running, already running, no sound device; `0x1403D8A80` "Do you wish to exit the game?".

### EXP weight has two jobs

`char.dat` `+224` is the kill-EXP weight of a defeated unit (kill path `0x140147F40`) **and** the unit's own EXP-curve
weight: the stat recalculation (`0x14021BFB0`) levels a unit up when `EXP ≥ row +224 × cumulative EXP[level + 1]`, and
the script command that sets a unit's level (`0x14016C680`) sets `EXP = cumulative EXP[level] × row +224`.

### Voices

- Every voice request goes through the table lookup `0x1400AEF60` (first statement of the play function; caller
  `0x1403F7954`), whatever the volume is.
- The voice is read from the **worn** row: the voice-key helper `0x140160280` reads the `char.dat` index at unit
  `+0x2E0A`, so a costume row speaks with its own voice ID.

## Findings from 2026-11-11 to 2026-11-20 (build B)

Addresses are for build **B** (Steam, SHA-256 `49a7530b…`) unless a row says otherwise. "Modified executable" means the
D5 Mod Kit development fork's patched build, which is what the live tests ran. Not yet in a public release of the Kit.

### The effect-model vertex arena (1,024 records)

Every 3D model the game builds (effect models, map objects) takes one block from an arena of **1,024 block records**
(**From decomp**, checked against the executable's bytes).

- Arena header `0x146A07110`: `+0` record count (0x400), `+4` records in use, `+8` capacity (0x18FA000 bytes, 25 MB),
  `+0xC` bytes in use, `+0x28` pointer to the 24-byte records (block, size, alignment). Set up once at start, never
  reset. A second arena at `0x146A070B0` (also 1,024 records, 8 MB) serves terrain and geo overlays.
- The allocator `0x140308C80` calls a record scan `0x140308C10`. When no record is free it **writes through NULL**
  (`0x140308C5D`). A Unicorn run of the game's own code reproduces this after 1,023 build-and-release cycles
  (**Verified offline**). The same address was the Wine page fault of a live crash 78 attack casts into one
  session on the modified executable (**Verified in game**); the crash did not repeat on fresh boots.
- The model build is `0x1402FA0A0`; it stores the block at `manager + slot × 0xD0 + 0x12528`. The cache release
  `0x1403FFFF0` calls the model free `0x1402FACF0`, which frees only that block.
- **Double build in the loader.** The loader `0x140306760` ends with a model build, and the game's own load job
  (vtable `0x14098B008`, slot 3, `0x1400AF850`) builds again, so the second block replaces the first and the first is
  never freed. In stock D5 only the start-up preload (`0x140709F20` through `0x1404000C0`) loads models, once each, so
  this costs a fixed 383 orphaned records. Nothing allocates from the arena at scene start, scene end, skip or battle
  end (**From decomp**). A model preloaded at start therefore holds two records (derived, not measured in stock).
- A loader that loads models **on demand** and copies the game's two-step job leaks one record (and a heap box array)
  per load. This hits any mod that makes models lazy (the Kit does, to get past the 400-model preload limit) once the
  cache evicts and reloads them.
- **Growth seen live** (**Verified in game**, modified executable, arena in-use count read between casts, one boot):
  a stock skill (Hellfire Shot) took 20 records on its first cast and none on casts 2 to 20; Flying Press (a converted
  Disgaea 4 attack) took 56 on its first cast, then 8, 6 and 12 on three later casts, then none for casts 8 to 20; Crane
  Dance 14 then 4 then none; ten End Turns with no cast added 2 once. A repeated cast does not grow the count; what grows
  is one set of records per distinct effect model used for the first time.
- Whether a long soak ends in the crash with the on-demand fix in place is **Unknown** (the run was still going).

### Sub-class list seen in play and the current-sub-class panel

Addresses for the screen are in [Class mastery and sub-classes](#class-mastery-and-sub-classes). New:

- The Strategy Assembly list offers a family when `0x140470610` accepts its first rank (the unlock test `0x1401C6110`
  plus seven character-specific monster exceptions); a row's ID is `family − 1 + unlocked ranks`. On exit `0x140470740`
  writes `unit +0x35AC` from the screen's `+0x8FB0`, and that is its only effect (**From decomp**).
- Live, with 41 imported generic classes installed (**Verified in game**, modified executable): a post-game unit of the
  vanilla cast saw 47 rows, 43 vanilla plus exactly 4 imported, and the list wrapped after the 47th. A vanilla Warrior
  and two imported unique characters each saw 43 rows, none imported. The Status screen's Class tab listed all 41. So the
  47-row cap is real and the number of free rows depends on how many families a unit has unlocked.
- Imported unique characters can open the sub-class topic and **Chara World** (Train, difficulty, board) (**Verified in
  game**). A new unit has 0 Mana, so Chara World answers "Not enough Mana": the Mana word is a u32 at unit `+0x2DF8`.
- The panel that shows the **current sub-class** (`0x14046F980`) draws for any positive `+0x35AC`. The value is compared
  as a **signed** 16-bit word (`jle` at `0x14046F9A0`), so 32,768 and above skip the panel. A positive ID with no
  `char.dat` row makes the lookup `0x140061F80` return −1, and the text call `0x1403FACE0` then gets a pointer built
  from a null page (**From decomp**; the crash path reproduced under emulation, **Verified offline**). This is the
  failure a save meets when it holds a mod's class as sub-class and the mod is later removed.
- A guard that skips the panel on a missing row (jump out of the function at `0x14046FA39`) was tested live: with
  `+0x35AC` set in memory to 32001 (no row anywhere), the list drew without the panel and without a crash
  (**Verified in game**, modified executable `6c5ca464fd87…`). An ID that does have a row (30000, an imported
  character) drew its name.
- **Menu confirm is read keyboard first.** The shared read `0x1402258B0` (`action, 1, 0`; 145 call sites with action 4,
  the confirm action) looks up the key bound to the action in the input object, tests the edge table `0x147B24AE0`, and
  only then falls to the pad (`0x140223CE0` → `0x140223EA0`). A hook that reads only the pad bits misses every keyboard
  press: with K held, the pad read returned 0 for every button (**Verified in game**).

### Dark Assembly bill lists

The two Dark Assembly screens choose by bill ID (**From decomp**, no live measurement of the predicates):

- Strategy predicate `0x14044A730(assembly, row, unit)`, called for every row by both list builders (`0x140442BC0`,
  `0x1404449A0`), hides IDs `0x133`, `0x47`–`0x48` and **1000–1999**, has per-ID rules for some vanilla IDs, then
  requires every `req_flag` set and every `block_flag` clear, and returns 1.
- Item Assembly predicate `0x14068FE40` lists **only IDs 1000–1999** (and 1) with the same flag rules plus a minimum
  item level. So a Strategy bill with an ID of 2000 or more is listed by the game's own code, gated by its own flags,
  and never shown in the Item Assembly.
- `WishDataBase.dat` loads to a runtime table of `0x80`-byte rows (count `0x140A04E88`, rows `0x140A04EA0`): `+0` ID,
  `+0x38` minimum item level, `+0x40…+0x4C` `req_flag1..4`, `+0x50…+0x5C` `block_flag1..4`, `+0x60…+0x6C`
  `set_flag1..4`, `+0x70…+0x7C` `clear_flag1..4`. Flags are bits of the game flag bitmap at `0x144FCCB0E`.
- The effect of a passed bill runs in `0x140221090` with the row in `r15`: it switches on the ID (1000–1999 decrement a
  count at `[rbx+0x134]`; `0x3F3`–`0x40E` stat bonuses through a jump table; `0x411`–`0x418`; `0x7C`–`0x7E` class
  changes of the selected unit), re-reads the ID at `0x14022191B`, then sets and clears the row's flags.
- Creating a unit from code: `0x1402EA3E0(class, 0, level)` refuses at army + reserve of 128. **Address caution:** the
  D5 Mod Kit names this function by its address in an older ("known") build, `0x1402EA890`; in the Steam build that
  address is inside a different function (`0x1402EA760`). Check which build a source address belongs to.
- A unit created at level L already holds its level-gated skills: the stat recalculation calls `0x1401D4F30`, which
  fills the skill list (`+0xCC0`, count `+0x2CC0`) by level, so no level-up call is needed. Measured on 245 imported
  classes at levels 1, 50, 500 and 9999: skill sets only grow, 191 have none or fewer at level 1, and none gains skills
  above level 500 (**Verified offline**).

### Evility rules read from the game

All **From decomp** unless marked.

- `0x140206160` / `0x1402062F0` (two copies) are the game's own test for evilities 124, 1224 and 5000 ("cannot be
  lifted"); callers `0x140154310` (carry check) and `0x1402DA750` (target collection). It does not give the holder any
  stats from those evilities.
- Hit chance `0x1401F5410` takes the percent in and out, clamps it to 1–100 as its first act
  (`0x1401F5431`–`0x1401F5475`), then multiplies by `(100 − v) / 100` for the defender's own evasion evilities. Call site
  `0x1401C2318` (emulated, **Verified offline**).
- Support-magic buffs: `0x1401FEFA0` multiplies the buff value by evility 1180's row `+0x38` when the skill's `magic.dat`
  row byte `+0x3F6` is 5. Callers `0x1401C0076` and the preview `0x140201F03`.
- Weapon stats: the unit recalculation calls `0x140219630` once per stat (eight static calls, `0x14021D53C`–
  `0x14021D5F3`). An equipment entry whose category byte (`+0x156` of its 600 bytes) is 1–9 is a weapon.
- Hit counter for Impact Charge 2065: the short at stat record `+0x5D8` is written by the damage apply (`0x1401B2544`),
  wiped at `0x14015FB47` and a second site (not re-listed here) and copied at `0x140091A4B`; readers are the attack-bonus rules and
  the damage breakdown `0x14020D1C0`. Evility 2065 adds 10 per counted hit.
- Idle counter (the "turns without moving" byte, stat record `SR +1`, cap 10): raised by the end-of-turn routine
  `0x140159F50` only when the movement word `+0x61C`, the stat-record byte `SR +2` (raised by the lift and throw commands
  `0x140534070` / `0x140534380`), the battle byte `+0x11E1` (set when a hit lands from a skill whose row has `+0x3F3`
  set) and, in one mode, `+0x11F5` are all 0.
- Percent stat bonuses: the recalculation `0x14021BFB0` takes `delta = trunc(aggregate × stat / 100)` per stat and, when
  the delta is 0 but the aggregate is not, uses +1 (or −1). The aggregate is rebuilt from nothing on every recalculation,
  and the recalculation also rebuilds the active evility count and list itself, so writing 0 to the count (`+0x3030`) is
  undone at once. Exact predicted numbers matched live for a self-only percent row (**Verified in game**, modified
  executable). Current HP is `+0x2CC8`, not `+0x2CC0` (that word is something else).
- Steal on defeat (Plunder 1202, Cat Burglar 3036): the roll in `0x1401AEB10` happens only if the killer is player side
  (`0x1401AF625`) and the victim is not (`0x1401AF63E`, skip jump at `0x1401AF645`). It copies one random non-empty
  equipped slot of the victim to pending loot (`0x140533760`) and never clears the victim's slot.
- **Self-destruct routine `0x1401A3E80(unit object, flag)`.** It reads only the unit object, takes 50 % of the unit's
  current HP as damage (`0x1401EADC0`; Prinny 1236 50 %, Explosive Body 2010 value 2 percent, Focused Bomb 2014 value 1
  percent, doubled while a holder of 0x1776 is on the map), builds the area with `0x1402038C0` (defaults 10 and 2;
  evilities 0x4D4 / 0x7DA / 0x7DC change it), sets `+0x11DC` to 1 and applies the unit's HP to itself. The flag only picks a
  sound. Callers: a thrown unit landing (`0x1406357E0`) and chain reactions (`0x1404A7BE0`, which also sets unit state
  `0x6A`). Nothing in it needs a throw.
- Summoning (Summon Guardian 6139): the spawn block in `0x140552D20` calls the unit spawner `0x1401C5AE0`, after a test
  that fewer than 32 bound units are alive (`0x1401C3A00 < 0x20`). The spawner takes a 0x40-byte request: `+0` record
  pointer (0 = build from the character ID), `+8/+0xC/+0x10` world x, y, z floats, `+0x14` facing in degrees (int),
  `+0x18` character ID, `+0x1C` level, `+0x20` byte, `+0x24` team byte, `+0x28` byte, `+0x2C` variant (−1). It returns the
  battle slot or −1, and fails only when no spare roster record or battle slot exists. Class IDs 2090–2095 are the
  Undead tiers.
- A created unit given team byte 4 was AI-run and fought on the player's side in play: enemies hit it and it
  countered, and it did not act on its own phase in the one round observed. It was cleaned up at stage end
  (**Verified in game**, modified executable; a unit with a distant target was not tried).
- **When a defeated unit disappears.** A hit that leaves HP 0 starts a 20-tick countdown at battle record `+0x2B2`;
  `0x14015A1B0` decrements it every frame (unless a pause flag is set) and at 0 releases the record,
  `0x14015B9E0` clears `+0x2E8` and destroys the actor, and an enemy-pool record is zeroed. That is about 0.3 s after
  the hit, while the action's script is still running. The end-of-action pass `0x14053FE71` (the only caller of
  `0x140542CB0`) comes after the script ends, so by then the victim's record no longer holds its cell or level.
- Per-target action context: target count (short) at `ctx +0x27318`; entry *j* at `ctx +0x1318 + j × 0x260` (battle
  slot short at `+0`); the knocked-out byte for target *j* at `ctx +0x152E + j × 0x260`.

### Draw packets, texture objects and stale records

(**From decomp**; the crash itself **Verified in game**, see [modifiable.md](modifiable.md#freezes-and-the-class-page-crash-2026-11).)

- A draw packet is 200 bytes: `+0x20` texture object, `+0x28` palette object. It is written by `0x1402F6B10` (and the
  same code in `0x1402F7C60`) and queued whenever `0 ≤ cell.tex < record +0x12`, **whether or not the texture pointer is
  NULL**. The flush `0x1402F3650` reads the texture's format byte at `+0x24` (`0x1402F3740`, `0x1402F3744`) with no NULL
  test.
- Texture objects are 0x58 bytes from a fixed pool of **16,384**; `+0x20/+0x22` width and height, `+0x24` format
  (9 = 8-bit indexed).
- The package release `0x1402EEE00` zeroes `record +0x98 / +0xA0` and frees the record array. Owners hold record
  pointers found by ID (`0x1402F6870`, `0x1402F4B10`) **without taking a reference**, and nothing walks the owners on
  release. So a stale record gives NULL textures until its memory is reused, then junk. The emitter reads the texture
  header (`(record +0x68) + cell.tex × 12`, width at `+4`) at `0x1402F7A4C`, which is where a junk pointer faults.
- **Residency rule for `N + 2` (corrected 2026-11-20).** A humanoid unit binds part slot 0 to `N + 2` while body `N`
  loads (`0x14014ED00`, from `0x14014E7E0`) but acquires only `N`. A resident `N + 2` is found first and never
  released; a loose one is loaded by whoever asked and released when that holder lets go, possibly while a node still
  draws from it. This is **likely, not proven** to be the cause of the class-page crash; making every `N + 2` of the
  affected classes resident ended the crashes in the live check (modifiable.md). Humanoid uniques and plain `char.dat`
  rows with a loose `N + 2` were not covered (426 rows in the tested install; about 0.14 MB each) and are
  **Unknown**.
- A package builds in slices: records become findable by ID once their header is copied, and their texture and palette
  tables fill one entry per slice, so a bind can find a record whose tables are not decoded yet.

### Unit record: new fields

| Offset | Size | Field |
|---|---|---|
| `+0x0BFC` | 0x44 | Title text. **Not kept from the save**: every load, base entry, New Game+ and battle start copies it from the `char.dat` row (`+0x1FC + language × 0x44`) through `0x1401C3800`, then `0x1401C3A90` replaces it for certain class IDs |
| `+0x2DF8` | u32 | Mana (Chara World) |

`0x1401C3A90` switches on the class ID: Killia's (10) gets her earned story title once the flag `0x144FCCB65 & 0x20` is
set; other cases are 0x3C, 0x46, 100, 0x78, 0xBE, 0xDC and 0x14A–0x172. A class ID with no case falls through to the
row's plain title. This is why a unit saved while wearing an outfit (a different class ID) came back with the row title
(**Verified in game**, modified executable `b4dd297e…`; fixed by mapping the class argument through the outfit table).

## Unit record (in the game state)

The game state is a `0x89B8C8`-byte block that a save stores whole. Units are `0x4750` bytes each.

| Offset | Size | Field |
|---|---|---|
| `+0x0BC8` | | Name |
| `+0x2CD8` | | Final stat maxima |
| `+0x2D18` | | Base stat vector |
| `+0x2E04` | u32 | `char.dat` ID |
| `+0x2E0A` | s16 | Cached `char.dat` **row index** (used directly in 124 places) |
| `+0x2E26` | u8 | Personality |
| `+0x2E36` | | Cached portrait anm |
| `+0x2F30` | 64 × s32 | Active evility list (see below) |
| `+0x3030` | u32 | Its length, 0–64 |
| `+0x2E31` | u8 | Roster slot (changes when units are reordered) |
| `+0x3060` | u64 | Unit serial: the game's identity for a unit, kept through reordering and reincarnation |
| `+0x309C` | | Overload override |
| `+0x2E70` | 125 bytes | Learned-evility bits, one per sorted `Skill.dat` row (1,000) |
| `+0x2EEE` | 5 × u16 | Innate evilities; count at `+0x2EF8` |
| `+0x2EFC`, `+0x2F10` | 10 × u16 each | Two equipped-evility lists; counts `+0x2F2C` / `+0x2F2E`; 9999 = empty |
| `+0x30A4` | 53 × 24 | Class-mastery slots: six `u32` counters each (vanilla uses 0–46) |
| `+0x359C` | 8 × u16 | Mastery completion bonuses |
| `+0x35AC` | u16 | Selected sub-class |
| `+0x35BC` | u16 | Revenge gauge |

**The active evility list** (build **B**, emulated original code). Builder `0x140218C90` fills all 64 slots with −1,
sets the length to 0, then appends the unit's innate and learned evilities, five equipment sources and partner
providers, each guarded by the 64-slot limit. IDs are converted from signed 16-bit, so **IDs of 32,768 and up become
negative** here. The list is then sorted (`0x140115960`, signed comparator `0x1402BFA90`) with **no de-duplication**.
The has-evility check (`0x1402068B0`) and another hard-coded consumer (`0x140162010`) use the length as an
*inclusive* upper bound, one past the last entry; harmless while the slot after the list holds −1.

**Model records** (build **B**): 650 slots of `0xE98` bytes at `0x141067DC8`; a unit's slot index is at `unit+0x4348`, and
the model record keeps its owner's full serial at `+0xE68` (written in `0x1401DCD61…`), which is how mastery from a
battle copy finds the real unit (award fan-out resolves it through `0x14021F370`).

**Roster and unit identity** (build **B**): 128 unit records (`0x4750` bytes each) at state `+0x23B2B8`; army count at
state `+0x5A7F9C`, reserve count at `+0x5A7F9E` (reserve units are stored at the tail); a 64-bit allocation counter at
state `+0x5A7F60` supplies each new unit's serial. Creator `0x1402EA3E0` (refuses when army + reserve reaches 128; a unit hired at the Recruiter joins the army, verified in game);
serial lookup `0x14021F370` (army, then reserve, then temporary units; first match wins); slot swap `0x1402E7DB0`
moves whole records. Reincarnation (`0x140219C40`, committed by `0x140472360`) keeps the serial. Temporary-party
battles copy the whole state (`0x14021FA10`) and restore it afterwards (`0x140220210`). Save encode / decode:
`0x140744730` / `0x140743130`, always the full `0x89B8C8`-byte state.

Other state: event flag bitfield at `0x144FFA9A0 + 0xE` (A) · `0x144FCCB0E` (**B**), flags ≤ 19,999; 400 stage slots of
0x80 bytes at state offset `0x4767D8`.

## Shared with other games

Function-level matching shows D5 shares a lot of engine code with D4, D6 and D7, and little with D1, D1C and D2
(private BSim analysis, not in this repo). Engine pages will go in `engines/`.

### Seen in play (2026-11, Verified in game)

- A queued special's SP is spent when it executes at End Turn; a queued cast on a target that an earlier action killed
  is cancelled with no SP spent.
- A shape-from-caster special (range 0) needs an enemy on the shape or the menu says "No target."
- A Geo Change row (the Item World skill type) does nothing in a normal battle.
- A unit's **facing**, not the camera, decides whether it plays the front (1001) or back (1007) idle. Over four camera
  steps a unit shows 1001 / 1007 / 1007 / 1001.
- A unit dispatched more than three panels from the base is refused.
- On the load screen the cursor starts on the autosave row.
- A save re-written with YKCMP type 7 instead of type 4 loads normally.

## Open questions

- Which functions decide turn order, the command menu, and Move/Jump totals? (Not yet mapped for evilities.)
- Are the two build-B hashes (SHA-1 `9460a641…`, SHA-256 `49a7530b…`) the same file?
- A full name map of the battle, AI and menu systems.

## Sources

- D5 Mod Kit by eraser4521 (MIT): [EXE_ANALYSIS.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/EXE_ANALYSIS.md), [CLASSES.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/CLASSES.md), [OVERLOADS.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/OVERLOADS.md), [VOICES.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/VOICES.md), [MAPS_AND_OBJECTS.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/MAPS_AND_OBJECTS.md), [SKILL_ANIMATIONS.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/SKILL_ANIMATIONS.md), [OUTFITS.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/OUTFITS.md); build list from `d5lib/gamecheck.py` and `d5lib/exemap.py`.
- This project's D4 → D5 expansion research (2026-10, `Research/d4-expansion-20261018/`: `rules/active-list`, `mvp-mastery-consumers`, `mvp-mastery-identity`), which runs original build-B code in an emulator ([method](../../../guides/decompile-setup.md#checking-a-finding-by-running-the-original-code)).
- D5 Mod Kit development fork (Sean13128): `docs/EVILITIES.md`, `docs/MASTERY.md`.
- [disgaea5autoplay](https://github.com/Grid487/disgaea5autoplay) by Grid487: the SHA-256 of the Steam executable.
- November 2026 sections: this project's research, published in the D5 Mod Kit development fork's `docs/ANM.md`, `docs/EVILITIES.md`, `docs/IMPORT_GATES.md`, `docs/CHARACTER_BANK.md`, `docs/GAME_TRUTH.md`, `docs/IMPORT_YIELD.md` and `docs/SOURCE_VOICES.md`. The resident-placeholder design is Cryswar's (Overlord).
