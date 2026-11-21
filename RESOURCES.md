# Community Resources

Places outside this wiki with game mechanics, data and modding help. Tools and mods have their own catalogs:
[tools](tools/README.md), [mods](mods/README.md).

## Wikis and guides

| Resource | What's there |
|---|---|
| [Disgaea Wiki (Fandom)](https://disgaea.fandom.com/wiki/Critical_Hit) | Mechanics across the series: stats, critical hits, experience, classes, evilities |
| [r/Disgaea Netherwiki](https://www.reddit.com/r/Disgaea/wiki/index/d5) | Disgaea 5 strategy: grinding, post-game, evilities, FAQ |
| [The Cutting Room Floor](https://tcrf.net/Disgaea_2:_Cursed_Memories) | Unused content, debug menus and prototypes (D1, D2, D4, Disgaea DS, Phantom Brave) |
| [GamerGuides: Disgaea 5](https://www.gamerguides.com/disgaea-5-alliance-of-vengeance/guide/basics/gameplay/overview) | Systems walkthrough (team attacks, combos, lifting, Overloads) |
| [NIS America: Disgaea 5 systems](https://nisamerica.com/disgaea/d5/html/system/partysystem.html) | Official system pages (squads, team attacks, base) |
| Steam community guides and discussions | Mod releases and modding threads per game (see each game's `tools.md` and `mods/`) |

## Community spaces
- **Death's Requiem** Discord: the Disgaea 5 PC modding hub, credited by several mods.
- **r/Disgaea**: mod release posts (Overlord Edition, Classic Disgaea 5 Enhanced) and monthly question threads.
- **ZenHAX / XeNTaX** (archived forums): file-format threads (D1/D2 PC saves, D6 strings).

## Mechanics notes from the community

Useful when deciding what a modded number will do. All **Community-reported**, not checked by this project:

- **Damage** (simplified, D5/D7): `(attack stat × skill % × total attack power − 0.5 × defense stat × total defense power) ×
  (damage modifiers × critical damage × other multipliers)`. D5 and D7 share the structure; D6 differs outside the
  attack-versus-defense part. D5 applies elemental and weapon resistance as separate multipliers; D7 handles them
  differently. ([r/Disgaea](https://redlib.cypherpunktools.com/r/Disgaea/comments/1f5zz2i/rdisgaea_monthly_noob_questions))
- **Critical hits** add 50% to an attack's base power; in D7, critical-damage bonuses from evilities and item properties
  add together (cap +100%) and apply as a separate multiplier. ([Disgaea Wiki](https://disgaea.fandom.com/wiki/Critical_Hit))
- **Stat modifiers** are capped between +100% and −50% up to Disgaea 6; D7 removes the caps. Evility stat modifiers stack
  additively with buffs and debuffs. ([Disgaea Wiki, statistics](https://breezewiki.discard.no/disgaea/wiki/Statistics))
- Many effects translated as changing "ATK" or "DEF" actually change whichever stat feeds the damage formula.
