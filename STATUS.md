# Status

Progress toward [NORTH-STAR.md](NORTH-STAR.md). Update this when you start or finish a page.

**Phase:** 0 (foundations) done 2026-11-04 · Phase 1 (mining the seed): **Every game with research on hand now has a first page set** (2026-11-04). Phase 2 shared pages done the same day. Next: Phase 3 gaps (see below).

## Phase 0 – Foundations
| Item | Status |
|---|---|
| North Star | Done |
| [LICENSE.md](LICENSE.md) (CC0 content, MIT code) | Done |
| [CONTRIBUTING.md](CONTRIBUTING.md) | Done |
| [templates/](templates/) | Done |
| Front page [README.md](README.md) | Done |

## Phase 1 – Per-game pages
Legend: ✅ done · 🟡 in progress · ⬜ not started · — template only

| Game | README | INDEX | data | functions | modifiable | assets | tools | mods | Next step |
|---|---|---|---|---|---|---|---|---|---|
| Disgaea 5 | ✅ | ✅ | ✅ | 🟡 | ✅ | ✅ | ✅ | 🟡 | data-reference.md: every named field of all 90 tables · functions: battle, AI and menu systems need Sorrow's permission to publish his labels · mods: 4 write-ups |
| Disgaea 4 | ✅ | ✅ | 🟡 | 🟡 | ✅ | ✅ | ✅ | ✅ | data: 5 tables mapped · functions: evilities, damage and skill scenes only · no public D4 mods exist |
| Disgaea 7 | ✅ | ✅ | ✅ | 🟡 | ✅ | 🟡 | ✅ | 🟡 | functions: loader hooks only · assets: models, animations, textures mapped (formats/nmb-models.md); `.nmbs` open · mods: Overlord write-up awaits permission |
| Disgaea 1 / 1 Complete | ✅ | ✅ | ✅ | 🟡 | ✅ | ✅ | ✅ | 🟡 | data: PC tables from pg_disatools (2026-11-04) · functions: PC addresses, formulas, unit record; D1C nothing · scripts.md added · mods: 1 write-up |
| Disgaea D2 | ✅ | ✅ | 🟡 | 🟡 | ✅ | ✅ | ✅ | 🟡 | data: 4 tables · functions: only what the native port names · voices unknown |
| Disgaea RPG | ✅ | ✅ | ✅ | — | — | ✅ | ✅ | — | Online game: client modding out of scope; data and art documented for reuse |
| Disgaea 6 | ✅ | ✅ | ⬜ | 🟡 | ✅ | ✅ | ✅ | ✅ | data: no public table definitions exist yet · functions: counts only |
| Disgaea 2 | ✅ | ✅ | 🟡 | ✅ | 🟡 | ✅ | ✅ | ✅ | 25 table types named; byte layouts unknown · test the `NisFs` bypass path |
| Disgaea 3 | ✅ | ✅ | 🟡 | ⬜ | ✅ | ✅ | ✅ | ✅ | `char.dat` layout; archives are PFS-encrypted on Vita; no decomp, tools or mods |
| Others | ✅ | — | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ | Overview READMEs only; no copies scanned |

All `INDEX.md` files have been rewritten so they no longer link into the private research seed. Some still cite this project's research as a source; those facts need public sources or re-verification before going public.

## Extra pages beyond the template

| Game | Pages |
|---|---|
| Disgaea 5 | `evility-consumers.md` (666 evility IDs → the functions that test them), `data-reference.md` (all 90 tables), `scripts.md` (events, commands, skill animations), `id-registry.md` (ID ranges in use) |
| Disgaea 1 | `scripts.md` (TALK and SCRIPT languages), `scripts-reference.md` (every command's arguments) |

Other shared pages: [GLOSSARY.md](GLOSSARY.md).

## Phase 2 – Shared topics
| Area | Status |
|---|---|
| `formats/` | ✅ 17 pages: archives (NISPACK, NSAC, PS_FS_V1, FAFULLFS, DSARC, Disgaea PC containers), compression (YKCMP, NIS LZS), anm (all variants), textures, OBF, NLSD, NMB/NMA models, D5 maps, CED maps, portraits and faces, saves |
| `engines/` | ✅ families, function matching, core tables compared |
| `tools/` | ✅ catalog |
| `mods/` | ✅ catalog (most write-ups planned) |
| `guides/` | ✅ 9 guides (setup, first D5 mod, first D7 mod, first Disgaea PC mod, D7 evilities, porting characters, decompile setup, automated testing of D5 mods (2026-11-11), contributing) |

## Public-release checklist

| # | Item | State |
|---|---|---|
| 1 | Cryswar's permission for pages that describe Overlord Edition (anything beyond its public release posts; material from unreleased files is kept private) | Needed |
| 2 | Sorrow's permission, if his D5 function labels are to be published | Needed (nothing of his is published now; the D5 evility-function table was relabelled with plain descriptions 2026-11-04 in case its earlier labels came from him) |
| 3 | 38 pages cite "this project's research". Either publish that evidence in a public form, re-verify the facts from public sources, or label them **Community-reported** with the project as the community source | Audited: [RESEARCH-CITATIONS.md](RESEARCH-CITATIONS.md) groups every citation and says what each option takes. Decision needed |
| 4 | Remove the research seed | Done: this repo was started fresh without it |
| 5 | Remove the seed and removed files from git history | Done: fresh repo with no prior history |
| 6 | Choose the publishing platform (plain repo, GitHub Pages, hosted wiki) | Decision needed |
| 7 | Final audit: no game files, decompiled code, keys, personal paths or private notes outside the seed | Passing (2026-11-04 scan) |
| 8 | Allow `tcrf.net` in the session's network settings to check TCRF-sourced facts directly | Optional |

## Phase 3 – Next gaps, in priority order
1. ~~Disgaea 2: write up the Mac build's named C++ classes~~ (done 2026-11-04). Still open: table byte layouts; test the bypass path.
2. ~~Disgaea 6: tables from Makai Kit~~ (checked 2026-11-04: the public crate defines only 2 tables and the public loader has no table patching). Still open: map D6's tables directly; check whether its evilities are data-driven like D7's.
3. ~~Disgaea 5: field layouts for the other 85 tables~~ (done: `data-reference.md`). Battle, AI and menu functions: the best names are Sorrow's private labels; publishing them needs his permission.
4. ~~Save formats~~ (locations for every game in `formats/saves.md`, 2026-11-04). Still open: formats for D4, D6, D7, D3, D1C.
5. Mod write-ups: 7 written (D5 Mod Kit outfits, CD5E, Unleashed, disgaea5autoplay, D1 HD sprites, Pretty Prinny, D2 costume recipes). Overlord ones need Cryswar's permission; Requiem mod and Netherworld Content Pack need more public detail.
6. ~~Guides~~: 8 written.
7. ~~Games with no coverage~~: overview pages written 2026-11-04 (releases, platforms, what little is known). Makaism is Disgaea Mayhem (same game). None has a scanned copy yet; Disgaea Mayhem, Phantom Brave PC / The Lost Hero and Makai Kingdom Reclaimed are on PC and worth scanning first. Disgaea Mayhem now has a table list from a public translation tool (2026-11-04).
12. ~~Third pass: playtest, live-verify, mastery, evility, Character Forge, D2 port and source-pack reports~~ (2026-11-04): Hire Character freeze causes (image-header byte order, added palettes), hit-reaction motions, D5 motion ID bands, Skill Shop eligibility, description limit verified in game, `SkipMovie` and the Metal texture limit under Wine, char.dat stat widths, mastery award ranks, D4 Evil Area and Friends, D4 → D5 evility map, Disgaea D2 → D5 skill/evility map, D1C monster skill map. The 141 addresses left unlisted were checked one by one (2026-11-04): 13 are now documented; 92 lie within 0x1000 bytes above a documented function (call sites, range ends and stack-frame notes inside it); the other 36 are start-up registration call sites whose callbacks and constructors are now on the D4/D5 script-callback table, C runtime helpers (memset, assert, heap), data globals, a float constant, a vtable, and addresses inside the D5 Mod Kit's own patch section, none a game function worth its own row; Overlord- and Sorrow-derived designs (packed mastery, recruiter route traces, `11-prior-art/`) stay out.
13. ~~**Address sweep**~~ (2026-11-06): every game address cited in the seed's notes (Overlord and Sorrow material excluded) was checked against the wiki. Of the 791 unique addresses still unlisted after the first pass, all were classified and the useful ones published: 44 lie in the D5 Mod Kit's own patch section (not game code); 72 are data globals (the meaningful ones are now listed); 319 sat within 0x400 bytes of a documented function, of which the 89 that are 16-byte aligned (possible function starts) were each read in context and the real functions added, while the other 231 were then also read one by one and confirmed as instruction sites inside documented functions (call sites, loop bodies, patch sites), with the few facts they carried added; the remaining 356 were each read in context and the game anchors among them added to D5 `functions.md` ("More anchors") and D4 `functions.md`. What stays out: Kit patch internals, emulator-proof window boundaries, C runtime helpers, and items that came from Sorrow's annotations or Overlord's constants.
14. ~~Disgaea D2 port reports (`08-disgaea-d2-re-port/codex/`)~~ (2026-11-06): every report was scanned line by line for game content (tables, maps, units, items, assets, saves, guest functions), and every guest function they cite was checked against the D2 pages; the game facts are now on D2 `data.md`, `functions.md`, `modifiable.md` and `INDEX.md`, and the RPG-import notes on appearance fields and `anm` roles were read in full. The rest of that folder is the port's own engineering (audio clock, GPU FIFO, movie decoding, PS3 runtime libraries, macOS menus, CPU profiling), which describes the emulator layer rather than the game and isn't wiki material.
15. ~~RVA-form address sweep~~ (2026-11-06): the first sweep only matched full addresses (`0x14…`), so a second pass looked for addresses written as RVAs (`0x217230`). 810 candidates: about 750 were Kit/Overlord patch-site byte ranges from the collision assessment, DLL offsets, struct sizes or D2 guest addresses; the other 61 were each read and the game facts added (JobRank in memory, class-index switch tables, mastery counter layout, unit class fields, the Status-screen crash for classes without a portrait bank, the generic-range check sites page, the colour-menu limit). The public D5 Mod Kit's reference data files (opcodes, identity sites, build address map) are now listed on D5 `tools.md`; FAD / FANM got a format page; seven `char.dat` / `magic.dat` / `JobRank.dat` fields disputed by an unpublished code review are flagged.
16. ~~Project findings of 2026-11-06 to 2026-11-11~~ (2026-11-11): mined from the D5 Mod Kit development fork's docs changed in that window and from about 45 project reports. Added: how D5 reads each `anm` field, the render-root rule, the Disgaea D2 `anm` layout and the D4 missing-page rule (`formats/anm.md`); on D5 `functions.md` a "Findings from November 2026" section (sprite binding and the resident `N + 2` placeholder, graphics ID and faces, army cap and Dismiss rule, Quest Shop board, learned skills by ID, skill power and damage formula, defeat counters, evility limits and mechanics, Magichange state, animation loading, window / message boxes / input) and things seen in play; 15 new limit rows on D5 `modifiable.md`; voice situations labelled by matching recordings (D5 `assets.md`); new ID bands (`id-registry.md`); D1 target-panel patterns, power formula, face table and voice lists; D4 `char.dat` fields, EXP / mana formulas and more evility sites; Disgaea D2 voice selector (replaces the earlier "partially mapped" note), `mskill.dat` values and executable notes; Disgaea RPG `leaderskill` values, skill tables and voice banks; Wine / macOS notes (registry emptying, background input, an unexplained rare freeze). **Left out on purpose:** the facility task IDs, Give Up route and animation-actor field chain, which were ported from Overlord with Sorrow's names and wait on checklist items 1 and 2; functions are given by address with plain descriptions, not Sorrow's labels. Nearly everything is **From decomp** or **Verified offline**; the live checks of new evility effects and of voices by ear are still open.
17. ~~Project findings of 2026-11-11 to 2026-11-20~~ (2026-11-20): mined from the D5 Mod Kit development fork's docs changed in that window (+1,495 lines) and about 150 project reports, as a fact ledger per area (new / updates / contradicts) checked against these pages, then audited. Added: D5 effect-model arena limit (1,024 records) and the loader leak behind it, the Set a Sub Class screen's own 47-family list, stale-placeholder release, new unit-record fields, a Character Bank page, outfit own values, exe-patch safety and save notices; Disgaea 4 and Disgaea D2 special-attack scenes as a source (D2 scripts are D4's scene language), D4 command-width corrections, faces, NPC rows and rebuilt evilities; D1 Complete HD faces in `SUBDATA.DAT` member `anmdat.fad`; D2 angel/flat rules, voices and evility matches; Disgaea RPG leader-skill codes, class-title sources and cut-ins; format notes for `anm`, `fad`, `ykcmp`, faces and D4 effect textures; two new guides (building packs from your own game copies; keeping game text out of a mod repo) and updates to the testing and Mac/Linux guides. About 20 earlier statements corrected and dated. **Left out on purpose:** the Class-tab sub-class route, recruit bills and recruiter page (Overlord designs, checklist item 1), importer plumbing, and results still running (the 200-cast arena soak, the D2 crash cause).
11. ~~Remaining seed folders~~ (2026-11-04): `04-d4-import-mod` (D4 vs D5 command equivalence), `06-tools` (exporter only), `09-game-source-notes` (clean-install hash lists for the five Steam PC games, the clean D5 depot), `11-prior-art` (D5 save file encoding and game-state layout, crit/throw clamps, six-star mastery), the rest of `03-engine-extensions` (evility mechanics, attack direction, wheel preview, Skill Shop list). Material that rests on Overlord's source or Sorrow's labels stays out until permissions are settled.
10. ~~Second pass over `seed: 03-engine-extensions/` reports~~ (2026-11-04): learned evilities are bits by `Skill.dat` row position (append-only rule), evility categories, `char.dat` sorted on load, hook ABI rules, how saves are written, volume masters, anm control codes, the Hire Character freeze with many class packs (cause found in the live-verify reports: image-header byte order; plus the added-palette rule), hit-reaction motions, D4 → D5 evility map. Reports that rest on Sorrow's or Overlord's material were used only for the project's own measurements.
9. ~~D4 expansion research (`seed: 05-research/d4-expansion-20261018/`)~~ (mined 2026-11-04: every report with engine or format facts went into the D4 and D5 pages. What's left is deployment logs and project tooling. The folder's `.c` files are **not** decompiled game code: they're copies of the D5 Mod Kit fork's own runtime (`d5native`) across many builds; their build-B addresses were added to D5 `functions.md` the same day. Function-level addresses from the reports were mapped onto the D4 and D5 function pages the same day (three passes; every address with an identifiable role is now on a page). The ones left are instruction sites inside those functions (call sites, patch points, test-case boundaries) and a few helpers with no stated role).
8. ~~Disgaea PC from pg_disatools' public wiki and source~~ (done 2026-11-04: tables, TALK/SCRIPT, addresses, unit record, save layout, container formats, mod framework, first-mod guide). Same day: every Data Editor table layout, the `START.DAT` member map, sprite ID ranges and all 93 SCRIPT opcode names, from pg_disatools' v1.2.1 release and source. Still open: unknown fields in those tables; what some SCRIPT arguments mean (all layouts now in `scripts-reference.md`); Disgaea 1 Complete's functions (nothing public exists, searched 2026-11-04; needs a decompile of the game, which needs your copy).
