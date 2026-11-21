# Disgaea 4: A Promise Unforgotten

- **Released:** 2011 (PS3) · **Developer/publisher:** Nippon Ichi Software / NIS America
- **Versions covered here:** Disgaea 4 Complete+ for PC (Steam app 1233880, build 6150765). File names for A Promise Revisited (PS Vita). Switch and PS4 not covered
- **Modding status:** almost none. Files are readable, but no tool writes them and no mods are public
- **Research on hand:** Deep for reading D4's data (as a source for Disgaea 5 imports); thin for changing D4 itself

## Start here

- **I want to install mods:** none are public yet. Trainers exist ([tools.md](tools.md)).
- **I want to make a mod:** start with [modifiable.md](modifiable.md): it lists what's known and what isn't.
- **I want to understand the game:** [INDEX.md](INDEX.md) → [data.md](data.md) → [functions.md](functions.md).

## Pages

| Page | What's there |
|---|---|
| [INDEX.md](INDEX.md) | Files, archives (NSAC v2), leftovers |
| [data.md](data.md) | `char.dat`, evilities, skills, help text, voice tables |
| [functions.md](functions.md) | Evility and damage code (Aegis), skill event path, script commands |
| [evilities-vs-d5.md](evilities-vs-d5.md) | 195 D4 evilities mapped to their Disgaea 5 twin or rebuild |
| [modifiable.md](modifiable.md) | What's known and unknown about changing D4 |
| [assets.md](assets.md) | Asset formats; moving D4 art and voices into Disgaea 5 |
| [tools.md](tools.md) | Readers and console tools |
| [mods/](mods/) | None found yet |

## Shares code or formats with

**Format pages:** [NSAC](../../../formats/nsac.md) · [FAFULLFS](../../../formats/fafullfs.md) · [NIS LZS](../../../formats/nis-lzs.md) · [anm](../../../formats/anm.md) · [textures](../../../formats/textures.md) · [OBF](../../../formats/obf.md) · [engine families](../../../engines/README.md)

- **Disgaea 5:** close relative. Same anm sprite format (except node row size), same skill event scheme
  (`12,000,000 + skill ID`), near-identical script bytecode, same kind/gender codes. About 2,300 functions match closely.
- **Disgaea 3:** D4 seems to be built on D3's code: its opening movie file is still named `dis3_0.dat` (TCRF).
- **Disgaea D2:** ships a `dis3_0.dat` too. Its database uses D5-style NISPACK archives, not D4's NSAC.

## Open questions

- Save format; archive and save integrity checks; loose-file support.
- Most data tables beyond characters, evilities and skills.

## Sources
See each page.
