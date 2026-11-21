# Disgaea 5 maps (`mapNNNNN.lzs`: .hex, .chr, .obf, .gop)

**Used by:** Disgaea 5 Complete PC (`Data/Map/Map_{JP,EN,FR}.dat`, 448 maps in the JP base set). Each map is NIS LZS
(tag `pac`) around a **pac** of four members, always in this order: `.hex` (terrain), `.chr` (units), `.obf` (3D mesh),
`.gop` (Geo panels). The game builds names with `map%05d.%s`.

**Evidence:** **Community-reported** from D5 Mod Kit's [MAPS_AND_OBJECTS.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/MAPS_AND_OBJECTS.md).
The Kit marks fields [H] (checked against the loader or all files), [M] (consistent evidence) or [L] (guess); kept as
*Conf.* below. The Kit rewrites all four members byte for byte.

## pac container
`u32 count`, 12 pad bytes, then `count × {u32 offset, char name[28]}`; offsets are relative to the end of the table.
Members are padded to 16 bytes.

## `.hex` terrain (loader `0x1402272D0`)

| Offset | Type | Field | Conf. |
|---|---|---|---|
| 0x00 | u32 | Version (all shipped files: 6; the loader also takes 3, 4, 5, 7) | H |
| 0x04 | u32 | Width W (≤ 128) | H |
| 0x08 | u32 | Height H | H |
| 0x0C | i32[5] | Motion indices for terrain objects 0–4 (−1 = none) | H |
| 0x20 | 0xD0 bytes | Lighting and camera block | M |
| 0xF0 | f32[(W+1)(H+1)][4] | Four corner heights per panel (negative = up); last row and column are padding | H |
| then | u8[128][128] | Unknown flags (22 non-zero cells in the whole game) | L |
| +0x4000 | u8[128][128] | **No panel**: 1 = hole or outside; movement and placement reject these | H |

1 panel = 12 mesh units; one height step = 6 units.

## `.chr` units

Header `u16 version (1), u16 count, 12 zero bytes`; records of **592 bytes** (four demo maps use an older 436-byte
record). The engine reads at most **255** units.

| Offset | Type | Field | Conf. |
|---|---|---|---|
| 0x000 | u32 | Slot (unit number) | M |
| 0x004 | u32 | `char.dat` ID: classes; 4810/4811 Geo Symbol; 5000–5299 objects (boxes, bombs, pillars, treasure, level spheres); 5260/5261 item symbols; 5400+ NPCs | H |
| 0x008 | u16 | Level | H |
| 0x00A | u16 | Facing in degrees (0 = +y, 90 = +x …) | M/H |
| 0x00C / 0x00E | i16 | Panel x / y (x = −1: not on the map at start) | H |
| 0x010 | u8 | Spawn group: 255 or 0 = on the field; 1–127 = reinforcements an event calls in | H |
| 0x012 | u16 | Repeat count (0 = never placed) | H |
| 0x014 | u16 | Colour variant (0xFFFF default) | L |
| 0x018 | u32[5] | Equipment (`item.dat` IDs; [0] weapon) | H |
| 0x02C | u16[5] | Item levels | H |
| 0x038 | u32[20] | Evilities (`Skill.dat` IDs) | H |
| 0x088 | u32[20] | Skills (`magic.dat` IDs) | H |
| 0x0D8 | u16[20] | Skill levels | M |
| 0x150 | u16[10] | Weapon mastery by weapon type | M |
| 0x164 | i16[8] | Stat modifiers, signed % | M |
| 0x175 | u8 | Team: 1 enemy; 2 neutral (objects, Geo Symbols, spectators); 3–5 extra armies | M |
| 0x178 | i32 | AI pattern (101–116; −1 objects) | M |
| 0x17C | u8 | **Appears**: 0 every visit; 1 only until the stage is cleared; 2 only after | H |
| 0x17D | 199 bytes | AI rules: kind byte (255 = none) + 4 × u16, often a trigger rectangle | M |
| 0x248 | u32 | Geo Symbol colour (panel codes; 0 = clear) | M |
| 0x24C | u32 | Geo Symbol effect (`GE.dat` ID: 1 Damage, 2 Recovery, 3 EXP, 12 Enemy Boost, 16 No Entry, 20 Invincible, 21 Move, 61 Game Over …) | H |

One map holds both the first-visit and the cleared sets of units, told apart by **Appears**.

## `.gop` Geo panels (49,168 bytes)

| Offset | Type | Field |
|---|---|---|
| 0x0000 | u32 | Live entry count |
| 0x0004 | u8[16384] | x |
| 0x4004 | u8[16384] | y |
| 0x8004 | u8[16384] | Colour: 1 red, 2 green, 3 blue, 4 yellow, 5 purple, 6 cyan (order from the executable's texture tables; [M]) |

Entries past the count can hold stale editor data.

## Stages (`dungeon.dat`)

223 rows of 336 bytes. Key fields: `0x04` map ID (101 → `map00101`); `0x10` Netherworld skill
(`MakaiSkillDataBase` ID, e.g. 1003 Snow, 2000 Sea of Poison); `0x16` the story flag that lists the stage
(5000 + 10 × episode + stage); `0x18` the level the stage list shows; `0x1D` which list (story / extra / Carnage);
`0x36`/`0x37` base panel x/y. Every stage also needs a `DemoCheckDataBase.dat` row, or the stage list **closes the game**
([D5 modifiable](../games/mainline/disgaea-5-alliance-of-vengeance/modifiable.md)). Saves keep 400 stage slots.

## Tools
D5 Mod Kit (`d5lib/mapfile.py`, Stages page with a 3D map editor).
