# Disgaea D2: A Brighter Darkness — What Can Be Modified

Two very different situations:

- **On a PS3 or RPCS3:** no public mods were found. Changing files means rebuilding NISPACK archives; saves can be
  decrypted and edited with PS3 save tools.
- **In the native port** (macOS, with a Windows fork): the game runs as native code, so the port can add features
  directly. It ships a **Cheats** menu, a visual **Item Editor**, and an experimental **costume system** that brings
  Disgaea RPG outfits into D2.

**Evidence:** **Community-reported** from the native port's reports and the D5 Mod Kit development fork.

## By area

| Area | What's known | Limits |
|---|---|---|
| Data tables | Readable; layouts on [data.md](data.md) | Pick one patch level; the 1.40 update replaces 26 data files |
| Sprites | Readable (same 10-table anm structure as D5, 16-byte node rows like D4); the port builds new bodies and verifies them with the game's own LZS decoder. Which bank a character uses, and the pack overlay order, are on [assets](assets.md#sprite-banks-for-a-character) and [INDEX](INDEX.md) | **Verified in game** (port): an imported Etna outfit plays idle, movement, equipment screens and the Prinny Raid skill |
| Faces | Compressed TXF; the port can widen the face atlas and append rows | The game's fixed face tables **don't find appended rows by themselves**; the port routes them in code |
| Illustrations | `anm1NNNN` (for example `anm10030`) is the large Status screen illustration, confirmed by recolouring it in game ([which file is which](data.md#which-picture-a-unit-uses)) | Other illustration files' screens aren't confirmed |
| Costumes | Port: pick Original or an imported costume, live in the castle; choices persist | Castle only; battle switching, other characters and full action sets not yet verified |
| Items, stats, skills (live) | Port: Cheats menu (HP/SP lock, one-hit kills, free shop, EXP multiplier up to 1024×, unit fields and skills) and a visual Item Editor ([item record](data.md#game-state-in-memory-140)) | 999 item slots, 4 equipment slots per unit, 6 innocents per item |
| New characters | An appended `char.dat` row works as a **visual alias** (renderer only); the lookup has no fixed row count ([limits](data.md#looking-up-rows-and-what-limits-adding-one)) | A new playable class isn't proven: an ID with no row is read out of bounds, and the roster (128), battle actors (160), sprite pools and a fixed 870-key visual list are separate limits |
| Saves | PFD-protected on PS3; the port imports retail saves into plaintext | Partly mapped: units at root `+0x598` × `0x1A60`, items at `+0xDD518` ([data.md](data.md)) |
| Voices | Readable (2026-11) | Clips are found through the executable's selector table; see [data](data.md#voices) |
| Special-attack scenes as a source | Offline only: the scripts are in Disgaea 4's scene language, so 57 of the 59 D2 specials that Disgaea 5 ports as stand-ins convert to Disgaea 5 scenes, and 55 pass every offline gate ([functions](functions.md#special-attack-scenes)) | **Verified offline.** Sound effects and clip-ID voice lines don't carry; 150 of 185 scene-created actors are invisible helpers; a crash first seen with one converted attack was traced (2026-11-21) to D2 palette counts, not to the scenes ([functions](functions.md#special-attack-scenes)); the full set's boot is unproven |
| Evility rules | Readable from the executable ([data](data.md#signature-evilities-read-from-the-executable)); the angel and flat groups are hard-coded, not in any table ([data](data.md#angel-and-flat-groups-evilities-140-and-550)) | **From decomp.** Porting them to Disgaea 5 needs a D2-ID-to-D5-class mapping, and several rules (per-step healing, per-holder counters) have no D5 hook; the D5 Mod Kit development fork builds adapted versions of some |
| Cut-ins and face icons | Readable: cut-in strips are separate banks (`anm10000+`), face icons come from `wf_*` atlases ([assets](assets.md#cut-ins-and-face-icons)) | **Verified offline.** Using a D2 strip in Disgaea 5 needs fitting (aspect 0.230 against 0.311); how D5 screens draw it is **Unknown** |
| Flying and movement | Row fields `+0x1A2` MOV and `+0x1A4` flying flag ([data](data.md#battle-fields-in-the-chardat-row)) | **From decomp.** Whether Disgaea 5 honours a flying flag on a bank with no flying walk motion is **Unknown** |

## Things to know

- **Give Up resets to the title**, not to the castle (seen while testing the port).
- The costume recipes the port shares (`*.d2costume.json`) hold **no artwork**: each user rebuilds the costume from
  their own game and their own Disgaea RPG files.

## Sources
[Disgaea D2 native port](https://github.com/Sean13128/disgaea-d2-recomp) (README, costume-recipe and importer reports); D5 Mod Kit development fork `d5lib/dd2source.py`; [pfdtool](https://github.com/SteffenL/pfdtool), [Apollo](https://github.com/bucanero/apollo-ps3).
