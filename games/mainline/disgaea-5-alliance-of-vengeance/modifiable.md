# Disgaea 5 Complete — What Can Be Modified

How each part of the game can be changed, and where it breaks. PC version; function addresses are on
[functions.md](functions.md), table layouts on [data.md](data.md).

**Evidence:** unless a row says otherwise, everything here is **Community-reported** from D5 Mod Kit's documentation
(eraser4521), which is static analysis of the executable plus in-game tests by the Kit author. "Verified in game (Kit)"
means the Kit's docs say it was tested in the running game.

## The ground rules

- **There is no loose-file folder.** The game reads data only through its archives. To change a file you rebuild an
  archive or add an entry to one (see below). The only files it opens directly are `Data/Script/script.dat`,
  `Data/Script/talk.dat`, the sound archives, movies, `config.ini`, `keysettings.bin` and the saves.
- **No checksums** anywhere: archives, tables and saves are not verified.
- **Archive lookup is a case-insensitive prefix match, first entry wins**, searching the text-language archive first,
  then JP. So a file added to `*_EN.dat` overrides the same file in `*_JP.dat` for English players, and new names are
  found in any language archive. Watch out: a short name earlier in the directory can shadow a longer one.
- **Tables load at startup.** Restart the game after changing them.
- **Saves cache `char.dat` row indices.** Only ever append rows, with IDs above every existing ID (max shipped: 9001).
- **Steam updates and "Verify integrity" put vanilla files back.** Mods have to be reinstalled afterwards.

## Methods

| Method | How it works | Used by | Needs |
|---|---|---|---|
| **Data edit** | Change rows in `Database_0.dat`, or add rows | D5 Mod Kit, D5tools (`dat2csv`), Disgaea Unleashed | Re-obfuscate and rebuild the archive |
| **Archive overlay** | Add replacement or new entries to an archive (often the language archive, which is searched first) | D5 Mod Kit | Archive writer |
| **Script / text edit** | Edit `script.dat` / `talk.dat` in place: the only way to change events and dialogue. Appended scripts are found (the last matching ID wins) | D5 Mod Kit | Script compiler (567-opcode table) |
| **Executable patch** | Patch `disgaea5.exe` on disk, or add code sections | D5 Mod Kit (`.d5*` sections), Disgaea Unleashed (replaced EXE) | Build-specific addresses (see [functions.md](functions.md#which-build-an-address-belongs-to)) |
| **Runtime hook (proxy DLL)** | A DLL the game loads by name hooks functions in memory; files on disk stay vanilla | Overlord (`dinput8.dll`), disgaea5autoplay (`XINPUT9_1_0.dll`) | Build-specific offsets |
| **Memory edit** | Change values in the running game | Cheat Engine tables | Addresses for your build |
| **Save edit** | Decode the save, change the state, re-encode | Kit save tools | Save codec (see [INDEX.md](INDEX.md#save-files)) |

## By area

| Area | What can change | Method | Limits |
|---|---|---|---|
| **Stats, growth, aptitudes** | Everything in `char.dat` and `JobRank.dat` | Data edit | Stat cap (99,999,999) isn't in the tables. Disgaea Unleashed raised it to 199,999,999 and shipped a replaced EXE to do it |
| **Unique characters** | New characters with their own art, skills, evilities, Overload | Data edit + art | IDs **below 1000** only: the game treats 1000+ as generic classes. About 714 usable IDs (Kit OUTFITS.md) |
| **Generic classes** | New recruitable classes with six ranks | Data edit; exe patch past the dummy slots | See [classes](#generic-classes) |
| **Class mastery** | Mastery for new classes | Exe patch | Six spare slots, so six new classes at once (Kit fork) |
| **Skills** | Numbers, areas, effects, learnsets, new skills | Data edit | Skill animation events must exist, or the battle hangs (below) |
| **Skill animations** | Timing, effects, poses, camera | Script edit | Effect IDs are signed 16-bit in scripts (≤ 32,767) |
| **Overloads** | Rename, change numbers, new Overloads | Data edit + exe patch | Vanilla Overload IDs 1–213. Per-unit data exists for Overload skills 3000–3255 only, so about 208 new Overloads can have numbers of their own |
| **Evilities** | Names, numbers, slot cost, owners | Data edit | **Behavior is in the executable**, keyed by ID. A new ID does nothing unless code is patched in. The Kit's fork can alias a new ID to an existing effect or add new effect code for stats, damage, hit, ailments, crits, regen, flight, nullify and counters. Turn order, the command menu, Move/Jump and ranges aren't covered yet |
| **Items** | Stats (64-bit), price, range, innocents, use skill | Data edit | |
| **Maps and stages** | Terrain, units, Geo panels, new stages | Archive overlay + data edit | Grids up to 128 wide; ≤ 255 units per map; 400 stage slots per save. Every stage needs a `DemoCheckDataBase.dat` row, or the game **closes** when the cursor reaches it on the stage list |
| **Story and dialogue** | Events, lines, flags | Script edit | Event IDs < 16,777,216 (stored in 3 bytes); flags ≤ 19,999; 256 script threads; loops nest 8 deep; ≤ 16 call arguments |
| **Sprites** | New and replaced sprites, palettes | Archive overlay | `sprite_id` is signed 16-bit (1–32,767), portraits use `10000 + id`, so keep sprite numbers below 22,768. The anm cache has 0x400 slots at once |
| **Portraits, faces, icons** | New faces, cut-ins, list icons | Archive overlay | Atlases can be enlarged (Kit: thousands of faces and icons, **Verified in game (Kit)**) |
| **Effects (3D)** | New effect models | Archive overlay | **400 models** preloaded at startup (vanilla 383); more crashes the game unless loading is patched |
| **Voices** | Reassign, replace, add lines | Archive overlay + data edit | ≤ 512 lines per voice set; the line table must stay sorted by ID |
| **Music** | Replace and add tracks | Archive overlay + data edit | Music unlock index < 152 |
| **UI textures** | Replace UI art | Archive overlay | |
| **Saves** | Anything in the state | Save edit | No checksum. Units added by mods expect those mods to stay installed |

## Generic classes

What the game supports without code changes, and what needs patches (Kit CLASSES.md; **Verified in game (Kit)** for
classes at 1360, 2340 and 2630):

| Range | Status |
|---|---|
| 47 vanilla families | The carousel (Hire Character, Reincarnation) lists exactly these, from a hard-coded array. The Strategy Assembly's Set a Sub Class screen has its own 47-family array (corrected 2026-11-20; it was listed here with the carousel) |
| "Human Dummy" 1270–1355, "Monster Dummy" 2220–2335 | Empty families already in `char.dat` and `comb.dat`. Edit in place: old saves stay valid |
| 1360+ (human), 2340+ (monster) | Need the inlined "is generic" test widened (about 60 sites), `charhelp.dat` and `comb.dat` rows, and for monsters a standing picture in the resident anm pack, or the Hire list **crashes** |
| Unlock flags | Block 17001–17999; vanilla names flags up to 17282 |
| Mastery | The family → slot mapper returns −1 outside the 47 vanilla families; the unit record has room for six more (slots 47–52) |
| Sub-class | Only accepts 1000–1310 and 2010–2300 (one of about 125 such range checks: [all of them](generic-range-sites.md)); the Strategy Assembly list has room for exactly 47 rows (the screen object ends there), so a 48th family can't be added to it |

How many ranks the Recruiter and Reincarnation offer depends only on unlock flags, not on mastery or `JobRank.dat`.

## Hard limits and crash causes

| Limit | Value | What happens past it | Workaround |
|---|---|---|---|
| Table row count | 65,535 (`u16`) | — | |
| `char.dat` rows that units can use | 32,767 | The cached row index in each unit is signed 16-bit | |
| Sheets in one anm file | 255 | A cell's sheet index is one byte | Split into more files |
| Records per anm table | 32,767 | Signed counts | |
| Inserted or deleted `char.dat` rows | — | Old saves show the wrong class data | Append only, IDs above the max |
| Duplicate IDs in a sorted table | — | Undefined lookups. `char.dat` is sorted by ID when the game loads it (an unstable `qsort`, `0x1400C4E00` with comparator `0x140061D80`, build **B**), so file order doesn't matter for lookups, but **which of two rows with the same ID wins is unpredictable**. Note that vanilla `charhelp.dat` already repeats IDs 5222, 5224, 5225 and 5226 | Give every row its own ID; check against every installed mod, not just your own |
| `*DataBase.dat` / `Skill.dat` out of order | — | Lookups fail | Keep sorted by ID |
| `comb.dat` IDs out of order | — | Startup hangs | Keep sorted, reorder the matrix too |
| Missing database table | — | Game can't start | |
| Effect models | 400 | Crash at startup | Patch to load on demand (Kit). Loading on demand meets the next limit: build each model **once** per load (a loader that builds twice leaks a record per load) |
| Vertex arena records (every built 3D model) | 1,024 (25 MB) | The record scan writes through NULL and the game crashes at `0x140308C5D` (**Verified in game**, modified executable, 78 attack casts into one session) | Stock preload already uses two records per model (the loader builds twice). Build lazy models once, and refuse a load when few records remain. Layout: [functions](functions.md#the-effect-model-vertex-arena-1024-records) |
| Draw-texture pool | 16,384 objects (0x58 bytes each) | A failed decode leaves a NULL texture in a draw packet, which the flush reads without a test ([functions](functions.md#draw-packets-texture-objects-and-stale-records)) | Keep the added `N + 2` pictures resident and the palette counts unchanged |
| Monster class without a resident standing picture | — | Hire Character list crashes | Add it to the resident pack |
| Stage without `DemoCheckDataBase` row | — | Game closes on the stage list | Add a row |
| Skill with no animation event | — | Battle waits forever | The game tries `12000000 + id`, `12100000 + id`, … then `11100000`, which vanilla lacks. Add the events |
| Absolute addresses in patches | — | Crash (ASLR) | RIP-relative code |
| Hook redirected with `JMP` into a wrapper that ends in `RET` | — | The stack is off by one return address; crashes as soon as the site runs | Redirect a `CALL` site with `CALL` (`E8`), keep the stack 16-byte aligned, and reserve 32 bytes of shadow space before calling from the wrapper |
| Hook code without unwind records | — | The game's crash handler (`0x140864A07`) walks the stack and gets lost, so crash reports point the wrong way | Add `.pdata` / `.xdata` entries for wrappers that change the stack |
| Jumping to a screen by writing the scene number | — | The screen shows but isn't set up; opening Hire Character from there froze the main thread | Navigate the way a player does (talk to the NPC, cancel out one level at a time) |
| Script event ID | < 16,777,216 | Event not found | |
| Event flags | ≤ 19,999 | Ignored | |
| Overload skills with own numbers | `magic.dat` 3000–3255 | No per-unit data | |
| Unique character IDs | < 1000 | Treated as a generic class | |
| Sprite IDs | ≤ 32,767 (portrait `10000 + id` too) | Wraps (signed 16-bit) | |
| Character IDs for Absorption (6032) | < 650 | The evility does nothing for characters with higher IDs | Imported characters need their own implementation |
| Derived movement stats (build **B**, clamp table at `0x140A18780`, applied by the stat recalculation `0x14021C900`) | Move 1–32, Jump 10–99, Counter 0–9, Throw 0–20, Crit 0–100 | Values past these are clamped after level and equipment bonuses | — |
| Evility IDs | < 32,768 | Saves store each unit's evility IDs as 16-bit values | Keep new IDs below 32,768. Removing a mod while a saved unit still has its evilities probably crashes the Status / Evility screens |
| Animation IDs | ≤ 32,767 | The play call takes a signed 16-bit ID: a face at `10000 +` a 30,000-range ID is never found and the panel face is blank (**Verified in game**) | Key faces and banners on a low art number |
| Evility ID a unit holds | ≤ 32,767, never 9999 | 9999 is the free-slot marker | |
| Learnable evilities | First 1,000 `Skill.dat` row positions | A learnable row past position 999 has no bit in the save. An innate-only row may sit past it ([functions](functions.md#evility-limits)) | Keep learnable rows inside the first 1,000 positions; **never let an install move the position of a learnable row** a save already uses |
| Evility shop rows (`shop_rank ≥ 1`) | 1,000 | Fixed array, no bounds check | |
| Shop quest IDs | < 2000 | The Quest Shop board stops at the first ID of 2000 or more | |
| Quest progress slot (`sort_order`) | < row count of `Quest.dat` (max 0x400 slots) | A quest on a higher slot is never listed (**Verified in game**) | Pad the table with hidden rows (own ID as prerequisite, so never offered) so the row count exceeds the highest slot |
| Quests listed on the board | 512 | Display array has no bounds check (builder stops at 1,000 candidates) | Keep the listed total well under 512 |
| Characters in a save | 128 (army + reserve) | The unit creator refuses | |
| Dismissing a character with ID < 1000 | — | Refused as "unique" unless it is one of the 36 DLC IDs in a table in the executable ([functions](functions.md#army-size-and-dismiss)); seen in the Dismiss list in game 2026-11 (**Verified in game**, partial) | Remove such a unit with a save editor |
| Lines in one voice set | 512 | The game's scan buffer | |
| Rows in the Strategy Assembly Set a Sub Class list | 47 (a post-game unit showed 43 vanilla rows, leaving 4 free; a Warrior showed 43) | Imported classes beyond the free rows never appear; seen live: 4 of 41 shown for one unit, 0 for others (**Verified in game**, [functions](functions.md#sub-class-list-seen-in-play-and-the-current-sub-class-panel)) | The Status Class tab lists all of them (512 entries with the Kit); moving the list means moving the screen's inline rows and every reader |
| Sub-class value (`unit +0x35AC`) | Signed 16-bit, 1–32,767 | 32,768 and above skips the current-sub-class panel; a positive value with no `char.dat` row crashes the panel (null-page text pointer) | Set a vanilla sub-class before removing a class mod; a guard that skips the panel on a missing row was tested (**Verified in game**) |
| Monster row (body type 1) whose `N + 2` isn't in the resident pack | — | No body is drawn, and the game freezes when the unit is attacked (**Verified in game**; [functions](functions.md#how-a-units-sprite-is-bound-and-why-a-monster-needs-n--2-resident)) | Add `anm{N+2}` to the resident pack. Applies to unique characters too |
| Body file without animations 1001 / 1007 | — | The unit shows a shadow and HP bar only | |
| Animation whose drawing isn't at node `root + 1` | — | Wrong pose or nothing drawn (**Verified in game**; [anm](../../../formats/anm.md#how-disgaea-5-reads-each-field)) | Control root first, drawing at `root + 1` |
| Low-ID character (< 1000) with sprite ID = colour ID = its own ID and no face animation in resident block 9017 | — | Blank Status portrait (**Verified in game**) | Add face animation `10000 + graphics ID` to the resident face block |

## Crash causes found in practice

Found while installing imported classes and characters in this project (October 2026), on the Steam build. The crash or
freeze was seen in game; the cause was traced offline in the executable unless noted.

| Cause | What happens | Rule | Evidence |
|---|---|---|---|
| A **`%` in an evility description** that isn't `%d` | The Skill Shop draws the selected evility with `sprintf_s(buf, 0x100, desc, value1, value2)` (`0x1407D22B0`): the description **is a format string**. A stray `%` (e.g. "increased by 10%.") is an invalid format, which ends the process | Use `%d` for numbers and the full-width `％` for a percent sign, as vanilla does (0 of 796 vanilla rows have an ASCII `%`) | Cause **Verified offline**; crash seen in game |
| A **new class with no Status portrait bank** | Opening Character → Status reads animation group `10000 + art ID` (a tall 336 × 1080 portrait); when it's missing the game crashes reading address 2 at `0x1402F5512` (caller `0x14040F8AB`). Equipment, recruiting and battle still work, so it shows up late | Ship an `anm(10000 + art ID)` bank for every new class (a copy of a generic class's Status bank with your own picture works) | **Verified in game** (Character Forge test, 2026-10-13) |
| A **class description over 255 bytes** | The Recruiter copies the selected class's description into a fixed 256-byte field with no length check (`0x140663203`). Longer text overwrites the list-pane pointer after it, which the menu then calls through | Keep class descriptions ≤ 255 bytes. The Recruiter's help line is also **one row, clipped** at the panel edge, so in practice keep them much shorter (about 190–210 bytes already clips) | **Verified in game**: 13 classes whose 273-byte descriptions froze the Recruiter worked once cut to ≤ 210 bytes (2026-10-30); overwrite **Verified offline** |
| A class family with **fewer than 3 personality rows** (`charPersonal.dat`) | The personality page dereferences its selection without a check (`0x658D50`, image offset) | Give every class three personalities | **From decomp**; reported by Overlord's author too |
| **Fewer than 6 entries** in a class wheel | The wheel indexes `(cursor − 6 + count) % count`; below 6 the index goes negative (`0x666D19`, write through a null unit) | Keep at least 6 entries (Overlord pads short pages) | **From decomp** |
| A Skill Shop evility with `source = 3` (class evility) on a class with **no class mastery** | The eligibility check reads the class's mastery without a null check (`0x1407CDAE1`) | Imported classes use `source = 4` (character evility), owner = family | Crash seen in game (2026-10-18) |
| **Removing a custom class** while a save still has a unit of it | The save loads, then a scene writes through a null pointer (`0x1406B08DC`) | Keep the class installed, or move the unit to another class first | Seen in a controlled test |
| No audio device under Wine / Proton | Crash at boot inside XAudio2 (`+0xC88E`) | Leave an audio device enabled | Seen in game (Wine) |

Not a crash, but easy to get wrong:

- **Class titles over 18 characters** overrun their box (vanilla's longest generic title is 18; class *names* can be
  longer).
- **Voice gender:** `voice_id` picks the voice set and the ones digit of `kind_gender` the gender; a male class with a
  female vanilla voice set speaks with a female voice.
- **Row number is not ID.** A table patch that names rows by position lands somewhere else once IDs have gaps: in
  vanilla tables row 14 of `Skill.dat` is ID 50 (Super Crush), not ID 14 (Fatal Hit); row 30 of `char.dat` is ID 310
  (Brave), not 30 (Killidia); rows 1012–1019 of `item.dat` are IDs 1302–1309 (belts), not the medallions 1012–1019.
  When converting a mod between tools, check whether its keys are IDs or row positions. A mod pack converted this way
  silently changed about 1,000 wrong rows with no warning (**Verified in game**: read back from the installed
  `Database_0.dat`, D5 Mod Kit fork live test, 2026-10-30).
- **Learned evilities are stored by row position, not ID.** Each unit keeps one bit per `Skill.dat` row (1,000 bits,
  `unit+0x2E70…+0x2EEC`), and bit *n* means "the *n*-th row of the sorted table". **Inserting** a `Skill.dat` row with an
  ID below existing ones shifts every later bit, so **saved units silently learn or lose different evilities**. Add
  new evilities with IDs above the current maximum (append only), the same rule as `char.dat` rows.
- **Which evilities the Skill Shop offers a unit:** the learn list (built by `0x1407CDE50`) walks `Skill.dat` **in
  table order** and keeps a row when all of these hold (checked by `0x1407CD9C0`): `kind` is not 1 (unique), `mana_cost`
  (`+0x4C`) is at least 1, `version_group` (`+0x5C`) is 150 or less, `owner / 10` equals the unit's class ID / 10
  (`unit+0x2E04`), `shop_rank` (`+0x50`) is below 1, and the unit doesn't know it yet. The other sort order uses
  `slot_cost` (`+0x18`). So a new class's evilities show up in the shop only if their `owner` shares the class's tens
  and `shop_rank` is 0. **From decomp.**
- **Skill Shop list size:** the Skill Shop's learn list (16-byte entries at screen `+0xFF48`, count `+0xFE7C`, built by
  `0x1407CDE50`) is filled **without a bounds check**; the space before the next field holds about **800 entries**
  (inferred from the screen object's layout, not tested). With very many learnable evilities this would overwrite the
  screen. (It is **not** the cause of the open Skill Trainer crash in the D5 Mod Kit fork's playtests: that crash
  reproduces with a 41-row list, as a C runtime invalid-parameter fast-fail at `0x140116224`; its cause is still open.)
- **Write a literal percent sign as the full-width `％`,** the way vanilla does. Descriptions go through `printf`-style
  formatting, on some paths more than once (the D5 Mod Kit fork's research), so even an escaped `%%` can come back as
  a live `%` on the second pass and be read as a conversion.
- **New innate evilities work through `char.dat`:** add a `Skill.dat` row (`kind` 1 for a unique evility, a
  non-zero `category`), point the character row's `evility1` at it and set `evility_count` to 1. Three new unique
  evilities set up this way (IDs 8890–8892 on imported characters) showed their own name on the character list's
  Evility page and their own description on the "change Evilities" screen (**Verified in game**, D5 Mod Kit fork,
  2026-11-01).
- **Give new evilities a `category`** (`Skill.dat` `+0x44`; `subcategory` `+0x48`). The evility screens filter on it
  (the equip pick list keeps a row only if the tab is 0 or matches its category or subcategory), so a row with category 0
  is visible on the unfiltered tab and **vanishes from every category tab**. All 587 learnable vanilla evilities have one;
  copy the category of a vanilla evility with a similar effect.
- **`Skill.dat` past 1,000 rows:** the bit test and setter (`0x14015E630` / `0x14015E6D0`) refuse indices of 1,000 and
  up, so rows past that can't be learned that way. Simply raising the limit would write into the fields that follow
  the bitmap (the innate list starts at `+0x2EEE`). Several functions also set bits inline instead of calling the
  setter (for example in the shop and result screens): in all, **143 code sites** test or record a learned evility ([every site](learned-evility-sites.md)), and a
  capacity extension has to cover every one (the D5 Mod Kit fork's extension does). Whether rows past
  1,000 cause other trouble is **Unknown** (they were a suspect in one crash).

- **Hire Character freeze with added class packs: two causes found** (**Verified in game**, D5 Mod Kit fork live tests
  under Wine, 2026-10-31 to 11-02):
  1. **Image headers must be little-endian.** Each anm image header has a marker field (see
     [anm](../../../formats/anm.md)); when its low 16 bits aren't 1 the engine reads that image's width, height and size
     byte-swapped, so one picture becomes many texture sub-resources. Nine Disgaea D2 class packs carried 280 such
     headers; together they exhausted the texture-upload pool and the Recruiter froze on its first frame (any one pack
     alone worked). With every marker set to 1 the wheel opened with all 110 entries and stayed stable.
  2. **Don't add palettes or image headers to a bank you replace.** Adding ten palettes to one class's bank froze the
     Recruiter page after 65 presses; the same new art mapped onto the bank's existing palettes passed 432 presses over
     two boots. Pixel and animation-table changes of the same size passed. Why extra palettes freeze it is **Unknown**;
     keep each replaced bank's palette and image-header counts the same as what it replaces.

## Freezes and the class-page crash (2026-11)

Seen in the D5 Mod Kit development fork's live soaks under Wine on the Steam build (modified executable). Soaks
scroll a class wheel with one key press about every 1.3 to 2 s.

- **What the freeze was.** Seven freezes across about 55 soak runs, all on the Recruiter's Disgaea 4 class page
  (18 rows), most while scrolling backward over rows 12 to 7. Capturing one showed the game had not hung: the frame
  thread took an unhandled page fault (read at `0x24`) at `0x1402F3744` and Wine's "Exception raised" box sat on top
  (**Verified in game**). The flush read a draw packet whose texture pointer was NULL
  ([functions](functions.md#draw-packets-texture-objects-and-stale-records)). Earlier suspects (a graphics or Wine stall,
  the Kit's runtime waiting on the frame thread, an environment-only exception) were ruled out by that capture.
- **A NULL guard hides it, partly.** Skipping NULL packets at that site caught 8 packets in 2,445 presses with no
  crash, against one crash per 1,847 presses before (**Verified in game**). A later run crashed at press 81 with a read
  at address 5 at `0x1402F7A4C`, a second read of the same texture field in the emitter, after the record's memory had
  been reused (junk pointer, not NULL). The pointer value is inferred from the fault address.
- **Cause and fix (likely, not proven).** The page's eight humanoid classes shipped `N + 2` as a loose file, while all
  80 vanilla humanoid rows keep it in the resident pack. Adding those blocks to the resident pack removed the
  symptom: 2,519 presses on the same page, no crash, guard counter 0 (**Verified in game**, one boot). Whether the
  Hire Character freeze noted above has the same cause is **Unknown**.
- **Not covered:** humanoid uniques with a loose `N + 2`, other pages, Windows or Proton, and a real keyboard
  (**Unknown**).

## Patching the executable safely (D5 Mod Kit development fork)

Mods can carry byte patches as data (`patches/exe`). The fork's rules show what a safe design needs. The tests are
**Verified offline**.

- **Site ledger.** Every executable byte range a feature writes is claimed under an owner (`mod <folder id>`, since
  display names repeat). A second claim on the same bytes is refused with both owners named. A feature that only
  checks a site must not claim it; every in-place write, even a one-byte loop bound, is claimed at the write.
- **Checked patches.** An entry gives an address, the original bytes and the same number of new bytes; the original is
  compared first. Entries inside the fork's own generated sections, or not wholly inside one original section, are
  rejected. At most 256 bytes per patch and 4,096 per mod.
- **Whole mod or nothing.** A mod that is unapproved, overlaps, has unreadable data or whose bytes differ is left out
  whole, and the log says why.
- **Consent.** Such a mod is off until the user approves it. The approval is stored outside the mod and bound to a
  fingerprint of its patch files and settings, so an edit needs a new approval; a bulk import never approves. The
  patches are read once and the same bytes that were checked are the ones written.
- **Equivalence.** With the gate in place the build of the fork's main 306-mod profile produced a byte-identical
  executable (14,377,984 bytes, SHA-256 `5bff43ba…`). Adding the sub-class hook and its guard moved the claimed-site
  count from 579 to 581 (two new ranges) and later to 583 as other hooks were added, with no code byte changed outside a claimed site.
- A few bytes can redirect execution, so approve only mods you trust.

## Save notices

- The Kit's "this save needs a disabled mod" notice appears only when a roster unit's class (`+0x2E04`) has no
  `char.dat` row and the game's row fixer returns −1. It checks classes only, not items or stages. In one report the
  notice appeared for a save whose 60 units used no mod class (the load log held no matching line), so the cause of
  that sighting is **Unknown**.

## Mod conflicts to know about

Full list of ID ranges in use: [id-registry.md](id-registry.md).

- **Two class systems.** Overlord (Cryswar) and D5 Mod Kit both add classes, and both use unlock flags from 17283,
  mastery slots 47–52, the class-menu functions, and `char.dat` rows 1350–1355. Installing both without agreeing on who
  owns these is unsafe. **Community-reported** (project assessment, 2026-10).
- **Whole-file replacement.** Mods that ship a full `Database_0.dat` or `disgaea5.exe` (Disgaea Unleashed) overwrite
  every other mod's changes to those files.
- **D5 Mod Kit installers** refuse a game that is already modified, or a different build, and change nothing.

## Open questions

- Where is the 99,999,999 stat cap enforced, and what else did Unleashed patch?
- Which functions control turn order, the command menu and Move/Jump totals?
- What breaks first when a save made with a mod is loaded without it?

## Sources

- D5 Mod Kit by eraser4521: [EXE_ANALYSIS.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/EXE_ANALYSIS.md), [CLASSES.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/CLASSES.md), [OUTFITS.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/OUTFITS.md), [OVERLOADS.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/OVERLOADS.md), [SKILL_ANIMATIONS.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/SKILL_ANIMATIONS.md), [MAPS_AND_OBJECTS.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/MAPS_AND_OBJECTS.md), [VOICES.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/VOICES.md), [SHARING.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/SHARING.md).
- D5 Mod Kit development fork (Sean13128): `docs/EVILITIES.md`, `docs/MASTERY.md`.
- Overlord/Kit overlap: prior-art assessment in this project's research (2026-10-26).
- [Disgaea Unleashed](https://steamcommunity.com/sharedfiles/filedetails/?id=2926099213) (ninakoru): stat cap and install method.
- [disgaea5autoplay](https://github.com/Grid487/disgaea5autoplay) (Grid487): proxy-DLL method.
- Limits added 2026-11: this project's research, published in the D5 Mod Kit development fork's `docs/IMPORT_GATES.md`, `docs/EVILITIES.md`, `docs/CHARACTER_BANK.md`, `docs/ANM.md`, `docs/EXE_ANALYSIS.md` and `docs/CLASSES.md`; the November 11–20 additions are from this project's reports on the arena, sub-class, freeze and patch-safety work.
