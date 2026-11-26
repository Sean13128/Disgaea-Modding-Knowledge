# Disgaea 4 — What Can Be Modified

**Short version: modding Disgaea 4 itself is barely explored.** No public tool edits D4 Complete+'s files, and no
mods for it were found beyond trainers. (The PS3 and Vita releases have a public `.DAT` unpack/repack tool used by fan
translations; see [tools.md](tools.md). Whether it fits the PC files is **Unknown**.) What's known comes from *reading* D4 to bring its content into Disgaea 5.
This page says what that tells us, and what's still unknown.

## What's known

| Area | Read | Written back to D4 | Notes |
|---|---|---|---|
| Data tables (`char.dat`, `mskill.dat`, `magic.dat`, `charhelp.dat`, `charPersonal.dat`) | Yes (**Verified offline (Kit)**) | **Unknown** | NSAC v2 layout is simple: count, index, names. No writer exists yet |
| Sprites (`ANM_HI.dat`) | Yes, converted to D5's format | **Unknown** | Same layout as D5's anm except 16-byte node rows |
| Effects (`EFFECT.dat`) | Outer OBF container yes; textures partly | **Unknown** | Textures use a legacy `nismultitexform` wrapper; the Kit decodes 4-bit, 8-bit and 32-bit variants |
| Voices (`voice.dat`) | Yes | **Unknown** | `.nlsd` = 28-byte header + standard RIFF MS-ADPCM |
| Event scripts (`script.dat`) | Yes; byte-identical re-encode | **Unknown** | 42 commands have operand widths that differ from D5's or are D4-only (12 found in 2026-10, 30 more in 2026-11) |
| Executable | Static analysis only | — | See [functions.md](functions.md) |
| Saves | No | — | Format **Unknown** |
| Memory | Trainers and cheat tools exist | — | Addresses not public |

## Things a D4 modder should know

- **Evilities are code.** As in D5, a table row holds the name and number; the effect is in the executable. Aegis,
  for example, runs integer maths at a fixed step in the damage chain (see [functions.md](functions.md)).
- **Numbers live at fixed offsets.** An evility's value is at `mskill.dat` row `+0x0D`; the game reads parameters
  at `+0x0D + index`.
- **Skills start event 12,000,000 + skill ID**, like D5. A new skill needs that event.
- **Voice group and art sprite are different fields** in `char.dat` (`+0x1BA` and `+0x196`).
- **Which picture a new character gets is decided in code, by two different rules** (2026-11, **Verified offline**): the
  face number is the class face (colour ID rounded to tens) for class, rank and NPC rows, and the cut-in adds the unit's
  colour byte. A new row on an existing body therefore shows that body's class face for every rank unless its ID is below 1000.
  Details: [assets.md](assets.md#faces-and-cut-ins-which-picture-a-character-gets-2026-11-offline).
- **A plain attack starts event `12,000,000 + 100 × weapon type`** (fist 1 to staff 7) for humanoid bodies and a body-specific
  `12,010,000 + ...` event for monsters; a missing event falls back to 11,000,000. A new weapon type or body needs the matching
  event ([functions.md](functions.md#unit-animation-from-action-to-motion-id-2026-11-24-and-25)).
- **A weapon shows only if the motion has a node for its mount key.** The engine binds a weapon to one key (fist 10, sword 11,
  spear 12, bow 13, gun 14, axe 15, staff 16 in the main hand; 110 to 116 in the second hand) and the shared motions place it;
  a motion without that node draws no weapon (D4's motion 4047 has no gun node).
- **Defend, victory and dying have no character motion** in the code paths searched; do not expect to override them by motion ID.
- **Mesh parts:** a node whose `+4` word is 1 is drawn on four stored vertices, so an editor that treats it as a pivoted
  rectangle misplaces the part ([assets.md](assets.md#how-the-game-draws-a-body-2026-11-offline)).

## Open questions

- Does D4 check archive or save integrity?
- Does it read loose files or only archives?
- Is the Xbox Game Pass build the same as Steam's?
- What does `Data/DATA/anm0.fad` hold?
- What `AnimeInfo.dat` (49 large sheets in the database archive) is for.

## Sources

- D5 Mod Kit development fork (Sean13128): `d5lib/d4source.py`, `d5lib/d4effects.py`, `d5lib/d4scripts.py`, `docs/D4_*.md`. **Community-reported.**
- Trainer listing: [CheatHappens](https://www.cheathappens.com/72508-PC-Disgaea-4-Complete-trainer).
