# Disgaea PC — Scripts (TALK.DAT and SCRIPT.DAT)

How Disgaea PC runs dialogue, menus, cutscenes and map events. Both script files live in `SUBDATA.DAT` →
`START.DAT` (decompress `START.DAT` first; the game accepts it uncompressed). Full command references:
[TALK.DAT](https://github.com/ProgSys/pg_disatools/wiki/TALK.DAT) and
[SCRIPT.DAT](https://github.com/ProgSys/pg_disatools/wiki/SCRIPT.DAT) on the pg_disatools wiki.

**Evidence:** **Community-reported**: pg_disatools wiki and source (ProgSys), building on Krisan Thyme's toolpack.
pg_disatools' map editor writes custom talk scripts that run in the game, so the core commands are tested in practice.

## Two languages

| | TALK (`TALK.DAT`) | SCRIPT (`SCRIPT.DAT`) |
|---|---|---|
| Job | Dialogue, portraits, choice menus, opening game menus (area select, shop, assembly …); the entry point to stages | Camera, units on the map, animations, sound and music, map logic, skills |
| Command shape | 1-byte opcode + fixed arguments | 1-byte opcode + 1-byte body size + body |
| Calls the other | `Script` (`0B`) runs a SCRIPT; it runs **in parallel**, and `Script(0)` waits for it | `LoadTalkScript` (`11`) |

## TALK.DAT

**File:** `u32 count`, `u32 count` again, then one 32-byte entry per script: start offset (not counting the header),
script ID, dungeon ID, a search flag (mostly 0–2 and 102–111), the number of positive and negative state filters (0–4
each), four positive and four negative filters.

**How the game picks a script:** by ID, or by dungeon and search flag; then every positive filter must name a **set**
bit of the global game state and every negative filter a **clear** one. That's how story variants of the same scene
are chosen. A filter is 16 bits: the low 3 bits pick the bit, the other 13 pick the byte in the game-state array, so the
whole value is a flat bit number, the same one the `Set` / `Clear` commands use. Known flag: byte 275 bit 0 = skip the
stage's opening story.

**Commands (selection):**

| Op | Name | What it does |
|---|---|---|
| `01` | Dialog | One line of text (Shift-JIS, ≤ 128 bytes, zero-terminated); up to three per box |
| `02` / `03` | Confirm / ConfirmClose | Wait for the player; `03` also closes the box (used when the speaker changes) |
| `65` | ConfirmAuto | Continue after a delay (a second delay applies when a state flag is set, probably the voice language) |
| `64` | LoadImg | Load story picture `BUnnnn` into one of 7 slots (`slot × 10000 + picture`). **Loads asynchronously: use a slot before it's loaded and the game crashes**; `Script(0)` waits |
| `04` / `0E` / `18` | CharLeft / CharMid / CharRight | Show a loaded slot; entrance as `speed × 100 + type` (instant, slide, fade …); flags mirror / lower it |
| `05`, `06` | Portrait, Voice | Portrait and voice line by ID |
| `07`, `08` | SpeechBubble, NameLabel | Bubble style (normal, shout, thought) and arrow; name tag position 1–4 |
| `09` / `0A` | Set / Clear | Set or clear a game-state bit |
| `0B` | Script | Run a SCRIPT.DAT script |
| `0C` | Call | Jump to another talk script. **There's no call stack**: the current script doesn't resume |
| `14` … `FD` | Menu | Choice menu: entry count, then each entry's text and the code for that branch; `FD` marks the end |
| `C9` | ShowBox | Open the background box |
| `FE` / `FF` | Restart / Return | Start over (used for menus) / end |

## SCRIPT.DAT

**File:** `u32 count`, `u32 0`, then `count` offsets, then `count` IDs, then the bytecode.

**Slots** pick the actor a command acts on: 0–63 units placed by the map; 64–95 actors private to the running script
chain (released when it ends, which is why a cutscene's cast disappears without being removed); 101–108 the acting
group; 151 the target (the unit a skill is aimed at); 160–191 a third unit list; 200 the object the script started on;
202 "every unit of the target group"; 203 the teleporter panel; 210 the stored event position. Actors themselves are in
**fixed arrays of 64 records** (one for the map, one for scripts); when all 64 are in use, spawning silently does nothing.
Commands given an invalid slot are usually skipped, sometimes they crash.

**Camera:** an orbit camera (look-at point, pitch, yaw, distance). Each scene starts at distance 750, pitch −30, yaw 0,
lens 320; most scripts then set yaw −45 for the usual diagonal view.

**Command names.** The wiki documents 33 commands in detail; pg_disatools' source names **93 opcodes**, and its
script test runner gives every one an argument layout: see the **[command reference](scripts-reference.md)**.

| Area | Opcodes |
|---|---|
| Flow | `00` Error, `01` Sleep, `02` Return, `03`/`04` RepeatStart/End, `07` If, `08` SetFlag, `09` Nop, `0B` SleepSkippable, `11` LoadTalkScript, `12` RunScript, `53` RequestTalk, `54` WaitForFlag, `61` RunScriptOnActor, `98` SetUnitScript, `C8` RunFunction, `C9` SetSceneMode |
| Player and screen | `05`/`06` LockUser/UnlockUser, `0A` SetFade, `0C` ShakeScreen, `0E` SetFadeColor, `1B` SetScreenColor |
| Maps | `15` SetDungeon, `16` EnterDungeon, `17` LoadDungeonGeometry, `18` WaitForMapLoad, `70` SetMapTile, `27` SetCursorPos |
| Camera | `28` SetCameraMode, `29` SetCameraZoom, `2A` RotateCamera, `2B` SetCameraFOV, `2C` SetCameraPos, `2E` SetCameraRotation |
| Text and portraits | `32` Text, `33` ClearText, `34` WaitForUser, `37` ShowText, `38` ResetTextScroll, `39` LoadPortrait, `3A` ShowPortrait, `55` WaitForPortrait, `5C` SetTextColor |
| Party and rewards | `46` SetUnitField, `4E` SpawnPartyMember, `4F` AddPartyMember, `50` AddMoney, `51` GiveItem, `5A` LoadData |
| Actors | `1C` SetActorRawPos, `23` ClearActorData, `24` SetRenderGroup, `2D` SetActorGravity, `64` MoveActor, `65` SetActorPos, `66`–`68`, `6B` animation commands, `69` SetActorGroup, `6A` SetActorPoseDir, `6D` SetActor, `6E` SetActorSprite, `73` SetActorRotation, `77` OrbitActor, `78` DoAction, `79`/`CA` SetActorFlag/Flag2, `7A` SetMultRGBA, `7B` SetActorPalette, `CB` SetActorDepth |
| Effects | `6F` PlayEffect, `71` PlayAimedEffect, `72` PlayEffectEx, `74` PlayAimedEffectEx, `CC`–`CF` the same four by effect ID, `D0` SetEffectParam, `D1` SetEffectValue |
| Sound | `82` LoadStream, `83` PlayStream, `87` PlayVoice, `88` StopVoice, `8C` PlaySound, `8D` FadeSoundVolume, `91` PlayBGM, `92` FadeBGM, `93` PlayBGMDirect, `94` PlayBGMWait, `95` PlayBGMAt |

(The wiki calls `0A` FadeOut; the source's newer name is SetFade.)

**`If`** has a two-byte size (so its body can pass 256 bytes), then comparisons `left op right` with operators
`0A` == · `0B` < · `0C` > · `0D` <= · `0E` >= · `0F` !=. **Only one or two comparisons work: with three or more the
block always runs.** Two comparisons are joined by AND or OR depending on an internal setting.

## For modders

- New dialogue and campaigns: pg_disatools' Map Editor compiles a text form (`.tscript`, with the command names above)
  into talk scripts and adds them through its *Campaigns* menu, so the game's own files stay untouched.
- Wait for pictures to load (`Script(0)` after `LoadImg`) before showing them.
- A `Call` never returns; structure dialogue so each branch ends cleanly.
- Keep `If` to two comparisons.

## Open questions
- What the arguments of the less-used commands mean in practice (the layouts are known; names come from pg_disatools).
- What the TALK search flag means.
- The full list of game-state bits (only "skip story" is named).

## Sources
[pg_disatools wiki](https://github.com/ProgSys/pg_disatools/wiki): TALK.DAT, SCRIPT.DAT, Map Editor; `PG_DisaTalkDatStructs.h` and
`PG_DisaScriptsModel.cpp` in the [pg_disatools source](https://github.com/ProgSys/pg_disatools). Read 2026-11-04.
