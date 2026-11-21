# Building character packs from your own game copies

How to make a converter that builds Disgaea 5 character packs (from D4, D1 Complete, D2 and Disgaea RPG) on **any** user's
machine from files that user owns, without shipping a byte of game data. The design is the one the D5 Mod Kit
development fork settled on in November 2026 for its importers; the tool code is not stored here (wiki rule 2), so this page
describes the method. For what a port needs once the files are readable, see
[Bringing a character from one Disgaea into another](porting-characters.md).

**Status:** method used by this project. Everything here is **Verified offline** (traced builds and tests, no new game
facts). **Native Windows and Proton are untested**; the tool is plain Python standard library.

## Why this needs designing

The first importers worked only on their author's machine. Tracing every file one build touched (an audit hook on
`open`, directory listings and subprocesses, run over one class and one unique per game) found:

- **Owner paths were hard-coded** in every importer, and one importer failed outright when the kit sat outside the project
  tree because it located its data by walking up from its own file.
- **A kit-only checkout lacked code**, not just data: importers ran helper files that lived outside the kit's repository.
- **Art came from derived folders**, not from the game: pictures extracted months earlier by other tools, an "art
  library", and prepared packets. Nothing in the importer could regenerate them. These are also the files a stale copy
  silently corrupts.
- **The "base game" was the modded install.** Builds read the live archives, which are twice the size of the clean depot
  copy and change with every install. Their hashes cannot be verified.
- **Helper programs** (`ffmpeg` for voices) were found on the `PATH`, not declared.

A fresh-machine build needs a different shape: one place the inputs live, a record of exactly what is in it, and
importers that read nothing else.

## The prepared source folder

One fixed folder next to the Disgaea 5 install (or wherever a setting or environment variable names), with each game's
original relative layout, copied or linked from the user's own copy:

```
<Disgaea 5 folder>/<kit>/Source/
    manifest.json
    d4/    the needed files of the D4 install (archives, database files, voice banks)
    d1c/   data/SUBDATA.DAT
    dd2/   PS3_GAME/USRDIR/Data/... (the update-level data archives, voice banks)
    drpg/  unpacked sprites, looks and atlas indexes, voice bundles, decoded master tables
```

- **Keep the original layout** so a file name in code is the same as on the user's disk.
- **A prep tool copies the needed files** (it can link instead of copy on one disk), hashes each one against a table of
  known-good builds, and writes the manifest. The table holds **hashes only, never contents**.
- **The prep tool never decrypts and never holds keys.** Decrypting a Switch `.xci` to its filesystem, unpacking a PS4
  `.pkg`, dumping a PS3 disc or unpacking a Unity client is the user's job; the tool checks the result by size and
  SHA-256. For Disgaea RPG the tool does not read Unity bundles at all: the user supplies already-unpacked files, and
  a different decoder's PNG bytes are reported as unverified, not accepted silently.
- **One resolver finds the folder**, in this order: an environment variable (tests, a hand-picked folder), a setting, the
  canonical default beside the game, and a folder inside the kit only when no game folder is found. An explicit
  destination for the prep tool is prep-only: the user must point the resolver at it too.
- **The base game is read through the kit's own pristine backups**, never the live modded files.

### Which dump a game needs

| Game | What the user supplies | Notes |
|---|---|---|
| Disgaea 4 Complete+ | Nothing: found through the Steam library | Plain archives; no decryption |
| Disgaea 1 Complete | The **Switch** filesystem, `SUBDATA.DAT` included | The HD faces are read from that file ([FAD](../formats/fad.md#disgaea-1-complete-anmdatfad-the-hd-faces)). A PS4 unpack is accepted but reported **incomplete** (its pixel layout is not decoded). When both are present the tool hashes them and picks the recognised Switch build |
| Disgaea 1 PC | Nothing: found through Steam | Optional; voices only |
| Disgaea D2 | A decrypted PS3 disc dump | The update-level archive is the one every read uses |
| Disgaea RPG (JP) | The unpacked client files and the decoded master tables | Required: the masters. Optional: a catalog file used by one reviewed mapping |

### The manifest

Per game: edition, origin, and for each file its path, size, SHA-256, known build, and one of four states:

| State | Meaning |
|---|---|
| `yes` | Size and SHA-256 equal an accepted build in the table |
| `no` | The path is known, the bytes differ. Listed; importers that pin a hash refuse it |
| `unknown` | The table has no row for that path |
| `missing` | Expected (from the input or the table's inventory) but not supplied |

A game entry can carry extra facts derived from the file on disk (for D1 Complete, which HD-face source it holds), never
from the input offered. Expected-but-absent rows are written as `missing` rather than dropped, so a consumer that checks
only the manifest sees the gap. A partial unpack cannot pass because the indexes and masters verified.

## Rules that came out of review

Five review rounds on the prep tool and one on each importer produced these; each came from a concrete way a build could
go wrong.

1. **Refuse a manifest that isn't yours** (another tool's JSON, a wrong schema, a truncated file) before copying anything,
   and never replace it, including in a dry run.
2. **A verified file is never replaced by a mismatching input.** Keep the good copy and warn. The manifest always describes
   the files actually left in the folder, including after a failed update.
3. **Write safely.** Stage a replacement inside the destination and publish it only after its bytes match the input; stage
   a new link at a unique path and rename it over the target, so a failed link can't delete the output first; resolve
   every destination parent so a nested symlink can't redirect a write into an input; refuse to write inside the original
   game folders or inside any of the tool's own inputs.
4. **Importers read only what the manifest verifies.** A file that merely sits in the folder is never read. Ask for a file
   by its exact relative path; a missing or failed row is an error naming the game, the path and the prep command, not a
   silent fallback.
5. **Re-check at use.** Hash the file when it is opened, keyed on its size and modification time, so a **same-size
   replacement after prep is refused**. Child build processes get the same verified set and read through it.
6. **Never delete a file you didn't create.** A cache folder carries a marker and a receipt of the files written; a foreign
   or symlinked folder is refused untouched, and only recorded files are ever removed.
7. **Don't read installed packs by default.** An importer that compares its output with what is installed should do so only
   when asked; the default build uses checked-in ids and counts (bank shapes, held animation ids) instead.
8. **Don't store game text in the importer's data.** See [Keeping game text out of a mod repository](keeping-game-text-out-of-a-mod-repo.md).

## Import gates

Every built pack is checked before it is called installable; a pack that fails a gate is built and marked not installable.
The fact behind each gate is on the game pages; the ones with design lessons here:

- **Used ids** are checked against three sources: the original game's tables (a clean backup), every importer's lease file,
  and the mods already installed. **Installed keeps its ids; the uninstalled side moves**, because renumbering an
  installed pack breaks saves. Ids handed out "next free" on each run are not leases: 110 of 146 Disgaea RPG packs once
  collided with D1 Complete and DD2 packs installed in between until their quest ids were pinned per character.
- **A rebuild should equal the install.** Older packs were patched by hand outside the importer (hand-tuned faces,
  re-leased icons, held poses). Where the importer can't reproduce a patch, take the installed result, and record every
  option a build used in the pack's `mod.json` so a later rebuild doesn't guess. List-icon rows are pinned for the same
  reason: allocation order, not art, decided their ids.
- **The wheel gate compares bank shapes for equality** (image-header counts), because a class wheel's resident memory
  grows one-for-one with them. A deliberate change is **declared per pack member** with its approved counts and page sizes;
  the gate then compares against the declaration, still for equality. An undeclared member keeps the installed reference,
  and a declared bank that drifts fails. Adding a declaration is the project owner's decision, not the builder's: it costs
  one more image header in the wheel's resident set.
- **Render roots** (node `root + 1`) are repaired at the end of an atlas rebuild; a fresh rebuild of one monster-bodied
  character had 129 animations the game draws differently until it was.
- **Faces:** for D1 Complete the default is now the game's own HD faces, and a rebuild of the 59 in-scope packs changed
  only faces and the importer's accumulated bank differences.

## Sharing built packs

The kit can export every mod of a chosen game as one zip per mod plus a manifest (id, name, uid, version, requires, size,
SHA-256), and import such a folder.

- Mods built by an importer usually have no stored uid. The export derives one (a UUID version 5 of the folder id), so two
  machines exporting the same generated mod give it the same identity, and importing it is an update.
- `requires` names are only folder names on one PC, so each package also carries the uid of every requirement; when an import
  must rename a required mod's folder, the requiring mods are re-pointed.
- A mod that others need is exported too and imported first. One failing package is listed; the rest still import.
- Importing moves clashing sprite numbers inside compressed `anm` files and keeps them compressed, and needs the vanilla
  files read-only to know which ids clash.
- A shared pack contains the pack's own files only: images and tables the importer wrote, not the source archives.

## Limits

- Fresh-machine builds were proven by tests and traced rebuilds, not by a second person's machine. **Unknown** whether
  every user's dump matches a known build.
- Some art code (an extractor for D4 and D2 bodies) was outside the kit repository, pending a provenance review; the
  importer asks for its folder through a setting.
- A learned humanoid pose reference used for Disgaea RPG is not in the repository and is not rebuilt by anything; the
  user must supply the upstream release file, and it is used only when its SHA-256 equals the pin.
- Four Disgaea RPG cut-ins and 34 look or monster-twin sheets are not in the standard unpack; packs that need them refuse
  by name until the user supplies them.

## Sources

The D5 Mod Kit development fork (a fork of eraser4521's [D5 Mod Kit](https://github.com/eraser4521/D5ModKit), MIT; the source-prep tool, import gates and uid-derivation changes described here are fork additions, not upstream work): `docs/SOURCE_PREP.md`,
`docs/SOURCE_FOLDER_DESIGN.md`, `docs/SHARING.md`, `docs/IMPORT_GATES.md`. This project's traced builds and review rounds,
2026-11-17 to 2026-11-20.
