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

## Checking an import against its source game, by measurement

When an imported sprite, stat or voice "looks off", measure it against the source game before changing anything. This
method came from the review rounds of the D4, D1 Complete, D2 and Disgaea RPG imports (November 2026). Everything below is
**Verified offline** unless marked.

1. **Decode the source with a reader of your own, not with the importer.** Agreement with the importer's own reading proves
   nothing. Where possible grade each reading rule by what backs it: the game's code run offline gives the same result
   (strongest); traced code plus a whole-dataset check; two independently written decoders agree; the shipped data is
   merely consistent; a community claim or inference (weakest). Decoding a file does not prove *when* the game uses it:
   grade which asset is chosen for which character separately from how the pixels decode.
2. **Compare drawn pictures, not files or ids.** Render the source motion at each of its own key times and the built bank at
   the same times, then compare the pixels. Report per motion the share of drawn pixels that differ, the feet-line offset and
   the loop length. Classify each difference by cause: the same picture after a shift (foot line), a held still where the
   source animates, blank where the source draws, a different picture, an id that exists only in the donor, a source motion
   the bank does not carry. Show source, built and installed side by side at the same scale with a shared foot line.
3. **Measure static art by size and proportion first.** A cut-in or face copied by a plain resize can silently change its
   aspect ratio. One example: Disgaea D2's cut-in is a 128 × 544 page whose drawn cell is 125 × 544; the D5 slot is
   336 × 1080 and a plain resize stretched it about 2.6 times across and 2 times down (aspect 0.230 became 0.311). D4,
   D1 Complete and Disgaea RPG cut-ins already have D5's proportions (0.31), so a plain resize is a uniform scale for them
   and only D2 needs care. The fix that loses and invents nothing is **one scale** to the slot's height, centred, with
   transparent bands at the sides; scaling to cover would crop about a quarter of the picture.
4. **Take every value the source row holds from the source row.** A builder that started from a donor row and filled in only
   id, base stats and aptitudes shipped the donor's movement, jump, throw, counter, critical, elemental resistance, weapon
   mastery and flying flag for every D2 class: 225 of 607 compared values differed across 12 packs. Compare field by field
   against an independent offset table. Leave a field on the donor only with a recorded reason (the donor game rebalanced
   it, no reader was found, no source byte exists).
5. **Rank the faults by how many characters each affects** before fixing one. In one survey of 46 D1 Complete, 44 D4, 12 D2
   and 170 Disgaea RPG characters, the largest groups were: source motions or clips the importer simply does not carry
   (every RPG character; most D1 Complete and D4 characters), donor-only motions the source has no equivalent for,
   shifted foot lines, one held still where the source animates, a stretched cut-in (12 of 12 D2 packs), and a sibling
   draw order that is reversed in D5 against D4 (66% of D4 packs; **From decomp**, not proven by a run). Fixing the importer
   cause is worth more than fixing a character. Mark each ranked cause *proven* only when every rule that decides it is.
6. **Say what a pass cannot show.** A pack that equals the source in a file comparison may still draw differently in the
   game (a bank whose shape differs freezes the Hire wheel; a strip narrower than its texture is a drawing the game was
   never shown). Keep "equal to the source offline" separate from "looks right in the game".

**Donor motions.** D5 asks for a motion id the importer may have no source for, and the game answers a missing id with an
empty cell. So an import fills each such id from somewhere. Three choices exist: D5's own motion drawn on the source art (the
default; honest only if labelled as not from the source); hold the source's standing pose under the id (invents nothing,
skills that call it show a still pose, but it changes the bank's shape); or carry every source motion and keep a donor only for
ids that the engine or a learned skill requests. A caster-motion survey with the kit's skill simulator counted donor ids used by
a learned skill: D4 45 of 559 (7 of 44 packs), D1 Complete 165 of 2,589 (28 of 46), D2 0 of 102. That count does not include
what the engine plays by itself (hit reactions, lift, throw, death, walking in eight directions), which a unit needs whatever it
learned. **Unknown:** how many donor ids are reachable in play.

**Motion names.** The ids that look shared between games are not: see [Motion ids and what they mean](../formats/motion-names.md).

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
- **D1 Complete humanoids are D5 humanoids.** D1 Complete draws its HD humanoids with D5's animation module on D5's shared
  motion set, so build them on D5's own resident motions with the D1 body's textures and palettes kept byte for byte,
  instead of flattening the rig into held poses (which left standing pictures where a reaction or lift belonged, and a walk
  1.55 times slower than the vanilla one). Bank shape then changes (4 to 6 texture pages against 2), which is the Hire-wheel
  memory class: declare the change per pack and check the wheel in the game before installing
  ([import gates](building-packs-from-your-own-game-copies.md#import-gates); **Verified offline**, with the game check
  still **Unknown** for those packs).
- **Disgaea D2's palette counts are big-endian.** Rewriting only the table bytes without the colour count crashes D5 on the
  first attack ([anm](../formats/anm.md)).
- **Tag the source game in generated class names** (for example "Gargoyle (D1C)") so two games' same-named classes do not
  double in the recruit list. Unique characters are not tagged.
- **Removing a port doesn't clean saves.** Restoring vanilla archives doesn't make a save that holds a ported unit safe;
  keep the mod installed for that save or go back to a save from before the port.

## Sources
Also: D5 Mod Kit development fork `docs/D4_BODY_IMPORT.md`, `docs/D4_UNIQUE.md`, `docs/D4_EXPANSION.md`.
D5 Mod Kit (CLASSES, OUTFITS, CHARACTER_STUDIO docs) and its development fork's D4 import docs; this project's
import work (2026-10); [Makai Kit](https://github.com/HybridEidolon/makaikit).
