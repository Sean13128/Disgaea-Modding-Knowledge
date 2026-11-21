# Disgaea 4 — Evilities compared with Disgaea 5

How each D4 evility used by the D4 → D5 import maps onto D5. Useful if you port a D4 class or character to D5, or want
to know which D4 effects D5 can already express.

**Evidence:** the D5 Mod Kit fork's D4 evility review (four batches, October 2026): 195 D4 evilities from the
45 imported classes and selected uniques, each compared by text and stored value against D4's `mskill.dat` and D5's
`Skill.dat`, then the D5 code that implements the twin, then (for rebuilt ones) the rebuilt rule run in an emulator at
the game's own call sites. **Verified offline**, not tested in play. It covers only the evilities those classes carry, not
all 1,230.

Counts below are the October 2026 review's. Rows marked "→ rebuilt 2026-11" were later rebuilt as Kit rule rows; see
[the November section](#signature-rules-rebuilt-for-d5-2026-11).

| Verdict | Count | Meaning |
|---|---:|---|
| **exact** | 94 | Reproduced with D4's own number. With a **D5 twin**, D5 already has the same evility (same text, same value) and the import just reuses it; without one, it was rebuilt as a D5 Mod Kit fork rule row on D5's own code paths |
| **adapted** | 20 | Uses the closest D5 mechanism; numbers or conditions differ |
| **lookalike** | 1 | Same idea, different effect |
| **omit** | 80 | No D5 mechanism for it yet (left out rather than faked) |

| D4 ID | Name | D5 twin | Verdict |
|---:|---|---:|---|
| 2 | Power Survival | 2020 | exact |
| 4 | Cleanse Evil | — | exact |
| 5 | Boy Hunt | — | adapted |
| 7 | Hawk Down | — | exact |
| 8 | Physical Boost | — | exact |
| 11 | Spirit Benediction | — | exact |
| 13 | Fire's Blessing | — | exact |
| 14 | Wind's Blessing | — | exact |
| 16 | Star's Blessing | — | exact |
| 19 | Blood Insanity | — | exact |
| 22 | Superiority | — | exact |
| 24 | Calming Aura | — | exact |
| 25 | Pretty Motion | — | exact |
| 26 | Macho Splendor | — | exact |
| 28 | Immortal Body | 2210 | exact |
| 32 | Gel Body | 2060 | exact |
| 38 | Shield Mastery | — | exact |
| 40 | Max. HP DEF Boost | — | exact |
| 44 | Bow Range UP | — | adapted |
| 48 | Enlarge | — | exact |
| 61 | Muscle Mastery | 1011 | exact |
| 62 | Mind Mastery | 1041 | exact |
| 63 | Offense Mastery | 1191 | exact |
| 64 | Defense Mastery | 1021 | exact |
| 65 | Casting Mastery | 1031 | exact |
| 66 | Dodge Mastery | 1091 | exact |
| 67 | Snipe Mastery | 1111 | exact |
| 68 | Resist Mastery | 1081 | exact |
| 71 | Armor Mastery | — | exact |
| 72 | Dark Mastery | — | omit |
| 73 | M Weapon Master | — | omit |
| 75 | Gimmick Body | — | adapted |
| 81 | Killing Hand | — | exact |
| 83 | Sword Force | — | exact |
| 84 | Energy Slash | — | exact |
| 85 | Spear Defense | — | exact |
| 86 | Spear Sting | — | exact |
| 88 | Power Shot | — | exact |
| 89 | Gun Trick | — | exact |
| 90 | Heavy Smash | — | exact |
| 91 | Concentrate | — | exact |
| 92 | Illusion | — | exact |
| 94 | Mach Strike | — | exact |
| 96 | Mind Focus | — | exact |
| 97 | Magic Force | — | exact |
| 98 | Magic Conversion | — | exact |
| 103 | Dark Tamer | — | exact |
| 106 | Prevention | 2111 | adapted |
| 108 | Wild Cry | — | exact |
| 111 | Shroom Power | 2100 | exact |
| 113 | Sexy Aura | 2140 | exact |
| 114 | Miasma of Defiance | — | omit |
| 115 | Miasma of Silence | — | omit |
| 117 | Miasma of Darkness | — | omit |
| 118 | Miasma of Laze | — | exact |
| 121 | One Man Army | — | omit |
| 127 | Mass Psyche | — | omit |
| 129 | Geo Energy | — | omit → rebuilt 2026-11 |
| 131 | Dark Blood | — | omit |
| 133 | Stone Piece | — | omit |
| 135 | Heavy Stance | — | adapted |
| 172 | Darkness Pact | — | omit |
| 173 | Activation | — | exact |
| 176 | Chicken Heart | — | adapted |
| 180 | Rapid Move | — | omit |
| 181 | Battle Order | 1170 | exact |
| 182 | Vengeance | — | exact |
| 183 | Meditation | — | exact |
| 184 | Accelerate | — | exact |
| 185 | Accuracy | — | exact |
| 186 | Fortitude | — | exact |
| 187 | Herbal Therapy | 2112 | adapted |
| 188 | Sweet Aroma | 2110 | adapted |
| 191 | Relaxation | 2113 | exact |
| 205 | Teaching License | — | omit |
| 211 | Cursed Dance | — | exact |
| 212 | Hazard Dance | — | omit |
| 213 | Ominous Dance | — | omit |
| 214 | Ruin Dance | — | adapted |
| 215 | Dismal Omen | 141 | adapted |
| 216 | Mind Shackle | — | omit |
| 223 | Grudge Time | — | exact |
| 228 | Last Fortress | 6082 | exact |
| 229 | Evasive Maneuvers | — | exact |
| 230 | Anti Elements | — | omit |
| 261 | Poisonous Matter | 2061 | exact |
| 262 | Hypnosis Wave | — | exact |
| 263 | Unholy Talon | — | exact |
| 264 | Amnesic Blast | 2031 | exact |
| 265 | Temptation | — | omit |
| 266 | Star Crossed Fate | — | adapted |
| 271 | Poison Chance | 2092 | exact |
| 272 | Dream Hunt | — | exact |
| 273 | Death Fang | 2211 | exact |
| 274 | Spiritual Wave | — | exact |
| 275 | Punishing Punisher | — | omit |
| 276 | Deadly Poison | — | omit |
| 291 | Poison Mutilation | — | exact |
| 294 | Voluntary Bondage | — | omit |
| 296 | Degraded Life | — | omit |
| 301 | Death's Heal | — | omit |
| 304 | Medical Insertion | 1180 | adapted → rebuilt 2026-11 |
| 320 | Echo | — | omit |
| 335 | Assault Attack | — | adapted |
| 337 | Accel Shot | — | omit |
| 343 | Elemental Force | 1160 | exact |
| 344 | Calm Waters | — | adapted |
| 345 | Mass Blaster | 1210 | exact |
| 347 | Bloody Battle | — | exact |
| 348 | Vendetta | 2124 | exact |
| 349 | Accel Blow | — | omit |
| 350 | Sharp Eye | — | exact |
| 351 | Misty Haze | — | exact |
| 352 | Flicker Wing | — | exact |
| 354 | Flame Haze | — | adapted |
| 355 | Trick Move | — | omit |
| 356 | Cat Step | 2074 | exact |
| 358 | Euthanasia | 1143 | exact |
| 367 | Patience | — | exact |
| 378 | Vision Blur | — | omit |
| 385 | Mind's Eye | 1141 | exact |
| 391 | Advance Guard | — | exact |
| 392 | Aegis | — | exact |
| 402 | Heart's Fertility | — | omit |
| 406 | Magic Moderation | 1040 | exact |
| 407 | Magic Bundle | 1030 | exact |
| 408 | Heart Steal | — | omit |
| 409 | Thief Skills | 1090 | exact |
| 411 | Lucky Finger | 1094 | exact |
| 412 | Secret Potion | — | omit |
| 421 | Photosynthesis | — | exact |
| 422 | Magic Creation | 2050 | exact |
| 423 | Anger of Earth | — | omit |
| 425 | Corpse Eater | — | omit |
| 429 | Revival | — | exact |
| 430 | Soft Inspiration | — | adapted |
| 431 | Fresh Green Sprout | — | omit |
| 441 | Team Formation | 2122 | exact |
| 445 | Synchronize | — | omit |
| 446 | Second Attack | — | omit |
| 448 | Assist | — | omit |
| 449 | Marking | — | omit |
| 474 | Explosive Nature | — | omit |
| 475 | Evade Danger | 1093 | exact |
| 476 | 1HL | — | omit |
| 608 | Special Counter | — | omit |
| 609 | Cross Counter | — | omit |
| 610 | Ambush | — | omit |
| 625 | Angel Protection | — | exact |
| 626 | Angel Potion | — | omit |
| 627 | Angel Blessings | — | omit |
| 628 | Angel's Flight | — | omit |
| 656 | Dead Man's High | — | omit |
| 663 | Rising Dragon | — | omit → rebuilt 2026-11 |
| 665 | Dragon's Treasure | — | omit |
| 666 | King's Dignity | 2204 | exact |
| 689 | Corpse Heal | — | omit |
| 690 | Speedy Move | — | omit → rebuilt 2026-11 |
| 691 | Mind Killer | — | omit → rebuilt 2026-11 |
| 692 | Accel Element | — | omit |
| 693 | Invincible Ruler | — | omit |
| 695 | Magic Wand | — | omit |
| 697 | Official Protection | — | adapted |
| 698 | Synch Guard | 1151 | exact |
| 699 | Damage Shift | — | omit |
| 700 | Deadly Support | — | omit |
| 800 | Geo Resist | — | omit |
| 801 | Geo Damage | — | omit |
| 802 | Self-Destruct | — | omit |
| 803 | Limiter Release | — | omit |
| 804 | Rear Monitor | — | omit |
| 805 | Damage Cut | 2203 | adapted |
| 806 | Basic Rule | — | omit |
| 807 | Stone Statue | — | omit → rebuilt 2026-11 |
| 808 | Stone Guard | — | omit |
| 809 | Stone Wall | — | exact |
| 810 | Effective Delivery | 1184 | adapted |
| 815 | Absorption | — | exact |
| 822 | Veteran Nurse | — | omit |
| 836 | Vanishing Cat | — | omit → rebuilt 2026-11 |
| 837 | Meowyland | — | omit |
| 838 | Playing Dead | — | omit |
| 839 | Cat Block | — | omit |
| 840 | Friends | — | omit → adapted 2026-11 |
| 841 | Prism Emblem | — | omit |
| 848 | Consecutive Shot | — | omit |
| 849 | Nirrti | 6061 | adapted |
| 850 | Fall of Castle | — | omit |
| 864 | Base Hospital | — | omit → rebuilt 2026-11 |
| 865 | Sanitation Control | — | omit |
| 866 | Medical Error | — | omit |
| 867 | Transportation | — | omit |
| 868 | Necromancy | — | omit |
| 869 | Undead | 2093 | lookalike |
| 870 | Cream of Blood | — | omit |

## Signature rules rebuilt for D5 (2026-11)

Signature evilities (one per character or class) have no D5 twin, so the D5 Mod Kit development fork rebuilt them as
rule rows on D5's own code, each checked against the D4 rule in [functions.md](functions.md#signature-evility-rules-traced-in-november-2026).
**Verified offline** means the rebuilt rule was run through the real D5 call sites in an emulator against the original
executable; **Verified in game** means it was read in a real D5 battle (D5 build B with the Kit installed) against a unit
without the rule. Live results are single reads, not statistics.

| D4 ID | Name | Offline | In D5 battle | Difference from D4 |
|---:|---|---|---|---|
| 875 / 878 | Phantom Lover / Eccarlate | Six stats only | Six stats exactly +5% with one other unit present, HP and SP flat | D4 has no HP/SP term; an earlier port raised all eight |
| 304 | Medical Insertion | `(value+100)/100`, cap not carried | A support buff rose 65,800 → 95,410 (+45%) | D5's own evility 1180 multiplies by an integer; the rebuilt row replaces its result for support magic |
| 892 | Everyone's Mechanic | Weapon slots only, sum capped 100 | A weapon wearer's stats rose when the holder arrived; the magnitude was not checked | D5 multiplies where D4 adds when D5's own weapon-percent evility is also present (715 against 700 in the proof) |
| 667 | Tyrant Guard | Exact for a threshold of 0 or 1 | First enemy action: both hits did 0; second action: −16; a lethal first hit at HP 1 killed the holder | D5's own first-hit nullify also stopped lethal hits; the row now lets them through |
| 844 / 351 | Kyoko / Reprachon evasion | Product of factors, 844 subtracts points before the clamp | Preview hit chance: Reprachon front 25%, behind 60%; Kyoko 50% → 1% with the named neighbour present | D5 rolls once (D4 twice) and floors at 1% (D4 at 0%) |
| 690 | Speedy Move | Counter added once per confirmed move | SPD 112,800 → 129,720 (+15%) after a 3-panel move; back to 112,800 after End Turn; control unchanged | D4 sets the counter, D5 adds each confirmed move |
| 663 | Rising Dragon | Counter, cap 20, reset every action | Counter +1 per enemy attack (a miss counted), 0 after his own counter-attack; counter damage about ×1.19 at 3 and ×2.2 at 14 | D5's own counter ignores elemental hits; heal test is a category in D5, a sign in D4 |
| 807 | Stone Statue | DEF +10/20/30/100% at 1/2/3/10 idle turns | DEF 91,800 → 100,980 / 110,160 / 119,340 after End Turns 1–3; a one-panel move reset it | D5 also clears the counter for three more reasons |
| 129 | Geo Energy | Distinct colour count | Six stats ×1.6 on a six-colour stage | Not carried: D4 clamp shared with other terms |
| 836 | Vanishing Cat | Facing values and ties proven | Target facing changed after her hit (one shot, qualified); overwritten 1.1 s later by its own move | Tie rules and zero distance now match D4 |
| 864 | Base Hospital | Per provider per round | Benched units gained 5% of max HP and SP; deployed and dead units did not; no change without a Medic | Only the player side's providers fire; D5's recipients are limited to the player roster |
| 820 | Physical Malice | — | Battle table 9 → 10 → 11 → 12 units, one per round, all on team 4; no growth without the holder | |
| 802 | Self-Destruct | — | At 10% HP the holder was hit, exploded, was defeated, and an adjacent enemy was defeated; a control unit did not explode | Not run: a hit well above 10% |
| 115 | Miasma of Silence | Not built | — | Seven D4 consumers; the D5 "may this unit use this skill" check has four callers and a mapping from D4's special skills to D5 skill classes is not established |

Not read in a battle by this work: Unlosing Mode, Friends' squad count, Roc, Nagi's magnitude, and Chief against a
control caster (**Unknown**). Necromancy (868) is not covered here.

## Sources
D5 Mod Kit development fork, `d4-review` ledgers (`LEDGER*.json`, batches 1–4, 2026-10); see also
[functions.md](functions.md) for how individual D4 rules (Aegis, Bloody Battle, Physical Boost, the Blessings) work.
The November section comes from the same fork's `docs/EVILITIES.md` and this project's signature-evility and live-battle
reports (2026-11-12 to 2026-11-14).
