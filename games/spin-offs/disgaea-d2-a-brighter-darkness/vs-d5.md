# Disgaea D2 — Skills and evilities compared with Disgaea 5

How every Disgaea D2 skill and evility maps onto Disgaea 5: the D5 row that can stand in for it, if any. Useful for
porting D2 classes or characters to D5 ([porting guide](../../../guides/porting-characters.md)).

**Evidence:** **Verified offline**: a whole-table comparison of D2 (PS3, update 1.40, `START_4.dat`: 386 skills, 227
evilities) against D5's `magic.dat` (583 rows) and `Skill.dat` (796 rows) by name, ID, weapon and tier slot, spell
family and effect text (D5 Mod Kit fork, 2026-10). A verdict describes the mapping, **not tested in play**.

| Verdict | Skills | Evilities | Meaning |
|---|---:|---:|---|
| exact | 156 | 45 | D5 has the same skill or evility (same ID, or the same name under another ID) |
| adapted | 67 | 71 | Same weapon/tier slot, spell family or effect family; D5's own version runs, numbers may differ |
| lookalike | 108 | 83 | Only a neighbour in the same slot exists (for example D2's eighth weapon tier) |
| needs new code | 0 | 27 | No D5 evility does this; it would need new engine code |
| omit | 55 | 1 | No D5 stand-in at all: Book summons, mounted and combo attacks, the 18xx/20xx support block |

**Corrections (2026-11-14, From decomp):** two evilities first marked exact were re-checked against the D2 executable
and are **adapted**. Rapid Movement (1210) has the same order and 64 cap as D5's 1060 but subtracts 2% per panel where
D5's row gives 3%, and a mod can't change the number in a vanilla row. Acceleration Mascot (320) gives +1 move per
other holder anywhere on the field (holders stack); D5's 6024 gives +2. Limitless Love (140) and Flat Alliance (550)
are tied to hard-coded groups, see [data](data.md#angel-and-flat-groups-evilities-140-and-550). The evility counts in the table
above include this change.

## Evilities

| D2 ID | Name | D5 match | Verdict |
|---:|---|---:|---|
| 10 | Overlord's Dignity | 6001 | exact |
| 20 | Overlord's Pride | 2033 | lookalike |
| 30 | Sadistic | 6005 | exact |
| 31 | The Lone Majin | 164 | lookalike |
| 50 | Heart of Affection | 1083 | exact |
| 60 | Dangerous Revival | 6057 | adapted |
| 70 | Li'l Sis Aura | 6013 | adapted |
| 71 | ？？？？ | 70 | lookalike |
| 80 | The Artifact | 143 | lookalike |
| 81 | ？？？？？ | — | omit |
| 90 | Artifact Gone Wild | 6036 | lookalike |
| 100 | Passiveness | — | needs new code |
| 110 | Shut-in Officer | 62 | adapted |
| 120 | Artifact Collector | 1242 | lookalike |
| 130 | Fabricated Setting | 6058 | adapted |
| 140 | Limitless Love | 6053 | lookalike |
| 150 | Vocal Mind Control | 1242 | lookalike |
| 200 | Loyalty | 164 | lookalike |
| 210 | Never Give Up | 245 | exact |
| 220 | Obvious Tendency | 6104 | exact |
| 230 | Absorption | 6032 | exact |
| 240 | Tyrant's Slave | 6036 | adapted |
| 250 | Angel Glitter | 6040 | exact |
| 260 | Concentration | 1215 | adapted |
| 270 | All Range + | 1174 | exact |
| 280 | Final Boss Barrier | — | needs new code |
| 290 | Pringer Protection | — | needs new code |
| 300 | Heavy Stance | 1224 | adapted |
| 320 | Acceleration Mascot | 6024 | adapted |
| 330 | Greed | 2160 | lookalike |
| 340 | Dream Girl | 6044 | adapted |
| 350 | Final Boss Setting | 6048 | adapted |
| 400 | Burning Spirit | 2192 | lookalike |
| 410 | Princess Gleam | 6100 | lookalike |
| 411 | Lonely Overlord God | 164 | lookalike |
| 420 | Healing Barrier | 2110 | lookalike |
| 430 | Demon Experiments | — | needs new code |
| 440 | Love Thy Heart | 101 | lookalike |
| 450 | R Command | 2113 | lookalike |
| 451 | Command A | 1170 | adapted |
| 452 | Command G | 12 | adapted |
| 453 | Command J | 1170 | adapted |
| 454 | Command M | 6050 | lookalike |
| 455 | Command S | 1174 | lookalike |
| 460 | Most Badass Overlord | 6108 | lookalike |
| 470 | Prophecy | 1230 | lookalike |
| 471 | Prophecy of Judgment | 6112 | adapted |
| 472 | Prophecy of Godly Wind | 60 | lookalike |
| 473 | Prophecy of Poison | 2145 | lookalike |
| 474 | Prophecy of Wisdom | 6021 | lookalike |
| 475 | Prophecy of Anger | 4163 | lookalike |
| 480 | God of Destruction | 6112 | adapted |
| 490 | Water God's Ability | 4164 | lookalike |
| 500 | Miracle Ability | 6040 | lookalike |
| 510 | Witch Power | 1140 | adapted |
| 520 | Converge Attack | 2205 | lookalike |
| 521 | Mass Blaster | 1210 | adapted |
| 530 | Purify Power | 1220 | lookalike |
| 540 | Defensive Formation | — | needs new code |
| 550 | Flat Alliance | 4092 | lookalike |
| 560 | Gig Power Injection | 61 | adapted |
| 570 | Brave Battle Princess | 55 | lookalike |
| 580 | T．O． Analyze | 66 | lookalike |
| 581 | T．O． Force | 6127 | lookalike |
| 590 | T．O． Pandemic | 6092 | lookalike |
| 600 | T．O． Illusion | 6065 | lookalike |
| 610 | T．O． Legion | 2104 | lookalike |
| 620 | T．O． Disaster | 6112 | adapted |
| 630 | T．O． Despair | — | needs new code |
| 640 | Basic Principles | 2031 | lookalike |
| 1010 | Wild Cry | 1010 | adapted |
| 1011 | Shaking Excitement | 1014 | exact |
| 1012 | Fortitude | — | needs new code |
| 1020 | Assault Attack | 1020 | adapted |
| 1021 | Cooperativeness | 164 | adapted |
| 1022 | Extra Move | 1025 | lookalike |
| 1030 | Magic Bundle | 1030 | exact |
| 1031 | Over Limit | 1035 | exact |
| 1032 | Sorcery Depletion | 1030 | lookalike |
| 1040 | Magic Moderation | 1040 | exact |
| 1041 | Magic Creation | 2050 | exact |
| 1042 | Lights of Life | 1034 | exact |
| 1050 | Light Salvation | 1073 | adapted |
| 1051 | Light Protection | 1075 | exact |
| 1052 | Book of Magic | 1112 | lookalike |
| 1060 | Corpse Heal | — | needs new code |
| 1061 | Advanced Healing | 1085 | exact |
| 1062 | Book of Medicine | 1072 | adapted |
| 1070 | Fury | 1050 | exact |
| 1071 | Forced Counter | 1055 | exact |
| 1072 | No-miss Counter | 1063 | exact |
| 1080 | Double Counter | 6141 | lookalike |
| 1081 | Combo Attack | 4128 | lookalike |
| 1082 | Counter Dodge | 1065 | exact |
| 1090 | Finger of Luck | 1094 | adapted |
| 1091 | Gold Finger | 2162 | adapted |
| 1092 | Treasure Sensor | 1095 | lookalike |
| 1100 | Geo Resist | 2195 | lookalike |
| 1101 | Geo Protect | 2195 | exact |
| 1102 | Geo Damage | 2193 | adapted |
| 1110 | Acceleration Shot | 1200 | adapted |
| 1111 | Sniping | 1122 | adapted |
| 1112 | Fly Swatter | 1114 | exact |
| 1120 | Assist | 1120 | adapted |
| 1121 | Marking | 1125 | exact |
| 1122 | Second Attack | 1123 | exact |
| 1130 | Bushido | 1140 | adapted |
| 1131 | Calm Waters | 1114 | lookalike |
| 1132 | Duelist | 1144 | adapted |
| 1140 | Defender | 1150 | adapted |
| 1141 | Defensive Stance | 1155 | exact |
| 1142 | Advance Guard | 1152 | adapted |
| 1150 | Misty Haze | 1130 | lookalike |
| 1151 | Flame Haze | 6060 | adapted |
| 1152 | Mirage | 1130 | exact |
| 1160 | Element Force | — | needs new code |
| 1161 | Attack Weakness | 2185 | adapted |
| 1162 | Spirit Protection | 1164 | adapted |
| 1170 | Angel Song | 1170 | exact |
| 1171 | Angel Majesty | 1172 | exact |
| 1172 | Angel Leadership | 1175 | exact |
| 1180 | Angel Protection | 6025 | adapted |
| 1181 | Angel Love | 70 | lookalike |
| 1182 | Angel Group | 6053 | lookalike |
| 1190 | Dark Tamer | 6116 | adapted |
| 1191 | Dark Breeder | — | needs new code |
| 1192 | Dark Blood | 6088 | lookalike |
| 1200 | Cursed Dance | 6090 | exact |
| 1201 | Gloomy Dance | 6093 | adapted |
| 1202 | Ominous Dance | 2152 | lookalike |
| 1210 | Rapid Movement | 1060 | adapted |
| 1211 | Blazing Start | 1024 | adapted |
| 1212 | Heart of a Flea | 1143 | lookalike |
| 2010 | Gel Body | 2060 | exact |
| 2011 | Poisonous Body | 2063 | lookalike |
| 2012 | Corrupting Body | — | needs new code |
| 2020 | Bad Trip | 4058 | adapted |
| 2021 | Registry Reverse | 2113 | lookalike |
| 2022 | Reverse Ailment | — | needs new code |
| 2030 | Poison Accelerator | 2083 | exact |
| 2031 | Full of Miasma | 2013 | lookalike |
| 2032 | Lengthen Ailment | 2084 | exact |
| 2040 | Reversal Power | 6146 | adapted |
| 2041 | Tense Crisis | 6146 | adapted |
| 2042 | Crisis Energy | 6146 | adapted |
| 2050 | Mind Break | 2055 | adapted |
| 2051 | Generate Pain | 2053 | exact |
| 2052 | Astral Body | 6055 | adapted |
| 2060 | Weakening Aura | 2070 | exact |
| 2061 | Softening Aura | 2072 | adapted |
| 2062 | Cute Aura | 2103 | adapted |
| 2070 | Explosive Body | 2010 | adapted |
| 2071 | Bomb Conversion | — | needs new code |
| 2072 | Cross Bomber | 2012 | adapted |
| 2080 | Predation | 2090 | adapted |
| 2081 | Soul Eater | 1103 | lookalike |
| 2082 | Corpse Cannibal | 2095 | exact |
| 2090 | Stone Statue | — | needs new code |
| 2091 | Turbo Boost | 2213 | exact |
| 2092 | Home Security | 6099 | adapted |
| 2100 | Sweet Aroma | 2110 | adapted |
| 2101 | Detox Aroma | 2110 | lookalike |
| 2102 | Shielding Aroma | 2115 | adapted |
| 2110 | Boar Charge | 2035 | exact |
| 2111 | Great Wall | — | needs new code |
| 2112 | Dead or Alive | 1233 | lookalike |
| 2120 | Soul Reaper | 4130 | lookalike |
| 2121 | Mind Reaper | 4130 | lookalike |
| 2122 | Near Death Reaper | — | needs new code |
| 2130 | Patience | 1114 | lookalike |
| 2131 | Combat High | 2215 | adapted |
| 2132 | Flames of Revenge | 170 | lookalike |
| 2140 | Sexy Aura | 2140 | exact |
| 2141 | Punishment | 2191 | adapted |
| 2142 | Shame | 2144 | exact |
| 2150 | Lovely Song | 2130 | exact |
| 2151 | Happy Song | 2131 | exact |
| 2152 | Fortune Song | 2132 | exact |
| 2160 | Enemy on the Brink | 2120 | adapted |
| 2161 | Fighting Spirit | 1225 | adapted |
| 2162 | Cat on the Brink | 2120 | adapted |
| 2170 | Power Shot | 4050 | adapted |
| 2171 | Piercing Shot | 4051 | exact |
| 2172 | Scatter Shot | 2121 | adapted |
| 2180 | Water Dominance | 4016 | exact |
| 2181 | Cold Charge | — | needs new code |
| 2182 | Aqua Depression | 4013 | adapted |
| 2190 | Fire Dominance | 4014 | lookalike |
| 2191 | Flame Charge | 2034 | lookalike |
| 2192 | Heat Depression | 2134 | lookalike |
| 2200 | Wind Dominance | 4015 | lookalike |
| 2201 | Air Charge | 2034 | lookalike |
| 2202 | Wind Depression | 6090 | lookalike |
| 3000 | Kind Shop | 1223 | lookalike |
| 3001 | Together Shop | 3003 | lookalike |
| 3002 | Secret Training Shop | 3008 | lookalike |
| 3003 | Scratch Noise Shop | 1121 | lookalike |
| 3004 | Ha！ Shop | — | needs new code |
| 3005 | Autumn Feast Shop | 3019 | lookalike |
| 3006 | Banish Shop | 3013 | lookalike |
| 3007 | Distracting Sweet Shop | 4124 | adapted |
| 3008 | Mana Thief Shop | 3016 | adapted |
| 3009 | Thief Shop | 1095 | lookalike |
| 3010 | Dig Shop | — | needs new code |
| 3011 | Dirty Things Shop | — | needs new code |
| 3012 | Brushing Shop | 3002 | lookalike |
| 3013 | Feel Around Shop | — | needs new code |
| 3014 | Don't Hit Shop | 1223 | lookalike |
| 3015 | Rally Shop | 3003 | adapted |
| 3016 | Sun Tanning Shop | — | needs new code |
| 3017 | Head Slap Shop | — | needs new code |
| 3018 | Kindness Shop | 6013 | lookalike |
| 3019 | Sumo Shop | 6088 | adapted |
| 3020 | Fortune Shop | 1063 | lookalike |
| 3021 | Slimy Shop | 2100 | adapted |
| 3022 | Snack Prize Shop | 4122 | adapted |
| 3023 | Transform Shop | — | needs new code |
| 3024 | Deathblow Shop | 6052 | lookalike |
| 3025 | Requiem Shop | 4130 | lookalike |
| 3026 | Dark Heart | 10 | lookalike |
| 3027 | Dengeki Shop | 6023 | lookalike |
| 3028 | Famitsu Shop | 2132 | lookalike |
| 3029 | Love Love Shop | — | needs new code |
| 3030 | Tsuntsun Shop | — | needs new code |
| 3031 | Revival! Shop | 145 | lookalike |
| 3032 | Fold Shop | — | needs new code |
| 3033 | Energy Shop | 3010 | adapted |

## Skills

| D2 ID | Name | Category | D5 match | Verdict |
|---:|---|---|---:|---|
| 0 | グレートバイス |  | — | omit |
| 1 | Physical |  | 1 | exact |
| 2 | Magic |  | 2 | exact |
| 101 | Triple Strike | Fist | 101 | exact |
| 102 | Double Shadow | Fist | 102 | adapted |
| 103 | Side Out | Fist | 103 | adapted |
| 104 | Beast King | Fist | 104 | adapted |
| 105 | Aura Punch | Fist | 105 | adapted |
| 106 | Big Bang | Fist | 106 | exact |
| 107 | Quintuple Shot | Fist | 107 | adapted |
| 108 | No Way Out | Fist | 107 | lookalike |
| 201 | Blade Rush | Sword | 201 | exact |
| 202 | Hurricane Slash | Sword | 202 | adapted |
| 203 | Nether Slash | Sword | 203 | adapted |
| 204 | Musou Slash | Sword | 204 | adapted |
| 205 | Dark Night Slash | Sword | 205 | adapted |
| 206 | Dimension Slash | Sword | 206 | adapted |
| 207 | Sky Nine Slash | Sword | 207 | adapted |
| 208 | Super Crosslord | Sword | 207 | lookalike |
| 301 | Impaler Drop | Spear | 301 | exact |
| 302 | Blade Bash | Spear | 302 | adapted |
| 303 | Spear Blow | Spear | 303 | adapted |
| 304 | Trident | Spear | 304 | adapted |
| 305 | Dimension Rush | Spear | 305 | adapted |
| 306 | Magic Comet Slash | Spear | 306 | adapted |
| 307 | Baker's Dozen Slash | Spear | 307 | adapted |
| 308 | Meteor Sky Spear | Spear | 307 | lookalike |
| 401 | Dark Spica | Bow | 401 | adapted |
| 402 | Bright Canopus | Bow | 402 | adapted |
| 403 | Meteor Arrow Kick | Bow | 403 | adapted |
| 404 | Zielregen | Bow | 404 | adapted |
| 405 | Doppelganger | Bow | 405 | adapted |
| 406 | Galaxy Comet | Bow | 406 | exact |
| 407 | Psycho Mirage | Bow | 407 | adapted |
| 408 | Fire Dragon Gods | Bow | 407 | lookalike |
| 501 | Tri-Burst | Gun | 501 | exact |
| 502 | Dark Matter Ether | Gun | 502 | adapted |
| 503 | Point Blank Blaster | Gun | 503 | adapted |
| 504 | Icicle Sword Gun | Gun | 504 | adapted |
| 505 | Rapid Launcher | Gun | 505 | adapted |
| 506 | Patriot | Gun | 506 | adapted |
| 507 | Double Hell Limbo | Gun | 507 | adapted |
| 508 | Cocytus | Gun | 507 | lookalike |
| 601 | Boulder Crush | Axe | 601 | exact |
| 602 | Helmet Crush | Axe | 602 | adapted |
| 603 | Super Back Spin | Axe | 603 | adapted |
| 604 | Axe Wheel WMD | Axe | 604 | adapted |
| 605 | Dynamic Moon | Axe | 605 | adapted |
| 606 | Plasma Sky  | Axe | 606 | adapted |
| 607 | Netherworld Loop | Axe | 607 | adapted |
| 608 | El Diablo | Axe | 607 | lookalike |
| 801 | 451 Degrees of Evil | Book | — | omit |
| 802 | Gone with the Cotton | Book | — | omit |
| 803 | Sea Horse Charge | Book | — | omit |
| 804 | Baskervilles | Book | — | omit |
| 805 | Nether News | Book | — | omit |
| 806 | Sea Dragon Bang | Book | — | omit |
| 807 | Cat Tale | Book | — | omit |
| 808 | NIS Certification | Book | — | omit |
| 899 | Dengeki Guide | Book | — | omit |
| 911 | Piggyback |  | 911 | lookalike |
| 931 | Create Box | Unique | 11091 | exact |
| 932 | Create Barrel | Unique | 11092 | exact |
| 933 | Stealing Hand | Unique | 5 | lookalike |
| 951 | Tower Fall | Tower | 951 | adapted |
| 952 | Tower Hamster | Tower | 952 | adapted |
| 953 | Tower Dunk | Tower | 953 | adapted |
| 955 | Tower Move | Tower | 955 | exact |
| 956 | Tower Lift | Tower | 956 | exact |
| 957 | Tower Throw | Tower | 957 | exact |
| 958 | Tower Break | Tower | 953 | lookalike |
| 1001 | Fire | Magic | 1001 | exact |
| 1002 | Mega Fire | Magic | 1002 | exact |
| 1003 | Giga Fire | Magic | 1003 | exact |
| 1004 | Omega Fire | Magic | 1004 | exact |
| 1005 | Tera Fire | Magic | 1005 | exact |
| 1101 | Wind | Magic | 1101 | exact |
| 1102 | Mega Wind | Magic | 1102 | exact |
| 1103 | Giga Wind | Magic | 1103 | exact |
| 1104 | Omega Wind | Magic | 1104 | exact |
| 1105 | Tera Wind | Magic | 1105 | exact |
| 1201 | Ice | Magic | 1201 | exact |
| 1202 | Mega Ice | Magic | 1202 | exact |
| 1203 | Giga Ice | Magic | 1203 | exact |
| 1204 | Omega Ice | Magic | 1204 | exact |
| 1205 | Tera Ice | Magic | 1205 | exact |
| 1301 | Star | Magic | 1301 | exact |
| 1302 | Mega Star | Magic | 1302 | exact |
| 1303 | Giga Star | Magic | 1303 | exact |
| 1304 | Omega Star | Magic | 1304 | exact |
| 1305 | Tera Star | Magic | 1305 | exact |
| 1401 | Heal | Heal | 1401 | exact |
| 1402 | Mega Heal | Heal | 1402 | exact |
| 1403 | Giga Heal | Heal | 1403 | exact |
| 1404 | Omega Heal | Heal | 1404 | exact |
| 1405 | Tera Heal | Heal | 1405 | exact |
| 1499 | Espoir | Heal | 1499 | exact |
| 1501 | Braveheart | Support | 1501 | exact |
| 1502 | Shield | Support | 1502 | exact |
| 1503 | Magic Boost | Support | 1503 | exact |
| 1504 | Magic Wall | Support | 1504 | exact |
| 1505 | Target Lock | Support | 1505 | exact |
| 1506 | Speed Boost | Support | 1506 | exact |
| 1601 | Enfeeble | Support | 1601 | exact |
| 1602 | Armor Break | Support | 1602 | exact |
| 1603 | Mind Break | Support | 1603 | exact |
| 1604 | Resist Break | Support | 1604 | exact |
| 1605 | Blind | Support | 1605 | exact |
| 1606 | Slow | Support | 1606 | exact |
| 1701 | Poison | Support | 1701 | exact |
| 1702 | Slumber | Support | 1702 | exact |
| 1703 | Stun | Support | 1703 | exact |
| 1704 | Blank Out | Support | 1704 | exact |
| 1705 | Charm | Support | 1705 | exact |
| 1801 | Move！ | Unique | — | omit |
| 1802 | Jump Jump Jump！ | Unique | — | omit |
| 1803 | Counter Classes | Unique | — | omit |
| 1804 | Throw！ | Unique | 918 | exact |
| 1805 | Magic Extender | Unique | — | omit |
| 1806 | Only Crits！ | Unique | — | omit |
| 1901 | Geo Change | Unique | 1901 | exact |
| 1903 | Bonus Change | Unique | 1903 | exact |
| 1904 | Bonus Blast | Unique | 1904 | exact |
| 1905 | Geo Blast | Unique | 1905 | exact |
| 1906 | Geo Warp | Unique | 1906 | exact |
| 2001 | Fire Charge | Unique | — | omit |
| 2002 | Wind Charge | Unique | — | omit |
| 2003 | Ice Charge | Unique | — | omit |
| 2004 | Anti-Fire | Unique | — | omit |
| 2005 | Anti-Wind | Unique | — | omit |
| 2006 | Anti-Ice | Unique | — | omit |
| 2101 | Wild Groom | Unique | — | omit |
| 2102 | Dominate | Unique | — | omit |
| 9001 | Dummy | Magic | 9001 | exact |
| 9002 | Mega Dummy | Magic | 9002 | exact |
| 9003 | Giga Dummy | Magic | 9003 | exact |
| 9004 | Omega Dummy | Magic | 9004 | exact |
| 9005 | Tera Dummy | Magic | 9005 | exact |
| 10011 | Blazing Knuckle | Unique | 10511 | exact |
| 10012 | Overlord's Wrath | Unique | 10512 | exact |
| 10013 | Meteor Impact EX | Unique | 30511 | lookalike |
| 10014 | Overlord Dimension | Unique | 10514 | exact |
| 10021 | Blazing Knuckle | Unique | 10511 | exact |
| 10022 | Overlord's Wrath | Unique | 10512 | exact |
| 10023 | Meteor Impact EX | Unique | 30521 | lookalike |
| 10024 | Overlord Dimension | Unique | 10514 | exact |
| 10031 | Prinny Raid | Unique | 10521 | exact |
| 10032 | Sexy Beam | Unique | 10522 | exact |
| 10033 | Chaos Impact | Unique | 10523 | exact |
| 10034 | Dead End Chaos | Unique | 10524 | exact |
| 10051 | Love Knuckle！ | Unique | 10531 | exact |
| 10052 | Holy Arrows | Unique | 10532 | exact |
| 10053 | Flonnezilla | Unique | 10533 | exact |
| 10061 | Extreme Love！ | Unique | 10532 | adapted |
| 10071 | Angelic Knuckle | Unique | 10541 | exact |
| 10072 | Prinny Parade | Unique | 10542 | exact |
| 10073 | Sicily Delivery | Unique | 10543 | exact |
| 10081 | Dark Phenomenon | Unique | 10111 | adapted |
| 10082 | Chaos Divider | Unique | 10811 | adapted |
| 10091 | Dimension Chaos | Unique | 10811 | lookalike |
| 10101 | Order： Prediction | Unique | 10131 | lookalike |
| 10102 | Order： Chaos Dance | Unique | 10132 | lookalike |
| 10111 | Fall ＆ Press | Unique | 11151 | lookalike |
| 10112 | Grosso Booster | Unique | 11152 | lookalike |
| 10113 | Guardian Cannon | Unique | 11152 | lookalike |
| 10121 | Tentacle of Death | Unique | 12131 | lookalike |
| 10122 | Frozen Lake | Unique | 12132 | lookalike |
| 10123 | Dam Breaker | Unique | 12133 | lookalike |
| 10131 | Spinning Shock | Unique | 12031 | lookalike |
| 10132 | Gaea's Pulse | Unique | 12032 | lookalike |
| 10133 | Ultimate Form | Unique | 12033 | lookalike |
| 10141 | Angels Unite！ | Unique | 11171 | lookalike |
| 10201 | Darkness Slash+ | Unique | 12162 | exact |
| 10202 | Sand Wyrm Impact+ | Unique | 12164 | exact |
| 10203 | Boulder Shot+ | Unique | 12163 | exact |
| 10211 | Cameo Storm | Unique | 11231 | exact |
| 10212 | Dead Hurdles | Unique | 11232 | exact |
| 10213 | Asagi Chachacha | Unique | 10243 | adapted |
| 10221 | My Autograph！ | Unique | 10721 | adapted |
| 10222 | Aching Heart | Unique | 10722 | exact |
| 10223 | Love Dynamite T | Unique | 10723 | exact |
| 10231 | Impaler Prince | Unique | 10571 | exact |
| 10232 | Bloody Hole | Unique | 10572 | exact |
| 10233 | Tyrant Flughude | Unique | 10573 | exact |
| 10241 | Swift Power Shot | Unique | 10581 | exact |
| 10242 | Dual Canine Attack | Unique | 10582 | exact |
| 10243 | Vanargandr | Unique | 10583 | exact |
| 10251 | Mandatory Repo | Unique | 10591 | exact |
| 10252 | Angelic Whip | Unique | 10592 | exact |
| 10253 | Goddess Artemis | Unique | 10593 | exact |
| 10261 | Soul Reaper | Unique | 10621 | exact |
| 10262 | Grim Specter | Unique | 10622 | exact |
| 10263 | Delta of Death | Unique | 10623 | exact |
| 10271 | Azure Prison | Unique | 10751 | exact |
| 10272 | Petta Beam | Unique | 10752 | exact |
| 10273 | Time Drive | Unique | 10753 | exact |
| 10281 | Evilman Slash | Unique | 12611 | exact |
| 10282 | Evilman Death Rave | Unique | 12612 | exact |
| 10283 | Evilman Gigantus Ray | Unique | 10633 | lookalike |
| 10291 | Prinny Rush | Unique | 12011 | lookalike |
| 10292 | Prinny Destroy | Unique | 12012 | lookalike |
| 10293 | Pringer Beam X | Unique | 12013 | lookalike |
| 10321 | Decide Shooter | Unique | 10561 | lookalike |
| 10322 | Usagi of the Dead | Unique | 10562 | exact |
| 10323 | Usagi Slash | Unique | 10563 | exact |
| 10331 | Darkness Slash | Unique | 12162 | exact |
| 10332 | Sand Wyrm Impact | Unique | 12164 | exact |
| 10333 | Boulder Shot | Unique | 12163 | exact |
| 10341 | Bomb-Bat-Stic | Unique | 10601 | exact |
| 10342 | Glorious Hitter | Unique | 10602 | exact |
| 10343 | Prinny Kaiser XX | Unique | 10603 | exact |
| 10351 | Final Boss Arises | Unique | 10611 | exact |
| 10352 | True Godly Weapon | Unique | 10612 | exact |
| 10353 | Yog Sothoth | Unique | 10613 | exact |
| 10401 | Crimson Flame | Unique | 10701 | exact |
| 10402 | Combo Explosion Shot | Unique | 10702 | adapted |
| 10403 | Vulcan Blaze | Unique | 10703 | exact |
| 10411 | Rose Thorns | Unique | 10711 | exact |
| 10412 | Crest Roses | Unique | 10712 | exact |
| 10413 | Rose Liberation | Unique | 10713 | exact |
| 10421 | You've Been Chosen | Unique | 11171 | lookalike |
| 10422 | Li'l's Divine Skill！ | Unique | 11172 | lookalike |
| 10423 | All-World Angel Solo | Unique | 11172 | lookalike |
| 10431 | Blast Finger | Unique | 10641 | exact |
| 10432 | Shine Beam | Unique | 10642 | exact |
| 10433 | Vasa Aergun | Unique | 10643 | exact |
| 10441 | D-Rule： Recycle | Unique | 10651 | exact |
| 10442 | D-Rule： Volunteer | Unique | 10652 | exact |
| 10443 | D-Rule： EcoFriend | Unique | 10653 | exact |
| 10451 | Short Recoil | Unique | 10661 | exact |
| 10452 | The Great Gun King | Unique | 10662 | exact |
| 10453 | Magnificent World | Unique | 10663 | exact |
| 10461 | Zetta Beam Spread | Unique | 10731 | adapted |
| 10462 | Badass Overdrive | Unique | 10732 | exact |
| 10463 | One-Inch Zetta Beam | Unique | 30731 | lookalike |
| 10471 | Freezing Prophecy | Unique | 10741 | exact |
| 10472 | Ice Mirror | Unique | 10742 | exact |
| 10473 | Frost Dragon | Unique | 10743 | exact |
| 10474 | 裁きの予言 |  | — | omit |
| 10481 | Lightning Vortex | Unique | 10731 | lookalike |
| 10482 | Vanishing Bolt | Unique | 10732 | lookalike |
| 10483 | God Thunder！ | Unique | 30731 | lookalike |
| 10484 | 破壊神 |  | — | omit |
| 10491 | Water Dragon Stroke | Unique | 12131 | lookalike |
| 10492 | Eccarlate Blast | Unique | 12132 | lookalike |
| 10493 | Super Rising Dragon | Unique | 12133 | lookalike |
| 10501 | Protect Everyone！ | Unique | 10532 | lookalike |
| 10502 | P Burgundy | Unique | 10533 | lookalike |
| 10503 | Meteo Helianthus | Unique | 30511 | lookalike |
| 10504 | Confine | Unique | 10531 | lookalike |
| 10511 | Metal Justice | Unique | 10691 | exact |
| 10512 | Twilight Elemental | Unique | 10692 | exact |
| 10513 | World is Metallia's | Unique | 10693 | exact |
| 10521 | Atomic Super Blast | Unique | 10761 | exact |
| 10522 | Holy Breaker | Unique | 10762 | exact |
| 10523 | Requiem Aeternam Fin | Unique | 10763 | adapted |
| 10531 | Kimi ni Todoke | Unique | 10761 | lookalike |
| 10532 | Sky High Cavalier | Unique | 10762 | lookalike |
| 10533 | Foret de Cercle | Unique | 10763 | lookalike |
| 10541 | Royal Paprika Rondo | Unique | 10681 | lookalike |
| 10542 | Metal Vierge | Unique | 10682 | lookalike |
| 10543 | Blanc et Noire | Unique | 10683 | lookalike |
| 10551 | Justice Knife | Unique | 10631 | exact |
| 10552 | V Extreme | Unique | 10632 | exact |
| 10553 | N I S A | Unique | 10633 | exact |
| 10561 | Witness My Power！ | Unique | 10731 | lookalike |
| 10562 | Another Dimension | Unique | 10732 | lookalike |
| 10563 | Now， Do it！ | Unique | 30731 | lookalike |
| 10571 | Bringing the Boom！ | Unique | 10601 | lookalike |
| 10572 | Super Duper Slash☆ | Unique | 10602 | lookalike |
| 10573 | Princess Battle Move | Unique | 10603 | lookalike |
| 10581 | Sword Rain | Unique | 10771 | exact |
| 10582 | Grand Sword | Unique | 10772 | exact |
| 10583 | Grave Eternity | Unique | 10773 | exact |
| 10585 | 超魔王フォース |  | 10772 | lookalike |
| 10586 | 超魔王ディザスター |  | 10773 | lookalike |
| 12011 | Slime Stomp | Unique | 12061 | adapted |
| 12012 | Slime Cheer | Unique | 12063 | exact |
| 12013 | Roslimebo | Unique | 12063 | adapted |
| 12014 | Muscle Slime | Unique | 12064 | adapted |
| 12021 | Jack Knife Jr． | Unique | 12051 | lookalike |
| 12022 | Daniel Jacks | Unique | 12052 | lookalike |
| 12023 | Jack the Ripper | Unique | 12053 | lookalike |
| 12024 | Jackpot 4 U | Unique | 12054 | lookalike |
| 12031 | Super Hypno Ray | Unique | 12081 | lookalike |
| 12032 | Mothsplosion | Unique | 12082 | lookalike |
| 12033 | Moth Field | Unique | 12083 | lookalike |
| 12034 | Sunset Lullaby | Unique | 12084 | lookalike |
| 12041 | Nether Lariat | Unique | 12031 | lookalike |
| 12042 | Pile Bunker | Unique | 12032 | lookalike |
| 12043 | Gaea's Lament | Unique | 12033 | lookalike |
| 12044 | Macho Golem | Unique | 12034 | lookalike |
| 12051 | Ignis Fatuus | Unique | 12091 | lookalike |
| 12052 | Micro Bomber | Unique | 12092 | lookalike |
| 12053 | Puppet Show | Unique | 12093 | lookalike |
| 12054 | Never Forget You | Unique | 12094 | lookalike |
| 12061 | Cat Setaria | Unique | 12071 | adapted |
| 12062 | Burning Hairball | Unique | 12072 | adapted |
| 12063 | Combo Attack | Unique | 12073 | adapted |
| 12064 | Blue Sky Surfer | Unique | 12074 | adapted |
| 12071 | Prinny Blizzard | Unique | 12011 | adapted |
| 12072 | Prinny Dance | Unique | 12012 | adapted |
| 12073 | Thousand Prinnies | Unique | 12013 | adapted |
| 12074 | Code Prinny | Unique | 12014 | adapted |
| 12081 | Poison Bite | Unique | 12091 | lookalike |
| 12082 | Rotten Headbutt | Unique | 12091 | exact |
| 12083 | Zombie Tornado | Unique | 12092 | exact |
| 12084 | Zombeam | Unique | 12094 | lookalike |
| 12091 | Flying Press | Unique | 12081 | lookalike |
| 12092 | Boltech Booster | Unique | 12082 | lookalike |
| 12093 | Electric Shot | Unique | 12083 | lookalike |
| 12094 | Galleon Cannon | Unique | 12084 | lookalike |
| 12101 | Petal Cutter V | Unique | 12111 | lookalike |
| 12102 | Solar Satellite | Unique | 12112 | lookalike |
| 12103 | Scatter Thorn Shot | Unique | 12113 | lookalike |
| 12104 | Thorn Drill Blast | Unique | 12114 | lookalike |
| 12111 | Darkness Slash | Unique | 12162 | exact |
| 12112 | Spinning Wind Blast | Unique | 12032 | lookalike |
| 12113 | Sand Wyrm Impact | Unique | 12164 | exact |
| 12114 | Boulder Shot | Unique | 12163 | exact |
| 12121 | Shadow Nail | Unique | 12091 | lookalike |
| 12122 | Circle of Death | Unique | 12092 | lookalike |
| 12123 | Soul Collector | Unique | 12093 | lookalike |
| 12124 | Testament | Unique | 12094 | lookalike |
| 12131 | Lightning God Slash | Unique | 11151 | lookalike |
| 12132 | Gigantic | Unique | 11152 | lookalike |
| 12133 | Black Death Circle | Unique | 11152 | lookalike |
| 12134 | Gallatin Sword | Unique | 11152 | lookalike |
| 12141 | Diving Heart | Unique | 12141 | adapted |
| 12142 | Pink Illusion | Unique | 12142 | adapted |
| 12143 | Nightmare Carnival | Unique | 12143 | adapted |
| 12144 | Twin Attribute | Unique | 22142 | exact |
| 12151 | Water Guillotine | Unique | 12132 | exact |
| 12152 | Tentacle | Unique | 12132 | adapted |
| 12153 | Ice Wall Circle | Unique | 12133 | adapted |
| 12154 | Dam Maker | Unique | 12134 | adapted |
| 12161 | Cat Dance | Unique | 12121 | lookalike |
| 12162 | Cat Punch Rush | Unique | 12122 | lookalike |
| 12163 | Cat Wave Z | Unique | 12123 | lookalike |
| 12164 | Hundred Cat Fist | Unique | 12124 | lookalike |
| 12171 | Ignition | Unique | 11121 | lookalike |
| 12172 | Bastard Black | Unique | 11122 | lookalike |
| 12173 | Catapult | Unique | 11122 | lookalike |
| 12174 | Rifle Death | Unique | 11122 | lookalike |
| 12181 | Shark Slider | Unique | 12131 | adapted |
| 12182 | Lorenzini | Unique | 12132 | adapted |
| 12183 | Shark Whirlpool | Unique | 12133 | adapted |
| 12184 | Sea Dome Tragedy | Unique | 12134 | adapted |
| 12191 | Burning Grapple | Unique | 12181 | lookalike |
| 12192 | Hydro Drake | Unique | 12182 | lookalike |
| 12193 | Mandala Volcano | Unique | 12183 | lookalike |
| 12194 | God of Flame | Unique | 12184 | lookalike |
| 12201 | Dragon Hornet | Unique | 12181 | lookalike |
| 12202 | Way of Dragon | Unique | 12182 | lookalike |
| 12203 | Dragon Lightning | Unique | 12183 | lookalike |
| 12204 | Evil Dragon Play | Unique | 12184 | lookalike |
| 30111 | Checkmate | Mounted | — | omit |
| 30121 | Along with Rainier | Mounted | — | omit |
| 30131 | Godly Machine | Mounted | — | omit |
| 30201 | Twilight Bomber+ | Mounted | — | omit |
| 30331 | Twilight Bomber | Mounted | — | omit |
| 30351 | Death Co． | Mounted | — | omit |
| 30441 | Ultimate Bookmark | Mounted | — | omit |
| 30511 | Witch Domination | Mounted | — | omit |
| 30521 | Light and Darkness | Mounted | — | omit |
| 30561 | Murder Play | Mounted | — | omit |
| 32011 | Slime Hand | Mounted | — | omit |
| 32021 | Jumpin' Jack | Mounted | — | omit |
| 32031 | Moth Tornado | Mounted | — | omit |
| 32041 | Lantern Wish | Mounted | — | omit |
| 32051 | Dimension Eater X | Mounted | — | omit |
| 32061 | Cat Umbrella Spin | Mounted | — | omit |
| 32071 | Bomber Dood | Mounted | — | omit |
| 32081 | DIEmension Slash | Mounted | — | omit |
| 32091 | Granite Checkmate | Mounted | — | omit |
| 32101 | Moon Spike | Mounted | — | omit |
| 32111 | Twilight Bomber | Mounted | — | omit |
| 32121 | Death March | Mounted | — | omit |
| 32131 | X-Slash | Mounted | — | omit |
| 32141 | Judgment Rendezvous | Mounted | — | omit |
| 32151 | With a Sea Angel！ | Mounted | — | omit |
| 32161 | Lovely Paw Fist | Mounted | — | omit |
| 32171 | Space Galaxy Kick | Mounted | — | omit |
| 32181 | Jaws Wave Rider | Mounted | — | omit |
| 32191 | Fire Moon Drop Shot | Mounted | — | omit |
| 32201 | Sky High Dragoon | Mounted | — | omit |

## Sources
D5 Mod Kit development fork, Disgaea D2 source-pack coverage review (`coverage.json`, 2026-10); reader code not
published; its results are the tables on this page. Table layouts: [data.md](data.md).
