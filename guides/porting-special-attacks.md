# Bringing special attacks into Disgaea 5 with their own scenes

How a special attack from Disgaea 4, Disgaea D2 or Disgaea 1 Complete can play its **own** scene in Disgaea 5
(poses, movement, camera, effects, sound and hit timing), rather than a borrowed D5 animation with the source
game's numbers. This page covers how each source game stores its scenes, the conversion rules that turned out to
matter, and the traps that make a converted scene look right in a preview but wrong in the game.

All of this converts from the user's own game copies at build time. Nothing here ships game data. For how a
converter finds those copies, see [Building character packs from your own game copies](building-packs-from-your-own-game-copies.md).

Status (2026-11-27): one attack per source game was converted with shared rules and played in a Disgaea 5 battle:
Dragon Claw (D4), Gigantic (D2) and Thursday's Robo Attack (D1 Complete). In each, every motion cue landed on the
expected frame, damage applied once per target, skip worked at the start and midway, and control returned
(**Verified in game**, Disgaea 5 Complete Steam build). A
D1 Complete → D5 scene converter for all attacks, and bulk D4/D2 conversion with the rules below, are built offline
(**Verified offline**).

Cryswar reports that he already ports D1 Complete, D4 and D2 specials with their animations
(**Community-reported**: Disgaea modding Discord). The work on this page was done independently; comparing the two
would be a good check.

## How each game stores a scene

| Game | Where | Form | Route |
|---|---|---|---|
| Disgaea 4 | `script.dat` | The same command language as D5: groups of typed commands, the same actor numbers | Translate command by command (**Verified in game**) |
| Disgaea D2 (PS3) | scene scripts | D4's command language in big-endian byte order; 490 of 500 typed command records have the same operand widths as D4 | Byte-swap, then the D4 route (**Verified in game**) |
| Disgaea 1 Complete (Switch, PS4) | `script.dat`, effects in `start.dat` member 33 | Its own bytecode: `op u8, length u8, args` (op 7 uses a u16 length); event id = 2,000,000 + skill id | New decoder, then lower to D5 commands (**Verified in game**) |
| Disgaea RPG (JP) | Unity prefab `prefabs/effect/command/{back,front}/command<id>` | One Animator clip at 60 Hz, with named events (sound, voice, per-target hits, shake, move) and Unity particle systems | Cues convert; the particle visuals have no D5 equivalent (**Verified offline**) |

### Disgaea 1 Complete details (Switch v0 `main`)

- **Dispatch.** The battle code requests event `2,000,000 + skill + 10,000 × relative quadrant`, falls back to the
  base id, then to `2,100,000` (**From decomp**, confirmed by an emulated run of the interpreter).
- **Interpreter.** A shared VM with a 0xD2-entry opcode table at `0x71001edcc4`, interpreter `0x7100098370`
  (**From decomp**). Useful opcodes: `01` wait, `03/04` loop, `07` conditional block (u16 skip length), `12/61`
  start another event, `28/29/2c/0c` camera follow, distance, position and shake, `65` move relative to an actor,
  `66/67/6a` motions, `68` wait for animation ready, `24/6f` effect set-up and spawn, `78` damage display (`0x65`) and
  apply (`0x1e`), `87/88` voice, `8c` sound effect.
- **Actors.** `0x65` caster, `0x97` first target, `0xca` all targets, `0xa0..0xbf` mapped target slots.
- **Widths.** The handlers, not the length byte, decide some command widths (op `d0` consumes three payload bytes).
  A scanner that trusts the length byte loses commands.
- **Timing.** One D1 Complete tick maps to one D5 frame (both 60 per second) (**Verified in game**).
- **Effects.** Keyframed textured quads (position, rotation in 0.1°, scale %, frame, alpha keys), textures BC7 in the
  Switch's swizzled layout or 16-colour palettes stored B,G,R,A. They convert to D5 effect models (**Verified in game**).

### Disgaea RPG details (JP Android, il2cpp)

- A skill whose `resource_id` is 0 falls back to its own command id (`BattleCommandData.SetCommand`)
  (**From decomp**). With that rule every roster skill resolves to a prefab.
- The clip's events (`SEPlay`, `VoicePlay`, `DispDamageA..E`, `TargetDamageMotionPlay`, `ShakeCustom`,
  `StartMoveToTarget`) map onto D5 scene cues; the particle systems (often 20-150 per skill) do not
  (**Verified offline**).

## Rules that mattered (D4 and D2 into D5)

- **Don't whitelist commands.** A converter that kept only commands it knew lost whole visual groups that D5 plays
  natively: attached effects (`3c20/3c22`), effect moves, rotation, colour, speed and fade steps, blend mode,
  backdrop gradients and their end (`2840 n` → D5 `2835 n`), sprite turns and offsets, following (`6c00/6c04/6c08`),
  draw depth (`6492`), gravity (`6610`), sound effects and particles. Count and list every command you don't carry.
- **Effect slots as actors.** D4 and D2 address effect slot *i* as actor **700 + i**; D5 uses **7000 + i**
  (and 10700 + i → 17000 + i). Passing 700 + i through puts the effect at the scene origin
  (**Verified in game**).
- **"All targets" selector.** For follow and sprite-turn commands, D4 and D2's selector 202 means every target; map it
  to D5's 3603, not to the main target. Otherwise only the main target stops following and the others end up
  off their panels (**Verified offline**).
- **Sprite pixel scale.** D4 scales sprite offsets by 3/s but also draws with the same s, so the two cancel; carry the
  values unchanged (**From decomp**).
- **Sound effects.** D4 and D2 sound ids can be mapped to D5's bank by comparing the audio. Ids that D5 loops forever
  must be dropped (**Verified offline**; loudness not yet checked).
- **Particles.** None of the three games has a particle table: emitters are built inline in the scene. The atlas has
  the same 12 cells in the same order, and colour operands widen from u8 to u16 (**Verified offline**).

## Skip and damage

- Skipping midway restarts the scene as event + 200,000, but only while game flag 2211 is set
  (`script_frame_driver` 0x14018F880). D5's stock set-up event sets it two frames in, and **every** damage-finalize
  command clears it (**From decomp**, behaviour **Verified in game**).
- So a scene can be skipped midway only until its first damage finalize. 577 of 610 stock skill scenes work this
  way. A "skip doesn't work" report is usually a press after that point.

## Traps: looks right in a preview, wrong in the game

- **Effect vertex colour order.** In D5 effect models the four colour floats at vertex +0x24 are **A, R, G, B**, and the
  shader doubles them. Writing R, G, B, A turned a blue effect lime green in game (**Verified in game**).
- **Effect node position units.** D5 builds each node's translation as (x·10, −y·10, z·10) and uses vertex positions
  as stored. Storing node positions in vertex units puts every part ten times too far out (**Verified in game**).
- **Helpers fall.** A scene-created helper actor that is lifted into the air is pulled back down by D5's gravity.
  Effects attached to it come down with it: D2's Gigantic storm cloud ended at unit height (**Verified in game**).
  D5 gives every new object a gravity of 0.163 per frame²; command `6610 actor, v` sets it (v/100, 0 = float). D4 and
  D2 have the same command with the same meaning, and their scripts already float such helpers with `6610 a, 0`.
  Carrying `6610` keeps the cloud in the sky (**Verified in game**); it changed positions in 73 of 77 D4 and 26 of 32
  D2 attacks (**Verified offline**).
- **Draw depth.** `6492` sets a per-unit sort bias; without it, a large copy of the caster draws in front of everything
  (**From decomp**).

## Tools

[D5 Mod Kit](../tools/README.md) (eraser4521) provides the D5 side: the script reader, the scene simulator
(`choreo`), the Skills timeline editor and the effect model reader and writer. Its preview did not model several of
the traps above when this was written.

## Credits

Cryswar (Overlord Edition), Sorrow (Disgaea 5 engine research: effect model format and render notes), eraser4521
(D5 Mod Kit).
