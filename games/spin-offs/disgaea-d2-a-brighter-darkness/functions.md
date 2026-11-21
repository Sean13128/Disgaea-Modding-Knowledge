# Disgaea D2: A Brighter Darkness — Engine Functions

**Executable:** `EBOOT.BIN` (PS3, PowerPC PPU + SPU). Versions 1.00 (disc) and 1.40 (update).

## What's mapped

There's no decomp index for D2 (Brighter Darkness) in this project. But the whole game has been **statically
recompiled** to native code by the [Disgaea D2 native port](https://github.com/Sean13128/disgaea-d2-recomp) (macOS) and
its [Windows fork](https://github.com/LUUKASR1/disgaea-d2-recomp-windows), using
[ps3recomp](https://github.com/sp00nznet/ps3recomp). The recompiled code is generated on each user's machine from their
own game and isn't published, but the ports' hooks name guest functions in the 1.40 executable:

| Guest address (1.40) | Role | Evidence |
|---|---|---|
| `0x001840E0` | TXF texture decode (receives the file + 4 bytes, after the `txf\0` magic) | **From decomp** (port tools) |
| `0x000A8FE0` | Temporary actor constructor caller, used when a skill spawns a stand-in actor | **Verified in game** (port: costume shows during Prinny Raid) |
| `0x000E849C` / `0x000E83BC` (called first by `0x00295EF8`) | Controlled-actor teardown / rebuild | **Verified in game** (port: live costume switch in the castle) |
| `0x0034FE34` / `0x0034F8D8` | Table binder (`{count, rows}`) / class-row lookup by ID (1.00: `0x003400E4` / `0x0033FBB8`) | **From decomp** |
| `0x00350168` | Data-table loader (file size, allocation, then binder) | **From decomp** |
| `0x00020CAC`, `0x00015E18` | Unit refresh: cache the class row index at unit `+0x115C`; read the row without a bounds check | **From decomp** |
| `0x0002E218` | Free party slots (128 minus two counters) (1.00: `0x0002E2FC`) | **From decomp** |
| `0x00015490` | Battle actor descriptor search, 160 slots (1.00: `0x000152B0`) | **From decomp** |
| `0x00180F74`, `0x00181438`, `0x00181B78` | Sprite resource pools; find or load `anm%05d.lzs`; load states 1 → 2 → 3 | **From decomp** |
| `0x00109350` | Sprite number from row `+0x196`, + 10,000, + colour byte (1.00: `0x00101BAC`) | **From decomp** |
| `0x00109E44`, `0x00067C28` | Body selector at unit `+0x117A`; post-load body reset | **From decomp** |
| `0x0010E77C`, `0x00109FF0` | Unit creation from a row (name, aptitudes); growth seeds | **From decomp** |
| `0x00062CD0`, `0x000619CC`, `0x00054AF8` | Item constructor, item recalc, item grade from rarity | **Verified in game** (port's Item Editor) |
| `0x00068FF0` → `0x00094B00` → `0x0015AD84` → `0x00149E80` | Generic list rows that draw a face from face ID + colour only | **Verified in game** (port's Choose Color work) |
| `0x00164C3C` / `0x00164D74` | Save-data load / save file callbacks (secure `SAVEDATA.DAT` + `ICON0.PNG`; slots `%s_NORMAL_%02d`, `%s_AUTO`). List callbacks: title `0x0017D410`, load `0x0016382C`, save `0x0016440C` | **Verified in game** (port saves and loads retail saves) |
| `0x00306474`, `0x00308234` | Sound mixer start / mixer main thread (start can race the thread's queue set-up) | **Verified in game** (port) |
| `0x00160C68`, `0x00160BF4` | Streamed music refill / retire a line on any decode error | **Verified in game** (port) |
| `0x00033CBC`, `0x00033C48` | Destroy hub messages / are message queues empty | **Verified in game** (port: needed before warping into battle) |
| `0x002C0C44` | Frame path: convert-and-flip, then wait for flip | **From decomp** |

The code reads `char.dat row + 0x194` as the class ID and `+0x1BC` / `+0x1BE` as the primary / alternate body
resource (**From decomp**).

**Executable notes (2026-11, Verified offline):** the retail `EBOOT.BIN` files (disc and update 1.40) are encrypted
SELF containers. Decrypted, the program is a big-endian 64-bit PowerPC ELF with its code at virtual address 0x10230
(file offset = address − 0x10000) and about 9,950 functions. Addresses in the sections below are disc (1.00) ELF
virtual addresses unless a 1.40 value is given. Six-rank class tables sit at 0x40AFA0 and 0x40AFB4, unique ordering
lists at 0x395D90 and 0x395DD6. `script.dat` is 8.2 MB of script bytecode; its event language is described below.

**Angel and "flat" are hard-coded (2026-11-17, From decomp, disc and 1.40 checked separately; replaces the earlier
note that the executable has no compare against 1170 / 1180 / 1185):** the angel test compares unit `+0x11FC`
(class ID ÷ 10, written once, at unit creation) with 117 and 118, so it matches the 12 generic angel rows 1170–1175
and 1180–1185 and no named character. The "flat" test is a function at `0x1A274` (1.40: `0x1A324`). Details and
member lists are on [data.md](data.md#angel-and-flat-groups-evilities-140-and-550).

**Evility helpers (From decomp, 2026-11):** `0xF33E4` (does a unit's data hold evility N: it walks the four slots,
stride `0x190`), `0xF3024` (an evility's value byte, the low byte of the u16 at mskill `+0x0C`; a second form reads the cap at `+0x0E`), `0xF6290` (counts
battle actors holding evility N: 162 actors, stride `0xAD0`, skips the caller; mode 0 requires the same side byte at
`+0x6EB`, mode 1 a different one). Unit fields: `+0x1158` class / character ID, `+0x115C` row index, `+0x11FC` ID ÷ 10,
`+0x11FE` ID ÷ 100, `+0x1188` a creation parameter (meaning open), `+0x522` panels moved this turn (zeroed at end of
turn), `+0x19CE` defeats the unit made (see [data.md](data.md#signature-evilities-read-from-the-executable)). Several
liveness filters inside `0xF6290` (`0x31A1C`, `0x1035B0`, `+0x262`) were not decoded, so it is **Unknown** whether
units on the base panel or dead units count.

## Special-attack scenes

Skill animations are scripted. In `script.dat` each event is a small program; the root event of a skill is
`12000000 +` the skill ID (native table at `0x1FDAC0..0x1FDAD8`).

- **Same language as Disgaea 4's scenes, in PS3 byte order** (**Verified offline**, 2026-11-20, from the D5 Mod Kit
  development fork's DD2 reader). 490 of the executable's 500 typed command registrations have exactly Disgaea 4's
  operand widths; the other 10 are sound and voice commands with wider sound IDs. Six are custom forms that
  Disgaea 5 frames the same way. Operands are tagged slots (a literal of the declared width, or a 16-bit variable).
  12,707 of 12,710 events frame and parse with the Disgaea 4 reader; the three that don't contain commands no special
  uses.
- **Actor numbering follows Disgaea 4:** caster 1064, main target 151, area targets 160–191, "every target" 202,
  scene-created actors from 1000. Damage is a per-hit command plus one finalizer per scene.
- **The scenes are DD2's own.** For the 34 skill IDs that exist in both Disgaea 4 and D2 with a scene, the traced
  command streams differ in all 34.
- **Static conversion works; capture was not needed.** Offline, the 59 D2 specials that Disgaea 5 ports as
  stand-ins (58 distinct source skills) go through the same converter as Disgaea 4's
  ([D4 functions](../../mainline/disgaea-4-a-promise-unforgotten/functions.md)): 57 convert, 55 pass every offline
  gate (single-target and every 2..n-target body, occupancy, timeline, skill budget, used IDs) and are staged.
  Two converted attacks are held by gates (a scene that puts the target on a panel ID the converter can't map; two
  caster motions missing from the D2 bank), and two don't convert (one waits on variables the engine, not the script,
  writes, and its writer wasn't found; one has no scene in D2 1.00 at all).
- **Rules the conversion needed:** a call to an event D2 doesn't ship does nothing (the search returns −1 and starts no
  thread); a later finalizer on an already-finalized target is dropped; clones of an absent area target are absent;
  writes to a variable nothing reads are accepted. Each was tested on real bytes with a negative control.
- **Scene-created actors:** 185 across the 55 scenes. 35 get a visible Disgaea 5 look (a same-name D5 character, a copy
  of the target, a weapon dummy); 150 are invisible helpers (D2 event sprites and item looks that D5 has no look for).
- **Voice and sound:** a scene's "voice by actor" calls carry over (904 calls in 44 scenes), routed through the voice
  column mapping on the [data page](data.md#voices). Voice by clip ID (98 calls) and sound effects (3,899 calls) do not:
  no map from D2's clip or effect IDs to Disgaea 5's exists.
- **Size:** the combined `script.dat` with the D2 and D4 packs is 41.7 MB and 23,176 events, 3.9× vanilla; **boot with
  that size is Unknown** (not run).
- **Capturing from the running game was weighed and skipped:** it would only give one facing, one target count and one
  random draw per cast, and needs the same conversion. It is useful as a timing oracle.

**One converted attack crashed Disgaea 5 on a test profile** (Sea Dome Tragedy, cast on 2 targets; **Verified in game**, in D5 build B with a modified executable).
Offline analysis could not find the cause (**Unknown**). The fault is a write through a stray pointer, not a field
overrun: the game's allocator handle (a global in the executable's data) was overwritten, so every model field the
engine follows was range-checked for all converted models and none was out of range. No model, scene or voice feature
unique to that attack was found; 20 D2 and 39 D4 attacks also clone the caster. So the cause is not tied to the D2
conversion, and the Disgaea 4 set is **at risk, not cleared** (52 of its 77 attacks have not been cast).

## Voice selector and script voice calls
See [data.md](data.md#voices) for the columns. Three paths exist: the selector wrapper (27 native call sites, all
found), the script voice commands (803 literal calls with slots 0–10, plus 6 with a variable operand), and a
direct-play command that names a clip ID and bypasses the selector (339 calls in 80+ events). **Verified offline**
(static call-site map, 2026-11-14).

## Open questions
- A public map of the battle, menu and data-loading code.
- What writes the variables two special scenes wait on (the engine, not any script).
- Whether the executable's unit-liveness filters count base-panel or dead units for the angel and flat counts.

## Sources
[Disgaea D2 native port](https://github.com/Sean13128/disgaea-d2-recomp) tools and reports (Sean13128); D5 Mod Kit development fork `d5lib/dd2source.py`.
2026-11-11 to 11-20 additions (scene conversion, evility helpers, angel / flat tests, voice call map): the D5 Mod Kit development fork's DD2 scene reader and converter, its offline test runs on the decrypted executable, and static call-site surveys. **Verified offline** unless marked.
