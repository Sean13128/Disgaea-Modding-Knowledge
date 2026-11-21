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

## Open questions

- Does D4 check archive or save integrity?
- Does it read loose files or only archives?
- Is the Xbox Game Pass build the same as Steam's?
- What does `Data/DATA/anm0.fad` hold?

## Sources

- D5 Mod Kit development fork (Sean13128): `d5lib/d4source.py`, `d5lib/d4effects.py`, `d5lib/d4scripts.py`, `docs/D4_*.md`. **Community-reported.**
- Trainer listing: [CheatHappens](https://www.cheathappens.com/72508-PC-Disgaea-4-Complete-trainer).
