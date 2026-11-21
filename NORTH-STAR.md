# North Star: Disgaea Modding Knowledge

*Version 1 · 2026-11-04 · owner: Sean13128*

## 1. The goal

A public, wiki-style reference for modding the Disgaea games. It is where people:

- learn **how each game works**: how its files are laid out, what its data tables hold, which engine functions exist;
- learn **what can be changed, and where the limits are**: which functions and tables can be modified, what breaks, which caps are hard-coded;
- see **which assets are compatible** across games (sprites, models, voices, maps) and what conversion they need;
- read **how existing mods were built**, written up by their authors or by us;
- find **community tools** and learn how to build on them instead of starting over.

**The test:** someone opens *Disgaea 2* and, without asking anyone, can answer: what is in each file, what the game's functions are, which of them people have hooked or patched, what the limits are, which assets from other games will work, which tools to use, and which mods show how it's done.

## 2. Who it's for

| Reader | What they need |
|---|---|
| Players who want to install mods | Which mods exist, which loader each needs, how to install and uninstall safely |
| New modders | Step-by-step guides that start from a clean install and use existing tools |
| Experienced modders and reverse engineers | Formats, table fields, function addresses, hook sites, engine limits, evidence |
| Tool authors | Shared formats across games, existing libraries, what's missing |
| Contributors | A clear template, rules on what may be posted, and credit for their work |

## 3. What a game page must answer

Every game gets the same set of pages, so a reader always knows where to look. Today each game has `README.md` and `INDEX.md`; the target set is:

| Page | Answers | Exists today |
|---|---|---|
| `README.md` | Overview: versions and platforms, where modding stands, which loader and tools to start with | Yes (template) |
| `INDEX.md` | File map: every archive and file, its format and contents | Yes (filled for 9 games) |
| `layout-*.txt` | Folder tree with sizes, per version | Yes (9 games) |
| `data.md` | Data tables: what each table holds, field by field, IDs and ranges | Partly, in seed docs |
| `functions.md` | The executable: function map, named systems (battle, menus, scripts, saves), known hook sites | Partly, in seed reports; decomp indexes kept privately |
| `modifiable.md` | What can be changed and how (data edit, file overlay, exe patch, runtime hook, script), with limits and known crash causes | No; scattered in seed reports |
| `assets.md` | Asset formats (sprites, models, textures, audio, maps), how to extract and rebuild, compatibility with other games | Partly |
| `tools.md` | Community tools that work on this game and version, with status and license | No |

Templates for every page are in [`templates/`](templates/); the rules are in [CONTRIBUTING.md](CONTRIBUTING.md).
| `mods/` | What specific mods do and how they interact with the game (files, tables, IDs, hooks, conflicts) | No |
| `guides/` | Step-by-step how-tos | No |

Shared topics that cross games live at the top level (see §7).

### Evidence labels

Modding knowledge is often half-proven. Every claim carries one label, so readers know how far to trust it:

| Label | Means |
|---|---|
| **Verified in game** | Tested in the running game; say which build |
| **Verified offline** | Proven by reading or rebuilding the files, or by an offline test, but not in the game |
| **From decomp** | Read from decompiled code. A lead, not proof of behavior |
| **Community-reported** | From a forum post, Discord or a mod's docs; link it |
| **Unknown** | Asked but not answered yet. Open questions are useful content |

## 4. What we have today

Most of the research behind these pages comes from a D5 porting project: bringing characters and classes from other Disgaea games into Disgaea 5 Complete. That makes coverage deep on D5, strong on games used as port sources, and thin elsewhere. The project's working notes stay private; facts are rewritten here for a general reader, with sources and evidence labels.

### Per-game coverage

*Snapshot from when this plan was written (2026-11-04, before the first pages). Current progress is in [STATUS.md](STATUS.md).*

| Game | Versions studied | File map | Data tables | Functions (private decomp index) | Mod tooling known | Coverage |
|---|---|---|---|---|---|---|
| **Disgaea 5 Complete** | PC Steam | Full | ~90 tables, field specs (Kit docs, Sorrow) | 28,901 functions; 2,483 tagged by Sorrow | D5 Mod Kit, Overlord, D5tools, disgaea5autoplay, Disgaea5Fix | **Deep** |
| **Disgaea 4** | Complete+ PC, Vita | Full | Kit D4 import docs (inventory, rules, effects, voices, scripts) | 19,926 functions; 2,339 strong matches to D5 | Kit importer; Artina, NisAnim (PS3, unmaintained) | **Deep** (as port source) |
| **Disgaea 7** | PC Steam | Full | 37 tables with named fields (Makai Kit) | 60,589 functions; ~4,800 matched to D5, ~3,000 strong | Makai Kit loader, Overlord Edition (beta) | **Strong** |
| **Disgaea 1 / 1 Complete** | PC Steam, Switch, PS4 | Good | D1C port notes; pg_disatools data formats; PC save format (YKCMP_V1 + XOR) | 2,420 (PC) and 2,364 (D1C); few matches to D5 | pg_disatools, Pretty Prinny, Netherworld Content Pack, HD sprite mod, EdiZon save config (Switch), Kit D1C reader; Disgaea DS formats (FireyFly) | **Medium** |
| **Disgaea D2** | PS3 (BLUS31313) | Good | DD2 census (558 rows), port notes | None yet | D2-RE Mac recompilation, Windows fork, Kit DD2 reader | **Medium** |
| **Disgaea RPG** | Android JP | Good | Table mapping and extraction reports | None (Unity IL2CPP) | UnityPy, AssetStudio, Kit RPG outfit builder | **Medium** |
| **Disgaea 2** | PC Steam (Mac build) | Basic | Port notes, stat evidence | 6,225 functions, 1,296 with real C++ symbols from the Mac build | Pretty Prinny (render fixes), PC save script (HenryEx), PSP save editor and PSP TXP textures (Scarlet); pg_disatools support unconfirmed | **Thin**, but the symbols are a big head start |
| **Disgaea 6** | Complete PC | Basic | Makai Kit D6 schemas | 41,874 functions; 1,714 strong matches to D5 | Makai Kit loader (incl. Lua script replacement), string tool | **Thin** |
| **Disgaea 3** | Vita (Absence of Detention) | Basic | Port notes | None | Seed readers only | **Thin** |
| Disgaea Infinite, Phantom Brave | None | None | None | None | Scarlet reads their textures (TXP, TX2, PSPFS); TCRF has Phantom Brave PS2 notes | **Minimal** |
| Prinny 1 & 2, Disgaea Mayhem (= Makaism), Makai Kingdom, Mugen Keitai | None | None | None | None | None found yet | **None** |

## 5. Community projects to build on

| Project | Games | What it does | License | Status |
|---|---|---|---|---|
| [D5 Mod Kit](https://github.com/eraser4521/D5ModKit) (eraser4521) | D5 PC | Full modding studio: characters, classes, outfits, skills, maps, database, scripts, mod manager | MIT | Active (v1.5.8) |
| [Makai Kit](https://github.com/HybridEidolon/makaikit) (HybridEidolon) | D6, D7 | Mod loader (dinput8), file replacement, JSON database patching (merge and patch files); Lua script replacement on D6 only; libraries for FAFULLFS, DSARC FL, NLSD, YKCMP | GPL-3.0 (mods don't inherit it) | No releases; 60 commits, date unclear |
| [pg_disatools](https://github.com/ProgSys/pg_disatools) (ProgSys) | Disgaea PC (D1) | DAT/MPP archive manager, map editor, sprite sheet editor, data editor, TX2 editor, mod manager | LGPL-3.0 / MIT | Active (v1.2.1 beta, Oct 2026) |
| [D5tools](https://github.com/BTAxis/D5tools) (BTAxis) | D5 PC | NISPACK, music and sound extract/repack; database to CSV | Not stated | Older |
| [disgaea5autoplay](https://github.com/Grid487/disgaea5autoplay) (Grid487) | D5 PC | Auto-battle via XInput proxy DLL; good example of runtime hooks | MIT | Experimental |
| Overlord Edition (Cryswar, Aryuto on Reddit) | D5, D7 | Rebalances, new evilities, playable bosses and characters, new stages ([D5 v1](https://reddit.com/r/Disgaea/comments/1our5w7/disgaea_5_overlord_edition_mod/), [D5 v2](https://reddit.com/r/Disgaea/comments/1ur6wvs/disgaea_5_overlord_edition_mod_v2/), [D7 beta](https://reddit.com/r/Disgaea/comments/1q2dj5n/disgaea_7_overlord_edition_mod_beta/)) | No licence stated publicly | Active |
| Disgaea Unleashed (ninakoru) | D5 | Raised caps (stats 199M, innocents 200k), evility and item rebalance; shipped as a replaced EXE and `Database_0.dat` | None | Steam guide now removed |
| Disgaea5Fix (Lyall) | D5 | Ultrawide and display fixes | — | — |
| Pretty Prinny (Kaldaien) | Disgaea PC, Disgaea 2 PC | Render fixes (resolution cap, MSAA, SSAO); ini-configured | — | Old |
| Disgaea PC HD sprite mod (community, Steam guide) | Disgaea PC | HD sprites for generics and the main trio, via a replaced `SUBDATA.DAT` | — | Old |
| Netherworld Content Pack | Disgaea PC | Restores Disgaea DS content; TALK.DAT editing | — | Old |
| [Disgaea D2 recomp (Mac)](https://github.com/Sean13128/disgaea-d2-recomp) / [Windows fork](https://github.com/LUUKASR1/disgaea-d2-recomp-windows) | DD2 PS3 | Native static recompilation; format and asset work | MIT | Mac unmaintained; Windows active |
| [NisAnim](https://github.com/xdanieldzd/NisAnim), [Artina](https://github.com/xdanieldzd/Artina) (xdanieldzd) | D4 PS3 | TXF image and ANM animation viewer; D4 data viewer | MIT | Unmaintained |
| [Scarlet](https://github.com/xdanieldzd/Scarlet) (xdanieldzd) | D2 PSP, Disgaea Infinite, D4 PS3/Vita, Phantom Brave | Image, container and compression decoders: TXP, TX2, TXF, NMT, NISPACK, NSAC, PSPFS, NIS LZS | See repo | Unmaintained |
| [Disgaea DS romfs notes](https://gist.github.com/FireyFly/d9ef476107d8b11cbe7f) (FireyFly) | Disgaea DS | File tree plus C tools for DSARCIDX, DSARCFL, DATTBL | — | 2015 notes |
| [Disgaea-DS-Manager](https://github.com/Team-BrewSearch/Disgaea-DS-Manager) (NitroShellMKDS, Team-BrewSearch) | Disgaea DS | Editor for DSARC and MSND archives | See repo | — |
| [D1/D2 PC save script](https://www.zenhax.com/viewtopic.php@t=4698.html) (HenryEx, aluigi) | Disgaea PC, Disgaea 2 PC | QuickBMS script: removes XOR, decompresses YKCMP_V1 saves, repacks | — | Old |
| [Disgaea 2 save editor](https://github.com/lmc/disgaea-2-save-editor) (lmc) | D2 PSP | Save editor (needs decrypted saves) | MIT | Old |
| EdiZon save configs | D1 Complete (Switch) | Save-editing config script | — | — |
| Disgaea6StringTool ([ZenHAX thread](https://www.zenhax.com/viewtopic.php@t=17081.html)) | D6 PC | Rebuilds `string.dat` text | — | Old |
| Cheat Engine tables, trainers | D6, D7 and others | Memory edits; a source of known addresses | — | Various |
| [UnityPy](https://github.com/K0lb3/UnityPy), [AssetStudio](https://github.com/Perfare/AssetStudio) | Disgaea RPG | Unity asset extraction | MIT | Active |
| [The Cutting Room Floor](https://tcrf.net/Disgaea_2:_Cursed_Memories) | D1, D2, D4, Disgaea DS, Phantom Brave, prototypes | Unused content, debug menus, file notes; also hosted a Disgaea PC modding index (2016) | Wiki | Active wiki |

Known community spaces: r/Disgaea (mod release posts), Steam community guides and discussions, Nexus Mods forum threads (D7), and Discord servers (the **Death's Requiem** Discord is the D5 PC modding hub, credited by several mods). No central Disgaea modding wiki was found in two rounds of searching; that is the gap this project fills. Most knowledge sits in Steam threads, forum posts, single-game repos and Discord, where it's hard to find and easy to lose.

## 6. Gaps to close

*As of the first draft. Progress against these gaps is in [STATUS.md](STATUS.md).*

1. **No pages yet for "what can be modified and its limits".** The knowledge exists, scattered across seed reports, Kit docs and Overlord source. This is the most valuable thing to write first.
2. **Functions are lists, not explanations.** The private decomp indexes give addresses and names; readers need "this is the battle damage function, this is how people hook it".
3. **Thin games**: D2, D3, D6 have file maps but little on data or functions. D2's Mac symbols and D6's Makai Kit schemas make them the next easiest.
4. **Little or no coverage** for Disgaea Infinite, the Prinny games, Mayhem and the related NIS games (Scarlet and TCRF are the only starting points found).
5. **Save formats** are barely covered: only D1/D2 PC (community script), D2 PSP and D1C Switch (save editors) have anything, and none are written up.
6. **Cross-game asset compatibility** is known only for "into D5" and "D6 into D7". A general matrix (which game's sprites, models, voices fit which other game) doesn't exist.
7. **Tool coverage per game and version** isn't recorded anywhere.
8. **Limits are folklore.** Examples like "Disgaea PC crashes at launch if `SUBDATA.DAT` passes 180 MB" (community-reported, and later lifted by a community-patched `dis1_st.exe`) live in comment threads. Each one belongs on a `modifiable.md` page with its evidence label.

## 7. Target shape of the repository

```
NORTH-STAR.md          this document
README.md              front page: what this is, how to find a game, how to contribute
CONTRIBUTING.md        rules, templates, evidence labels, what may never be posted
games/<group>/<game>/  the per-game pages from §3
formats/               shared formats (NISPACK, FAFULLFS, DSARC, YKCMP, LZS, TX2, TXF/NLTX, ANM, OBF ...)
engines/               shared engine families: which games share code, how knowledge transfers (BSim matches)
tools/                 catalog of community tools, one page each
mods/                  catalog of community mods, with links to their write-ups
guides/                cross-game how-tos (setting up a decomp, first data mod, porting a character)
```

How it's published (a plain GitHub repo, GitHub Pages with a static site generator, or a hosted wiki) is an open decision (§11). Plain Markdown works with all of them, so the choice can wait.

## 8. Rules for public content

These carry over from the seed's own rules, and become stricter once the repo is public.

**Never in the repo:**
- Game files, extracted game data or text, art, audio, models, or saves.
- Decompiled or recompiled game code.
- Modified game executables or archives. Mods are described, or shipped as patches and tools that run on the user's own copy. (Disgaea Unleashed shipped a replaced EXE and database through a Steam guide, and that guide has since been removed for breaking Steam's guidelines; the exact reason isn't stated, but it shows the risk.)
- Keys, dumps, or links to them.
- Personal paths, machine details and private notes.

**Tools and mods are described, not stored.** Pages link to them and explain what they do and how they interact with the game. Mod write-ups favor behavior and interaction over replicating the implementation.

**Allowed:** format descriptions, field layouts, function names and addresses, offsets, hashes of retail files, original tools and code, screenshots used for illustration, write-ups of how mods work.

**Credit and licenses:** every page credits the people whose work it draws on, and follows each project's license. Where a license is missing or unclear, ask before copying anything beyond a link and a summary.

### Public-safety status

This repository was started fresh from the finished pages. Working notes, research seed material, and anything removed for licence or privacy reasons were never part of it.

## 9. Roadmap

Each phase has a clear finish line. Phases 1 and 2 can overlap.

**Phase 0 – Foundations**
- Agree this North Star.
- Write `CONTRIBUTING.md`: page templates, evidence labels, the rules in §8.
- Licenses: CC0 for written content (no credit required, no restrictions), MIT for small original code. Done: [LICENSE.md](LICENSE.md).
- Finish line: a contributor could add a page correctly without asking.

**Phase 1 – Mine the seed, one game at a time**
- Order, by how much the seed holds: D5 → D4 → D7 → D1/D1C → DD2 → Disgaea RPG → D6 → D2 → D3.
- For each game, write the §3 pages from the seed, with evidence labels and credits. Each fact links to where it came from.
- Finish line per game: every §3 page exists, and the game's open questions are listed.

**Phase 2 – Shared topics**
- `formats/`: one page per shared container or format, listing which games use it.
- `engines/`: the engine families, from the BSim matches (D4–D7 share a lot; D1, D1C and D2 don't match D5).
- `tools/` and `mods/` catalogs from §5, with per-game and per-version support.
- Finish line: every format named in a game page links to its `formats/` page.

**Phase 3 – Fill gaps with new research**
- Save formats, D2 (using its Mac symbols), D6, D3, then the games with no coverage.
- Cross-game asset compatibility matrix.
- Finish line: no game is marked "None".

**Phase 4 – Go public**
- Public-safety audit against §8: file types, scans for paths and personal notes, and license checks.
- ~~Start a fresh public repo from the mined content~~ (done).
- Choose and set up the publishing site.
- Finish line: the repo is public and passes the audit.

**Phase 5 – Community**
- Invite tool and mod authors to review their pages and to add write-ups.
- Templates for "how my mod works" submissions; issue and PR templates.
- Finish line: outside contributors are adding pages.

## 10. How long runs operate

For agents (and people) doing the work over many sessions:

- **One game or topic per run.** Finish its pages before starting another.
- **Every fact gets a source and an evidence label.** No source, no claim; write it as an open question instead.
- **Rewrite, don't copy.** Seed material is notes for one project; pages are for a general reader. Plain language, short sections, tables for reference data.
- **Never copy game data in.** Check against §8 before each commit.
- **Check before you invent.** Look at existing tools, mods and the seed before describing something as unknown.
- **Review pass on every run:** re-read the pages against their sources, check links, search again in different words for anything missed.
- **Track progress** in a status file (per game: pages done, open questions, next step) so the next run can pick up.
- **Ask the maintainer only for decisions** (§11), licensing contacts and playtests.

## 11. Decisions

**Made (maintainer, 2026-11-04):**
1. **Content license:** CC0. The maintainer doesn't need credit and doesn't want to restrict use.
2. **Code license:** MIT, but tools aren't stored here; pages describe how they work.
3. **Function pages:** list function names and addresses.
4. **Mod write-ups:** document behavior and interaction, not how to replicate the mod.

**Acknowledged, needed before going public:**
- Permission from Cryswar to publish write-ups of Overlord Edition beyond its public release posts.
- Publishing platform (plain repo, GitHub Pages or hosted wiki).
- Related games: full pages or short notes (Phase 3).

## Sources

- The D5 porting project's private research notes, rewritten here; each page cites its own sources.
- Web: the project pages linked in §5; [Disgaea Mayhem release](https://www.animenewsnetwork.com/press-release/2026-04-24/disgaea-mayhem-to-launch-july-23-on-switch-2-switch-playstation-5-and-pc/.236771); [Disgaea PC mod index thread](https://steamcommunity.com/app/405900/discussions/4/371918937281416805/?ctp=27); [Disgaea Unleashed guide](https://steamcommunity.com/sharedfiles/filedetails/?id=2926099213); [D5 community discussion](https://steamcommunity.com/app/803600/discussions/0/2950376208535140648); [Disgaea 6 string tool thread](https://www.zenhax.com/viewtopic.php@t=17081.html); [D1/D2 PC save thread](https://www.zenhax.com/viewtopic.php@t=4698.html); [TCRF Disgaea 2](https://tcrf.net/Disgaea_2:_Cursed_Memories) and [Disgaea 4](https://new.tcrf.net/Disgaea_4:_A_Promise_Unforgotten); [Disgaea PC HD sprites thread](https://steamcommunity.com/app/405900/discussions/4/1742260837206441731); [Pretty Prinny for D2 PC](https://www.gamepressure.com/download/disgaea-2-pc-pretty-prinny-v07x-mod/zd10a7d); [Nexus D7 thread](https://forums.nexusmods.com/topic/13308945-disgaea-7-mods-for-steam-deck).
