# Disgaea Modding Knowledge

How the Disgaea games work, and how to mod them: file layouts, data tables, engine functions,
what can be changed and where the limits are, which assets work across games, and how existing
mods and community tools do it.

> **Status:** first drafts. Every game with research on hand has a page set; see [STATUS.md](STATUS.md).

## Special thanks

This wiki stands on the work of the Disgaea modding community. Thank you to:

- **Cryswar** (Aryuto on Reddit): Overlord Edition for Disgaea 5 and Disgaea 7
- **eraser4521**: D5 Mod Kit
- **HybridEidolon** (Eidolon): Makai Kit, the Disgaea 6 and 7 mod loader
- **ninakoru**: Disgaea Unleashed
- **iHaku**: Classic Disgaea 5 Enhanced
- **Sorrow**: Disgaea 5 engine research
- **ProgSys**: pg_disatools
- **xdanieldzd**: Scarlet, NisAnim and Artina
- **Darkmet98**: Pleinair and the Disgaea 4 `.DAT` tools, with scripts by **Raugo** and **Luigi Auriemma**
- **HenryEx**: the Disgaea PC and Disgaea 2 PC save script
- **Xkeeper0**: disgaea-pc-tools
- **Kaldaien**: Pretty Prinny
- **Maide**: the Disgaea PC executable patch behind the HD sprite mod
- **Liminori**: higher-resolution image tests for Disgaea PC
- **BTAxis**: D5tools
- **Grid487**: disgaea5autoplay
- **Lyall**: Disgaea5Fix
- **FireyFly**: Disgaea DS format notes
- **NitroShellMKDS**: Disgaea-DS-Manager
- **lmc**: the Disgaea 2 save editor
- **twelvehouse**: makaikit-vits
- **JellyfishCraft86**: Disgaea-7-Trainer
- **LUUKASR1**: the Windows build of the Disgaea D2 port
- **triforce-heroes**: triforce-disgaea for Disgaea Mayhem
- The **Death's Requiem** Discord, and everyone who writes for the Disgaea Wiki and the r/Disgaea Netherwiki

Links to each project are in the [tools](tools/README.md) and [mods](mods/README.md) catalogs.

## Find a game

### Mainline

| Game | Year | Research on hand | Pages |
|---|---|---|---|
| [Disgaea: Hour of Darkness](games/mainline/disgaea-1-hour-of-darkness/) | 2003 | Medium | [Overview](games/mainline/disgaea-1-hour-of-darkness/README.md) · [Files](games/mainline/disgaea-1-hour-of-darkness/INDEX.md) |
| [Disgaea 2: Cursed Memories](games/mainline/disgaea-2-cursed-memories/) | 2006 | Thin | [Overview](games/mainline/disgaea-2-cursed-memories/README.md) · [Files](games/mainline/disgaea-2-cursed-memories/INDEX.md) |
| [Disgaea 3: Absence of Justice](games/mainline/disgaea-3-absence-of-justice/) | 2008 | Thin | [Overview](games/mainline/disgaea-3-absence-of-justice/README.md) · [Files](games/mainline/disgaea-3-absence-of-justice/INDEX.md) |
| [Disgaea 4: A Promise Unforgotten](games/mainline/disgaea-4-a-promise-unforgotten/) | 2011 | Deep | [Overview](games/mainline/disgaea-4-a-promise-unforgotten/README.md) · [Files](games/mainline/disgaea-4-a-promise-unforgotten/INDEX.md) |
| [Disgaea 5: Alliance of Vengeance](games/mainline/disgaea-5-alliance-of-vengeance/) | 2015 | Deep | [Overview](games/mainline/disgaea-5-alliance-of-vengeance/README.md) · [Files](games/mainline/disgaea-5-alliance-of-vengeance/INDEX.md) |
| [Disgaea 6: Defiance of Destiny](games/mainline/disgaea-6-defiance-of-destiny/) | 2021 | Thin | [Overview](games/mainline/disgaea-6-defiance-of-destiny/README.md) · [Files](games/mainline/disgaea-6-defiance-of-destiny/INDEX.md) |
| [Disgaea 7: Vows of the Virtueless](games/mainline/disgaea-7-vows-of-the-virtueless/) | 2023 | Strong | [Overview](games/mainline/disgaea-7-vows-of-the-virtueless/README.md) · [Files](games/mainline/disgaea-7-vows-of-the-virtueless/INDEX.md) |

### Spin-offs

| Game | Year | Research on hand | Pages |
|---|---|---|---|
| [Prinny: Can I Really Be the Hero?](games/spin-offs/prinny-can-i-really-be-the-hero/) | 2008 | None | [Overview](games/spin-offs/prinny-can-i-really-be-the-hero/README.md) · [Files](games/spin-offs/prinny-can-i-really-be-the-hero/INDEX.md) |
| [Disgaea Infinite](games/spin-offs/disgaea-infinite/) | 2009 | Minimal | [Overview](games/spin-offs/disgaea-infinite/README.md) · [Files](games/spin-offs/disgaea-infinite/INDEX.md) |
| [Prinny 2: Dawn of Operation Panties, Dood!](games/spin-offs/prinny-2-dawn-of-operation-panties/) | 2010 | None | [Overview](games/spin-offs/prinny-2-dawn-of-operation-panties/README.md) · [Files](games/spin-offs/prinny-2-dawn-of-operation-panties/INDEX.md) |
| [Disgaea D2: A Brighter Darkness](games/spin-offs/disgaea-d2-a-brighter-darkness/) | 2013 | Medium | [Overview](games/spin-offs/disgaea-d2-a-brighter-darkness/README.md) · [Files](games/spin-offs/disgaea-d2-a-brighter-darkness/INDEX.md) |
| [Disgaea RPG](games/spin-offs/disgaea-rpg/) | 2019 | Medium | [Overview](games/spin-offs/disgaea-rpg/README.md) · [Files](games/spin-offs/disgaea-rpg/INDEX.md) |
| [Disgaea Mayhem](games/spin-offs/disgaea-mayhem/) | 2026 | None | [Overview](games/spin-offs/disgaea-mayhem/README.md) · [Files](games/spin-offs/disgaea-mayhem/INDEX.md) |

### Related NIS games

| Game | Year | Research on hand | Pages |
|---|---|---|---|
| [Mugen Keitai Disgaea](games/related/mugen-keitai-disgaea/) | 2004 | None | [Overview](games/related/mugen-keitai-disgaea/README.md) · [Files](games/related/mugen-keitai-disgaea/INDEX.md) |
| [Phantom Brave](games/related/phantom-brave/) | 2004 | Minimal | [Overview](games/related/phantom-brave/README.md) · [Files](games/related/phantom-brave/INDEX.md) |
| [Makai Kingdom: Chronicles of the Sacred Tome](games/related/makai-kingdom/) | 2005 | None | [Overview](games/related/makai-kingdom/README.md) · [Files](games/related/makai-kingdom/INDEX.md) |
| [Makaism: Frenzy of the Netherworld](games/related/makaism-frenzy-of-the-netherworld/) | 2026 | — | Same game as Disgaea Mayhem |

"Research on hand" is how much source material exists for each game (Deep, Strong, Medium, Thin, Minimal or None), not how many pages are written yet; see [STATUS.md](STATUS.md) for that. Details in [NORTH-STAR.md](NORTH-STAR.md#4-what-we-have-today).

## About this project

| Page | What it is |
|---|---|
| [GLOSSARY.md](GLOSSARY.md) | Game and modding terms |
| [RESOURCES.md](RESOURCES.md) | Outside wikis, guides, community spaces, mechanics notes |
| [formats/](formats/README.md) | File formats shared across games |
| [engines/](engines/README.md) | Which games share code, and what transfers |
| [tools/](tools/README.md) | Every community tool, by game |
| [mods/](mods/README.md) | Every community mod, with write-ups |
| [guides/](guides/README.md) | Cross-game how-tos (planned) |
| [NORTH-STAR.md](NORTH-STAR.md) | Goal, current coverage, community landscape, roadmap |
| [CONTRIBUTING.md](CONTRIBUTING.md) | Rules, evidence labels, where pages go |
| [templates/](templates/) | Page templates |
| [STATUS.md](STATUS.md) | Progress per game |
| [LICENSE.md](LICENSE.md) | CC0 for writing, MIT for small code |

Unofficial and fan-made. Not affiliated with Nippon Ichi Software or NIS America. No game files are hosted here.
