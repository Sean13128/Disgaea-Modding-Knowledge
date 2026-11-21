# Disgaea 5 Complete — Scripts and Skill Animations

How the game runs events and skill animations from `Data/Script/script.dat`. The script VM itself (container,
opcodes, operands) is on [functions.md](functions.md#script-vm-datascriptscriptdat).

**Evidence:** **Community-reported** from D5 Mod Kit's [SKILL_ANIMATIONS.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/SKILL_ANIMATIONS.md)
and [SKILL_EDITOR.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/SKILL_EDITOR.md). The Kit traced commands
in the executable and checked its skill simulator against frame-by-frame recordings of the game (e.g. one skill's
camera matched at all 1,251 frames). Command names like `cmd_3c00` are the Kit's (`cmd_` + opcode). Addresses are the
Kit's reference build (October 2018).

## Event IDs

| Event | What it is |
|---|---|
| **12,000,000 + skill ID** | A skill's wrapper event (Astral Wave 11031 → 12011031). Usually one line: `call_event 15xxxxxx` |
| 12,100,000 + skill ID | The same skill with effects skipped (options, skip button, enemy turns) |
| 12,200,000 + skill ID | Restart when skipped midway |
| 12,100,000 + (ID // 10 × 10), then 11,100,000 | Fallbacks. 11,100,000 doesn't exist in vanilla, so **a skill with no event at all hangs the battle** |
| 11,000,000 | Default sequence for a skill without its own event |
| 12,010,000 / 12,110,000 / 12,210,000 + sprite ID | A monster's normal attack (counters too), in the same three forms (launcher `0x1405474B1`) |
| 3,100,000 + class ID | Class-keyed events, one per rank |
| 15xxxxxx | Choreographies: small trees of events |
| 12000010 / 12000011 | Shared helpers: skill start / end |
| 12000020 | Actor motion sequence |
| 12000045 | Damage hits at listed frames (chains fading camera shakes) |
| 12000055 / 12000056 | Camera to the caster and back |
| 12000012 | Opening camera move (11 frames when a game setting is 1, else 7) |

Event IDs are stored in 3 bytes, so they must be below 16,777,216. Appended scripts are found (the last matching ID wins).

## Calling helpers

`call_event` arguments reach the callee **divided by 10**. Commands read them back as integers (slots, actors, effect
IDs: truncated) or × 10 (positions, sizes). So `call_event 15130101, 10, 1060, …` means slot 1 and effect 106.

## Command families

| Opcodes | Area |
|---|---|
| `01xx` | Flow: `0108` waits for child events, then ends (D5 `0x14018CD20`; D4 `0x1401502F0`, same logic, other offsets) |
| `14xx` | Flags: `1424 index, value` sets (value ≠ 0) or clears event flag **3900 + index**; indices 0–99 only, others ignored. Same in D4 (emulated original code, build **B**) |
| `28xx` | Screen: fades (`127` black … `0` … `129` white), backdrop gradient (`2830`–`2835`), map colour/opacity (`2820`) |
| `32xx` | Camera: aim `3210`/`3220`, zoom `3230`/`3240`, lens `3250`/`3258` (vertical field of view = 27,008 / value degrees; battle view 5120), turn `3260`/`3268`/`3280`, back to battle view `3290`, shake `32a0` |
| `3cxx` | Effects: load `3c00 slot, effect`, show at an actor `3c10`/`3c18`, size `3c40`/`3c42`/`3c48`, colour/opacity `3c80`, hide/show `3c9a`, draw order `3cde`, blending `3cd6`, nearer to camera `3ca0`, stop facing camera `3ca5` |
| `40xx`–`41xx` | Effect paths (`4140` steps ending in a `4170` flight; `41a0`) |
| `44xx` | Particles |
| `46xx` | Sound: BGM, voices, SE |
| `64xx`–`6cxx` | Actors: look `6402`, on `640a`, cut-in portrait `6411`, shadow `6470`, tint/opacity `6480`/`6482`, motion jump `6530`, stop/play `6560`, speed `6570`, size `65a0`, sprite turn `65b4`/`65b8`, shift `65bc`, base place `6620`, moves `6642`/`6672`/`6680`, cut-in place `6660`/`6664`, turn `6710`, attach `6c00`/`6c04`, detach `6c08`, 3D turn `6c10`/`6c18` |
| `72xx` | Battle: hits `7220`; mirroring `72f0` |

## Rules worth knowing

- **Frame order:** scripts run, then everything advances one frame, then the frame is drawn. 60 frames per second.
- **Facing:** the game turns every script offset by the skill's facing, `(4 − facing) % 4` quarter turns, so a skill is
  written once, facing forward (+z). Facing 0 = target away to the left of the battle view, 1 away right, 2 toward
  you on the right, 3 toward you on the left. Skills mirror themselves for facings 1 and 2 (`72f0 1`).
- **Places:** an actor ID means the actor's base place; `1xxxx` = where actor xxxx is right now; 300–399 = locators;
  5000 + i = area panel i; 7000 + i = effect slot i.
- **Cast:** 6101 is the first cast member and 6111 the second. Plain 101 / 202 are just dynamic actor slots here, unlike
  D4, where they mean "first attacker" and "every target" (see [D4 functions](../disgaea-4-a-promise-unforgotten/functions.md)).
- **Targets:** area targets are actors 3000 + i (30000 + 10i as a call argument); `$60400 + i` is 1 when target i
  exists; 3600 = the main target. `7220 3603, 3603, %` hits every target.
- **Waits:** `wait N`, `wait_children`, calls, and 32 commands whose handlers rewind and return 0 (e.g. `0a10`–`0a40`,
  message commands, `6540`/`6550`) pause a script. `640a` waits while an actor's sprites load.
- **Loop counter:** variable **`20100`** returns the innermost active loop's counter (as a float; 0.0 outside a loop).
  Loops nest at most 8 deep. Disgaea 4 calls the same variable `10100` (**Verified offline**: both games' loop and
  variable code run in an emulator gave identical results).
- **Eased values** step geometrically: each frame's step is p × the last, p = 1 + ease / 1000.
- **Camera units:** a map unit is 10 script units; the battle view sits 700 map units away, pitch 30°, yaw −45°.

## Effects

Effect models are `Start/Start_JP.dat/Effect.dat/effectNNNN.obf` (383 vanilla, YKCMP-compressed), listed in
`EffectTableDataBase.dat`. Each has 1–13 textures and its own animation. Texture formats: `0x00` ARGB8888 (mostly
additive glows), `0x0c` LA88 (1–3 stacked layers, coloured by `3c80`), `0x0b` ARGB1555, `0x0d` RGB565, DXT1/DXT3.
Blend modes (mesh flags or `3cd6`): 1 alpha, 2 add, 3 screen − colour, 4 multiply, 5 colour − screen.

**Limits:** effect IDs are a signed 16-bit operand (≤ 32,767; new ones from 3000 load fine); the startup preload has
room for 400 models ([modifiable.md](modifiable.md#hard-limits-and-crash-causes)). New or changed effect pictures need
a game restart.

## Open questions
- About 50,000 skill-specific command uses (of 66,000) still aren't simulated by the Kit: 3D actor turns, some particle
  commands, `41a0`, `66c0`, `2890`.
- Why some greyscale (LA88) effects are drawn orange without a colour command.

## Sources
D5 Mod Kit by eraser4521: [SKILL_ANIMATIONS.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/SKILL_ANIMATIONS.md), [SKILL_EDITOR.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/SKILL_EDITOR.md), [EXE_ANALYSIS.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/EXE_ANALYSIS.md), [CLASSES.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/CLASSES.md).
