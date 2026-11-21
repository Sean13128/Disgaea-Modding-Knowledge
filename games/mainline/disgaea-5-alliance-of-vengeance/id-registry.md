# Disgaea 5 Complete — ID Ranges in Use

Which ID ranges vanilla, D5 Mod Kit and Overlord Edition use, so new mods can avoid collisions. **Snapshot of
2026-10-26** (Overlord Edition v3; D5 Mod Kit with this project's class extensions). Before allocating IDs, check the
mods you target; ranges change between versions.

**Evidence:** D5 Mod Kit constants (`newclasses.py`, `masteryext.py`, `charstudio.py`, `voices.py`) and Overlord v3's
shipped tables, cross-checked by this project's ownership checker. **Community-reported.**

## `char.dat` rows (characters, class ranks)

| IDs | Owner | Notes |
|---|---|---|
| 1–1269 | Vanilla | Some rows are repurposed by mods (below) |
| 80 | D5 Mod Kit pack | Xenolith (Disgaea D2) reuses a vanilla NPC row |
| 90, 170, 430, 770, 820–850, 2610–2615 | Overlord | Vanilla rows reused for Berserk Usalia, Liezerota, Prinny Overlord, Baal, Baal Bits, Proto Darkdeath |
| 223–403 | D5 Mod Kit pack | 26 Disgaea 4 characters |
| 1270–1345 | D5 Mod Kit | "Human Dummy" class slots (8 classes × 6 ranks) |
| **1350–1355** | **Contested** | Claimed by both a D5 Mod Kit pack and Overlord. Don't use |
| 1360–1995 | D5 Mod Kit | New human class families (64 × 6 ranks) |
| 2220–2335 | D5 Mod Kit | "Monster Dummy" slots (12 × 6 ranks) |
| 2340–2395, 2620–2895 | D5 Mod Kit | New monster class families |
| 20000–20505 | Overlord | Battle Suit 20000, Android 20010, Stella 20300, Rutile 20310, Majin 20500 |
| 20506–21999 | Overlord (reserved) | Kept free for Overlord's future classes |

## Class identities

| IDs | Owner | Notes |
|---|---|---|
| 10000–19999 | D5 Mod Kit (reserved) | Registry identities, stride 10, ranks +0…+5; each is leased to a physical row above |
| 20000–21999 | Overlord (reserved) | Never allocate here |
| 22000–32760 | D5 Mod Kit (reserved) | Upper registry range |

## Unlock flags and mastery

| Range | Owner | Notes |
|---|---|---|
| Flags 17001–17282 | Vanilla | Class-rank unlock flags |
| Flags 17283–17996 | D5 Mod Kit | 119 families × 6 ranks: `17283 + 6 × index + rank`. Overlord also starts here: a known clash |
| Flags 17997–17999 | Unassigned | End of the class unlock block |
| Mastery slots 0–46 | Vanilla | |
| Mastery slots 47–52 | D5 Mod Kit | One imported class each; Overlord uses the same slots: a known clash |

## Other tables

| Namespace | IDs | Owner |
|---|---|---|
| Evilities (`Skill.dat`) | 6307–6314, 20000–20503 | Overlord |
| Evilities | 7000–7999 | D5 Mod Kit (class and character evilities) |
| Skills (`magic.dat`) | 40000–40312 | Overlord specials |
| Dark Assembly bills | 1049–1057, 20000–20500 | Overlord (recruit bills) |
| Sprites (`anm`) | 20000–20506 (animation sets), 30000–30506 (cut-ins), 30507–32769 (portraits), 13173–13186 (legacy save alias) | Overlord |
| Voice IDs | 4200–4205 (groups 42000–42050; inside the vanilla range) | Overlord |
| Voice IDs | 6000–9999 (handed out from 9000) | D5 Mod Kit custom voices |
| Voice IDs | 5999 (a voice with no lines: silent), 7000–7499 (Disgaea RPG), 7500–7599 (Disgaea 1), 7600–7699 (Disgaea D2) | This project's source-game imports (D5 Mod Kit development fork) |
| Skills (`magic.dat`) | 17000–17599 | This project's Disgaea RPG own skills |
| Evilities | 24000–26299 (Disgaea D2, 24000 + source ID), 26300–26999 (Disgaea D2, late band), 27000–27999 (Disgaea 4, 27000 + source ID), 28000 and up (Disgaea RPG, innate only) | This project's source-game imports. The late bands and 28000+ sort after every installed learnable row, so they never move a learned-evility bit; rows there are innate only |
| Quests (`Quest.dat`) | 1490–1999 | D5 Mod Kit development fork: hidden padding rows that keep high progress slots on the board. Keep real shop quests below 1490 |
| Dark Assembly bills (`WishDataBase.dat`) | 1000–1999 | Item Assembly only; the Strategy (Dark Assembly) list hides them. IDs of 2000 and up are listed by the Strategy predicate and never by the Item Assembly ([functions](functions.md#dark-assembly-bill-lists), **From decomp**) |

Vanilla limits that constrain all of these (sprite IDs ≤ 32,767, unique characters < 1000, append-only rows) are on
[modifiable.md](modifiable.md).

## Sources
This project's class-registry addendum and ownership manifest (2026-10-26); D5 Mod Kit source constants; Overlord
Edition v3's shipped tables (Cryswar).
