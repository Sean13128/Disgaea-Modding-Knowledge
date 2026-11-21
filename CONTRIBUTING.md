# Contributing

This is a wiki-style reference for how the Disgaea games work and how to mod them.
Read [NORTH-STAR.md](NORTH-STAR.md) for the goal. This page is the rulebook.

By contributing, you agree your writing is released under CC0 (public domain) and any original
code under MIT. See [LICENSE.md](LICENSE.md).

## The five rules

1. **Never add game material.** No game files, extracted data or text, art, audio, models, saves,
   decompiled or recompiled code, modified executables or archives, keys, dumps, or links to any of them.
2. **Describe tools and mods; don't store them.** Link to the project, and explain what it does,
   how it interacts with the game and its limits. Don't copy its code, binaries or files here.
   Mod write-ups document behavior and interaction (what it changes, which files, tables, IDs and
   hook sites it touches, what it conflicts with), not a recipe for replicating it. A short original
   snippet that shows a format or technique is fine.
3. **Every fact has a source and an evidence label** (below). If you can't source it, write it as
   an open question.
4. **Credit the people whose work you describe.** Name them and link their project.
5. **No personal details.** No machine paths, usernames, private notes or private conversations.

**Allowed:** format descriptions, field layouts, function names and addresses, offsets, IDs and
ranges, hashes of retail files, step-by-step guides, short illustrative screenshots, and write-ups
of how mods work.

## Evidence labels

Put one label on every claim that isn't obvious. Use the bold text exactly.

| Label | Use when |
|---|---|
| **Verified in game** | You tested it in the running game. Name the build |
| **Verified offline** | You proved it from the files or an offline test, not in the game |
| **From decomp** | You read it in decompiled code. A lead, not proof of behavior |
| **Community-reported** | It comes from a post, Discord, or a mod's docs. Link it |
| **Unknown** | Asked, not yet answered |

Example: `Stat cap is 99,999,999 by default (**Community-reported**: [Unleashed](link)).`

## Where things go

| Content | Location |
|---|---|
| Anything about one game | `games/<group>/<game>/` |
| A format used by more than one game | `formats/<format>.md` |
| Which games share engine code | `engines/` |
| A community tool | `tools/<tool>.md` |
| A community mod | `mods/<mod>.md`, linked from each game it touches |
| A how-to that spans games | `guides/` |

Game groups: `mainline`, `spin-offs`, `related`.

## Pages each game has

Copy the matching file from [`templates/`](templates/). Links inside the game templates are relative to a game folder, so
they only work after the copy.

| Page | Template | What it answers |
|---|---|---|
| `README.md` | [game-README.md](templates/game-README.md) | Overview, versions, where to start |
| `INDEX.md` | [game-INDEX.md](templates/game-INDEX.md) | Every file and archive, and what's in it |
| `data.md` | [game-data.md](templates/game-data.md) | Data tables, field by field |
| `functions.md` | [game-functions.md](templates/game-functions.md) | Engine systems, function names and addresses, hook sites |
| `modifiable.md` | [game-modifiable.md](templates/game-modifiable.md) | What can be changed, how, and the limits |
| `assets.md` | [game-assets.md](templates/game-assets.md) | Sprites, models, textures, audio, maps: formats and cross-game compatibility |
| `tools.md` | [game-tools.md](templates/game-tools.md) | Which tools work on which version |
| `mods/` | [mod-writeup.md](templates/mod-writeup.md) | How specific mods were built |

Shared pages: [format.md](templates/format.md), [tool.md](templates/tool.md).

## File listings

The `layout-*.txt` files and the file tables on `INDEX.md` pages come from scanning an installed copy. To make or
check one:

1. Start from a clean install (verify files in Steam, or a fresh dump), and note the store build number.
2. Record every file's relative path and size. Leave out anything a mod, mod manager or backup added, and say so in
   the file's header.
3. For archives, list member names and sizes with a reader named on the [formats](formats/README.md) page. Never copy
   member contents.
4. Write the date, platform and build at the top of the page's Sources.

A listing holds names and sizes only, so it can be published; it's evidence anyone with the same build can repeat.

## Writing style

- Plain language. Explain jargon the first time you use it.
- Short sections; tables for reference data.
- Say which **version and build** a fact applies to. Ports differ.
- Addresses: write them as the game's virtual address (`0x140123ABC`) and name the executable and its SHA-256.
- Link, don't repeat. If another page explains it, link to it.

## Before you open a pull request

- [ ] Nothing from rule 1 is in your change
- [ ] Every claim has a source and a label
- [ ] Relative links work
- [ ] [STATUS.md](STATUS.md) updated if you finished or started a page
