# Audit: pages that cite "this project's research"

For item 3 of the [public-release checklist](STATUS.md#public-release-checklist). Every place a page leans on this
project's own (unpublished) work, grouped by what backs it, with what each option would take. **No page has been
changed by this audit**: picking an option is the owner's decision. Counted 2026-11-04: 52 citations on 38 pages (including the D7 table-encoding line added the same day).

## The three options (from the checklist)

1. **Publish the evidence** in a public form (method notes, tool code, or non-game-derived outputs).
2. **Re-verify from public sources** and cite those instead.
3. **Label it Community-reported**, naming this project as the community source.

## A. File listings from scans of the owner's game copies

Pages: `INDEX.md` for D1, D2, D3, D4, D5, D6, D7, Disgaea D2; D6 `assets.md`.

What backs them: folder trees, file sizes and archive member names read from installed games (2026-11-03).
Anyone with the same build can repeat them, and the trees themselves are already published as
`layout-*.txt` files.

- **Option 1 fits best,** and its first half is done (2026-11-04): `CONTRIBUTING.md` now has a
  [file-listing method](CONTRIBUTING.md#file-listings), and every listing citation links to it. The listings contain no
  game data beyond names and sizes. What's left is wording: keep "this project's scan" or say "a scan of build N".
- Option 2 is only partly possible (SteamDB depots list some file names, not archive members).

## B. Readers in the owner's own repositories

| Source | Pages | Public today? |
|---|---|---|
| D5 Mod Kit development fork (`d5lib/d4source.py`, `d1skill.py`, `d1cart.py`, `dd2source.py`, `docs/D4_*.md`) | D4 data/assets, D1 data/assets/INDEX, DD2 data, porting guide | **No** (not in the public Kit) |
| Character Forge (D1 PC, D2 PC, D3 Vita, D6/D7 model and texture readers) | D2 data, D3 data/INDEX, D2 INDEX, `formats/nmb-models.md`, `formats/portraits-and-faces.md`, D7 data | **No** |
| Disgaea D2 native port | DD2 assets | **Yes** ([repo](https://github.com/Sean13128/disgaea-d2-recomp)) |
| DRPG extraction reports | DRPG data/INDEX | No |

- **Done (2026-11-04):** every page whose tables rest on these readers now says so in its Sources ("reader code not
  published; its results are the tables on this page"), so a reader can tell evidence from method at a glance.
- **Option 1 = publish the readers.** They're MIT-licensed project code and read files rather than contain them;
  publishing the fork and Character Forge (or just their format notes) would turn these into normal citations.
- Some are now also covered publicly: the Disgaea PC character table matches pg_disatools (D1 `data.md` line 46
  already says so), and D7's table encoding matches Makai Kit's `databases-serde`.

## C. Reverse-engineering research

| Research | Pages |
|---|---|
| D4 → D5 porting and expansion research (Ghidra, plus original code run in an emulator) | D4 data/functions/assets/INDEX, D5 functions, D5 source-packs |
| D2 Mac build decomp index (symbol names only) | D2 data, D2 functions |
| D1 decomp index (function counts only) | D1 functions |
| D7 function matching | D7 functions |
| Prior-art assessment, class registry, ownership checker | D5 modifiable, D5 id-registry, D5 assets |

- The pages already publish only facts (addresses, behaviour, counts), never decompiled code, as CONTRIBUTING requires.
- **Done (2026-11-04):** the method behind the **Verified offline** reverse-engineering facts is now published in
  [decompile-setup.md](guides/decompile-setup.md#checking-a-finding-by-running-the-original-code), and the D4 and D5
  function pages link to it from their evidence notes.
- **Correction:** the D4 expansion folder's `.c` files are the D5 Mod Kit fork's own runtime source, not decompiled
  game code. Their addresses were published on D5 `functions.md` (group B source, your own code).
- **Option 1** would mean publishing method notes and the emulator test cases' *descriptions* (not their game-derived
  inputs). **Option 3** is the light-touch alternative: these facts are already labelled **From decomp** or
  **Verified offline**, which describes them accurately.
- Option 2 isn't possible for most: nobody else has published this work.

## D. Statements of project policy, not evidence

- D2 `assets.md`: "This project's rule is whole-number pixel scaling … never AI upscaling."
- `engines/README.md` line 59: the faithful-stats extraction (every listed row read), which is type B.

**Done (2026-11-04):** the scaling rule is now worded as a recommendation with its reason, on the D2 and D5 asset pages.

## E. Temporary or private material named on purpose

- D7 `functions.md` says the function index is private. Under any option it should point to a published version or be dropped before the repo goes public.

## Suggested order, if the owner wants one

1. Category A: method notes are in; only the wording remains.
2. Category B: decide whether to publish the D5 Mod Kit fork and Character Forge; that resolves most of B at once.
3. Category C: the method page is in; keep the existing evidence labels unless you prefer option 3's wording.
4. Category D is done; category E is small rewording.
