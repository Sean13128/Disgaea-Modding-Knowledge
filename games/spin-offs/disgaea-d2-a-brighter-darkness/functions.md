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

**The crash seen with one converted attack was not caused by the special-attack set (updated 2026-11-21; replaces the
earlier note that the cause was Unknown and the Disgaea 4 set "at risk, not cleared").** A converted Shark Dragon
attack (Sea Dome Tragedy) crashed Disgaea 5 on a test profile. A later live capture reproduced the same memory
overwrite with that unit's **normal attack** on the unmodified profile, so no D2 scene, voice or effect model is
involved (**Verified in game**, D5 exe `f789ddb7`, 7 of 7 runs; the other D2 casters' 13 attacks and the whole D4 set
never wrote it). A software write watch named the writer: a Disgaea 5 function (`0x1402F9550`, called from the
basic-attack start) copies as many palette colours as the bank's palette-info count (the 32-bit value at row `+4`) into a
fixed 256-colour buffer, with no clamp. D2 stores that count **big-endian** (`00 00 01 00` = 256); Disgaea 5 reads it as
16,777,216 and overwrites memory after the buffer. Full mechanism and the Disgaea 5 side: [Disgaea 5 functions](../../mainline/disgaea-5-alliance-of-vengeance/functions.md#the-attack-palette-copy-has-no-bound).
**From decomp** with **Verified in game** captures and a live fix. Ten of the 307 installed packs that carry banks had such counts
(ten DD2 monster class packs); no other game's pack did. Writing 256 into the count bytes fixed them: the same actions then ran
without the overwrite on all ten classes (attack twice and a hit taken each), while the unchanged control pack crashed in 3 of 3 runs. The format point is on
[assets.md](assets.md#palette-counts-and-disgaea-5).

## Sprite, animation and cut-in code (1.40 addresses, 2026-11-24 to 11-25)

Read from the decrypted 1.40 executable by the D5 Mod Kit development fork's source-format work. Grades: **proven** =
the fork's reader gives the same answer as the recompiled game function on thousands of inputs (**Verified offline**,
and the reader fails when the rule is broken); **reading** = code read, not run (**From decomp**). The recompiled
functions are compared as C++ built from the 1.40 code, not the PowerPC image under an emulator (no PowerPC emulator
available to the fork ran any function).

| 1.40 address | Role | Status |
|---|---|---|
| `0x2EE2F0` | Motion select: take `abs(id)`, first matching row of the record's animation table, else row 0; resets the node tree | **Verified offline** (2,932 cases, 0 mismatches) |
| `0x2EDB64`, `0x2ED964` | Actor step: Q16 fixed-point time; the root node's control key (key byte `+2`: 1 loop start, 2 loop back, 3 and 4 conditional, 5 end); speed from `+0x1C`, `+0x2C` and a global; replay loop for the loop flag | **Verified offline** (2,155,776 cases, 0 mismatches). The length of a tick in seconds isn't proven |
| `0x167F8`, `0x169BC` (`0x16BCC`; dispatcher `0x1D3FC`, unit byte `+0x257`) | Facing to motion digit: 4 quadrants (1, 7, −7, −1) or 8 sectors (2, 1, 4, 7, 8, −7, −4, −1), from one camera yaw (`ctx+0x128` × 57.29674); `0x16BCC` is a third 4-way form, not ported | **Verified offline** (6,864 cases) for the first two |
| `0x107AB4`, `0x109350` | Cut-in bank number (see [assets](assets.md#cut-ins-and-face-icons)) | **Verified offline** (3,906 cases) |
| `0x2F92E8`, `0x2F903C` | Voice selector and clip-route lookups (below) | **Verified offline** (23,642 cases) |
| `0x182F1C`, `0x183084` | Archive lookup: scan the registered packs from the last back to the first, take the first name match | **Verified offline** (test with a broken variant) |
| `0x29804` (`0x1F26C`, `0x2A25C`) | Body loader: bank number = body + offset | reading |
| `0x2F19D0` (copy `0x2F3094`) | Per-node draw: builds a `0xC0`-byte record on a global queue (flush `0x2ED140`, sprite draw `0x2DC710`) | reading: it compiles but was not run, because it needs a loaded record and tree that nothing offline builds |

**Draw details read from `0x2F19D0`** (reading): a non-zero cell byte `+6` negates the local x, `+7` the y; cell byte
`+5` selects a blend class (2, 3 and 4 map to themselves, anything else is 1); cell `+4` (u16) and `+16` (byte) are
forwarded into the record and used by the draw and queue-sort code, which wasn't decoded. A key interpolates toward
the next key when its control byte (`+2`) is non-zero and a next key exists, and key byte `+3` = 6 interpolates the
cell rectangle. Children are submitted before their node. A node's binding nibble selects a bound record from the
actor's slot table: slot 0 is ref 1 (the body bank, bound by the body loader), slots 1 to 8 are refs `0x0A`..`0x11`,
9 is `0x15`, 10 is `0x1F`, 11 to 17 are `0x6E`..`0x74` (weapon mounts), 18 is `0x63`; equipment code binds the
others.

**Where D2 differs from D4's animation code** (each checked in code or data): every file is big-endian (D4 little);
key byte `+2` is both the control and the interpolate flag and byte `+3` the attach flag, as bytes (D4 reads one
16-bit flags word); cell words are byte fields (`+4` palette bank, `+5` blend, `+6`/`+7` flips) where D4 has 16-bit
words; node words 0 and 1 are one big-endian bitfield (id 24 bits, binding 4, mode 4); transforms carry the pivot
index at `+6`; texture info rows are 12 bytes and cells 18 (16 in four effect banks); looping motions replay their
steps on restart.

**Voice call chain (1.40; the 1.00 wrapper is `0x6B240`):** wrapper `0x6C1DC` takes (category, column) and reads row
`+0x1B8` as the voice key; a non-zero `+0x28E` replaces the variant argument by its byte − 1. Selector `0x2F92E8` scans
540 rows × `0x58` bytes at `0x4504F0` (1.00: `0x3D04F0`) for (category, key, variant) and returns the list at
`row + 0x0C + 4 × column` (19 columns). `0x2F5E6C` counts the −1-terminated list and picks uniformly with the PRNG at
`0x1A9A68`. The chosen ID goes through `0x2F903C` / `0x2F5D10`: the English table at `0x4AF288` (4,739 rows) or the
Japanese table at `0x4C1AB8` (8,631), rows of big-endian ID, pak, bank, clip, gain, rate and one more word; the dub is
chosen by an option byte (`+0xED`). The script opcode `0x4632` (handler `0xD6A2C`) splits its operand into
column (÷ 10,000) and actor (mod 10,000). Both builds' selector tables are identical (20,114 entries). Clips are VAGp
PS-ADPCM, mono, 22,050 Hz; the fork's decoder matches ffmpeg exactly on 64 clips (**Verified offline**: two independent
decoders). Not run offline: the random pick, the dub option byte, and how the PS3 lays the update over the disc.

## Voice selector and script voice calls
See [data.md](data.md#voices) for the columns. Three paths exist: the selector wrapper (27 native call sites, all
found), the script voice commands (803 literal calls with slots 0–10, plus 6 with a variable operand), and a
direct-play command that names a clip ID and bypasses the selector (339 calls in 80+ events). **Verified offline**
(static call-site map, 2026-11-14).

## Open questions
- A public map of the battle, menu and data-loading code.
- What writes the variables two special scenes wait on (the engine, not any script).
- Which game logic picks sprite body offsets 1 to 3 and what row `+0x1BC` holds beyond the alternate body (see [assets.md](assets.md#sprite-banks-for-a-character)).
- Whether the executable's unit-liveness filters count base-panel or dead units for the angel and flat counts.

## Sources
[Disgaea D2 native port](https://github.com/Sean13128/disgaea-d2-recomp) tools and reports (Sean13128); D5 Mod Kit development fork `d5lib/dd2source.py`.
2026-11-11 to 11-20 additions (scene conversion, evility helpers, angel / flat tests, voice call map): the D5 Mod Kit development fork's DD2 scene reader and converter, its offline test runs on the decrypted executable, and static call-site surveys. **Verified offline** unless marked.
