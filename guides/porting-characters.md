# Bringing a character from one Disgaea into another

What it takes to move a character or class between games, from the two routes that have been done: **into Disgaea 5**
(from D4, D1 Complete, D2 and Disgaea RPG, with D5 Mod Kit) with notes on **Disgaea 6 → Disgaea 7** (with Makai Kit), where ports are still **Unknown**. Neither route copies game files into a shareable form: each user builds from their own copies; for how a
converter finds and verifies those copies see [Building character packs from your own game copies](building-packs-from-your-own-game-copies.md).

## Which pairs work

| From → to | Art | Difficulty |
|---|---|---|
| D4, D1 Complete, D2 (Brighter Darkness) → D5 | HD 2D sprites, close formats | Medium: convert anm files, fit poses into D5's animations |
| Disgaea RPG → D5 | Same drawings as D5's generics | Easiest for looks |
| Disgaea 2, Disgaea 3 → D5 | Low-resolution sprites | Needs whole-number scaling; not done yet |
| D6 → D7 | 3D models, Lua skills | Done: models, animations, voices and skills carry over |
| 2D games ↔ 3D games | Sprites vs models | No known path |

See [engines](../engines/README.md) for why.

## The pieces, in order

1. **Identity.** Pick IDs the target doesn't use and that saves can survive. In D5: unique characters below 1000,
   classes in the reserved "Dummy" families or patched ranges, and only **append** table rows
   ([D5 modifiable](../games/mainline/disgaea-5-alliance-of-vengeance/modifiable.md)). In D7: IDs unused by vanilla
   and every installed mod.
2. **Data rows.** Character (and, for a class, six rank rows plus the rank chain), descriptions, personalities (D5
   needs **three**), team-attack rates.
3. **Look.** D5: sprite (`anmN`), standing picture, portrait/cut-in (`anm(10000+N)`), face, list icon; palettes per
   rank. D7: model, animation set, textures and their `model`/`modelinfo`/`modelsequence`/`texture` rows.
4. **Skills.** Easiest: copy a target skill with a similar animation and give it the source game's numbers. Real
   source animations need their scripts translated (D4 → D5 differs in 12 commands) and every mechanic they use
   (Lift Catch's carry) to exist in the target.
5. **Evilities.** In **D5** behavior is code: map each source evility to the closest D5 one (exact, adapted,
   lookalike, omitted) or add code; for D4 sources the
   [D4 → D5 evility map](../games/mainline/disgaea-4-a-promise-unforgotten/evilities-vs-d5.md) has 195 done, and the
   [Disgaea D2 → D5 map](../games/spin-offs/disgaea-d2-a-brighter-darkness/vs-d5.md) covers every D2 skill and evility. In **D7** behavior is data: an evility lists effect blocks
   ([D7 data](../games/mainline/disgaea-7-vows-of-the-virtueless/data.md)).
6. **Voices.** Convert to the target's format and map the source's voice situations to the target's. D4 → D5: only
   six categories mapped so far; Disgaea RPG has no character-to-voice table at all.
7. **Recruiting.** D5: a recruit quest or the Recruiter (classes). D7: Unknown.
8. **Label what isn't faithful.** Keep a note of every adapted skill, evility and voice.

## Lessons from the D5 imports
- Long class descriptions (> 255 bytes) and stray `%` signs crash or freeze D5 menus.
- Check voice **gender**: a male class given a female voice set speaks female.
- Rank colours come from palettes: use the source's real palettes, not palette 0 on every rank.
- Two mods adding classes (Overlord and D5 Mod Kit) collide on unlock flags, mastery slots and class rows.
- **Complete poses vs. multi-part animations.** D4 (and other games') art is drawn as complete poses, but some D5
  animations draw the body, an arm, hair, a shield or a flame on separate tracks. Putting a complete pose on every
  part draws several overlapping figures (it showed up in Create Character). The D4 import keeps one main body track
  per animation, the one with the largest original cell, and leaves weapon and effect tracks alone; after that, all
  240 standing and idle animations of the 45 imported classes showed exactly one figure at every tick (**Verified
  offline**). The choice is per animation: the same donor cell can be the body in one and an arm in another.
- **Leave donor attachment animations alone.** Some donor bodies pull small parts in through nested keys (a `0x100` key:
  D5 class 2130's motion 1001 nests animation 1591, a 64 × 64 glyph; 2210's nests 1501, a scythe strip). Painting the
  whole imported pose into those draws the character a second time (seen on the Recruiter's centre preview). Keep them
  blank unless the source has a matching part.
- **Check the donor's body track for rotation keys.** A whole-pose drawing painted into a key that carries a rotation
  tumbles with it: D5's Winged Warrior (2080) rotates its body track 90° on one key of its walk and damage animations,
  so a D1 Gargoyle built on it fell over in one frame. Pick a donor whose body track has no rotation (the Gargoyle
  works on Succubus 2140), or draw whole-pose keys upright and keep rotation only on part keys. Nightmare's idle
  variants rotate −20° to −130° for the same reason (**Verified offline**: rendered and compared, D5 Mod Kit fork, 2026-10).
- **Source art can have blank frames.** D1 Complete's Vampire body (2180) has empty cells in its own animation 1501;
  drop those keys rather than inventing art for them.
- **Same name, different skill ID.** Skills that both games have keep different IDs: D4's Ice is skill 721, D5's is
  1201 (scene 15150500). Map by what the skill does, not by its number.
- **Reuse before you copy.** D5 already has some D4 characters' skills (Valvatorez's three). A comparison port can use
  those, but D5's versions keep D5's damage, targeting and effects. Evilities with no D5 equivalent (Valvatorez's
  innate Bloody Battle, D4 ID 347) are better left out than replaced with an unrelated one.
- **Removing a port doesn't clean saves.** Restoring vanilla archives doesn't make a save that holds a ported unit safe;
  keep the mod installed for that save or go back to a save from before the port.

## Sources
Also: D5 Mod Kit development fork `docs/D4_BODY_IMPORT.md`, `docs/D4_UNIQUE.md`, `docs/D4_EXPANSION.md`.
D5 Mod Kit (CLASSES, OUTFITS, CHARACTER_STUDIO docs) and its development fork's D4 import docs; this project's
import work (2026-10); [Makai Kit](https://github.com/HybridEidolon/makaikit).
