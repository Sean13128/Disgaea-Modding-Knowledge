# Disgaea 4 — Engine Functions

**Executable:** `Disgaea4pc.exe` (Complete+ PC, Steam build 6150765), x64, image base `0x140000000`.
SHA-256 `2436d4b39aff4291dee71b65055a11161e7ec9186ff98cc2deebc4411614c765`. All addresses are for this build.

**Evidence:** **From decomp** (static analysis in this project's D4 → D5 porting research, 2026-10, with Ghidra and
Capstone); rows marked **Verified offline** were also run in an emulator ([method](../../../guides/decompile-setup.md#checking-a-finding-by-running-the-original-code))). Not tested in the running game.

## What's mapped

D4 has about 19,900 functions; about 10% carry names, almost all of them compiler and C++ library code. Function
matching against D5 links about 3,400 D4 functions to D5 ones, about 2,300 of them closely. So D5's
[function map](../disgaea-5-alliance-of-vengeance/functions.md) is the best guide to D4's engine, but every match
still needs checking. The function index itself is private (derived from the game), not in this repo.

## Evilities and damage

Traced while porting the evility **Aegis** (ID 392: less damage from the front).

| Function | Address | What it does |
|---|---|---|
| `mskill.dat` in memory | descriptor `0x14076B888`, rows `0x14076B890` | Registered at start-up by `0x14006DA90` |
| Evility parameter lookup | `0x1401A5E10` | Scans `mskill.dat` rows (stride `0xD4` = 212), matches the ID at `+8`, reads parameter byte `+0xD + index` |
| Active parameter aggregation | `0x14010DE70` | Sums a unit's active evility parameter across its sources, with restrictions |
| Direction class | `0x14022F930` | Compares the two actors' angles (actor `+0x274`) and writes the class per target at `action + 0x3954 + 4i`: **2** when the difference is under 45°, **0** for 136°–224°, **1** otherwise (written at `0x14022F9C4`; Aegis acts on class 0) |
| Damage-modifier chain | `0x14010ACA0` | Intermediate damage modifiers, in order. Aegis check at `0x14010C89D`, magnitude call at `0x14010C8B1` |

**Aegis, as the code does it:** if the hit comes from the front, the signed integer damage so far becomes
`truncate_toward_zero(damage × (100 − 30) / 100)`, then the next modifier runs. So 101 becomes 70 at that step. Order
and integer truncation matter: D5's nearest equivalent works in floating point, so results differ at the edges.
Direction also differs: D4's direction classifier, D5's, and D5's prepared attacker facing **disagree at some adjacent
diagonals**, so a port that uses D5's front/side/behind classification is a D5-native choice, not an exact match
(**From decomp**, D5 Mod Kit fork `docs/D4_RULES.md`).

### Bloody Battle (evility 347, Valvatorez)

Traced by running the original D4 code in an emulator on unchanged tables (**Verified offline**: original
instructions executed; not tested in the running game). The description reads like a personal kill count that
raises ATK; the code does something different:

- **The counter belongs to a unit index, not the unit.** Each unit has a raw index byte at `unit+0x1161`. A manager
  keeps one byte per index (`+0x11C + index`, read by `0x1402E9180`, incremented by `0x1402E8D70`), **capped at 99**.
  Units sharing an index share the counter. A second counter per index (`+0x120`, `0x1402E8CC0`) counts for the target.
- **When it counts:** in the action-outcome function `0x140191170`, after damage, when the target is defeated. Targets
  whose character ID / 100 is 51 or 52, or whose ID / 10 is 481, are excluded (in the data those are Geo blocks, books,
  tombstones). The code doesn't check that the attacker has Bloody Battle.
- **What it does:** the attack calculation (`0x140106BBE`) adds `counter × magnitude` (5 per kill) to an
  **attack-side damage modifier**, not to the ATK stat. At 99 kills that's +495.
- **Stacking:** duplicate learned copies count once (5); a learned copy plus an equipped one gives 10; several
  equipped copies without a learned one still give 5.
- Attack and defense modifiers are clamped to **−99 … 2000** (`0x140103EB0`).

D5 has no equivalent: its Absorption and Combat High evilities behave differently, so a port needs new code.

### Unit fields and evility sources

| Field | Offset in the unit | Notes |
|---|---|---|
| Raw index (shared counters) | `+0x1161` | |
| Level | `+0x1130` | |
| `char.dat` row index | `+0x1136` | Found by scanning rows for the ID at `+0x194` |
| Innate evility list | `+0x1192` (count bound `+0x13A2`) | Copied from `char.dat` `+0x1B4` at creation |
| Character ID / 10, ID / 100 | `+0x1806`, `+0x1808` | Used by target-exclusion tests |
| HP (current / max) | `+0xD10` / `+0xD20` | |
| Evil Area list | count `+0x189E` (signed byte), entries from `+0x183E` (one byte each) | Read by `0x1400E92E0` / `0x1400E9280`; `0x14024B490` tests whether two units' lists share an entry |

An evility's magnitude (`0x14010DE70`) sums its sources: the unit's learned list, equipped items, Geo effects and
the **Cabinet** (`MinistryMap.dat`, provider `0x1400FC090`), which gives a post's evility to the unit holding it. Two
neighbouring checks show the call pattern: Aegis calls the magnitude function with ID `0x188` (392) at
`0x14010C8B1`; the stage before it calls `0x140105770` with ID `0x2BA` (698); Helping Hand (`0x33A`) uses a separate
whole-army sum at `0x140105B40`.

**Friends (evility 840)** is not a squad count: its loop (`0x1401BA0A0`) counts the *other* battle units whose Evil
Area list shares an entry with the holder's, skipping dead or inactive records and a few excluded unit kinds. Miasma
(114) in the same loop adds **each** eligible adjacent hostile provider's value, so two providers stack. (**From
decomp**, call arguments resolved from the executable; D5 has no direct equivalent, D5 Mod Kit fork research,
2026-10-30.) Details: the holder itself is skipped, and a unit counts only if its battle record is active (`+0x294`), it isn't
dead, and it isn't one of the excluded kinds (`0x1401B1280`: class `+0x1808` 0x33–0x34 or family `+0x1806` 0x1E1;
`0x1400EEA80`: three character IDs at `+0x1132`); the count × the evility's value is added to the stat total.
Miasma's adjacency test (`0x1400ED660`, called at `0x1401BB189`) compares both units' **footprints**, then the
4 × 4 team-relation table (`0x14022D830`) decides hostility, and each provider's value is subtracted from all three
element slots (unit `+0x118C` / `+0x118E` / `+0x1190`), unless the recipient is exempt (`0x1400EC4D0`).

### Elemental damage evilities compared

D4's Fire's / Wind's / Star's Blessing (13 / 14 / 16, +30%) are checked in the damage-modifier chain `0x14010ACA0`
(sites `0x14010B619`, `0x14010B6F4`, `0x14010B8B8`): each needs a skill of the matching element and **multiplies the
running damage by (100 + 30) / 100 at once**. D5's nearest evilities (Lava / Storm / Comet Blessing, 4019 / 4027 / 4031)
are checked in `0x1401F8FD0` against element flags 1 / 2 / 4 and **add** 30 to the shared modifier, capped later at
2000. So the same "30%" stacks differently once other boosts apply.

In D4 the element test is `0x14010ECF0(skill, 1 / 2 / 4)` and needs a non-zero action skill ID. **Physical Boost (8)**
is gated in the same chain at `0x14010B380`…`0x14010B3F4`, with its physical-skill test in `0x14010ED60`, and applies a
signed percentage to the running damage (**From decomp**, emulated with learned and equipment sources, D5 Mod Kit fork
research).

### Hard-coded evility checks

A static sweep of the executable found **1,102 call sites testing literal evility IDs, covering 511 of D4's 1,230
evilities** (D5: 1,730 sites, 602 IDs), plus 175 sites with computed IDs. So in D4 too, *what* an evility does lives
in code at those sites.

### A few evility mechanics traced

(**From decomp**, D5 Mod Kit fork research, 2026-10-29/30; call sites read from the executable.)

| Evility | How D4 does it |
|---|---|
| Second Attack (446) | In the damage sequence (`0x14010ACA0`), when the action's combo step (short at `+0x7AC`) is non-zero and the attacker has it, damage is multiplied by `(100 + value) / 100`, truncated. It sits among the other damage multipliers, not in the attack-stat stage |
| Assist (448), Marking (449) | Set a **one-shot flag for the next actor** in the combo (two separate bytes per participant, `0x1414794C9` / `0x1414794CA` + (index + 1) × `0x4478`), set in `0x1402E0490` and `0x1402E13A0`. Not an aura |
| Magic Wand (695) | When the attacker's battle-record byte `+0x298` is 7 (copied from the unit's `+0x115B`, probably a weapon category), `0x140102FA0` swaps the attacker's stat at `+0xD30` for `+0xD40` (ATK → INT) and the defender's `+0xD38` for `+0xD58` (DEF → RES), in both the forecast and the real hit |
| Anti Elements (230) | The recalc (`0x1401B4010`) adds the evility's value to three element-resistance slots while the unit's HP is at or below the evility's threshold (inclusive at the integer-rounded threshold; the getter `0x14010DE70` uses max HP `+0xD20` and current HP `+0xD10`). The status display (`0x1401A6310`) uses a strict float `< 0.25` instead, so at exactly 25 % the two can disagree |

### Counts never include the holder (2026-11)

The stat aggregate `FUN_1401ba0a0` skips the holder's own battle record before every count, Meowyland (837) and Friends
(840) included; Phantom Lover (875) counts other units of the holder's team in Spirit class group 205. `FUN_1401B4010`
adds Jet Black Sword (851) to the holder's eight equipment-aptitude terms while its Magichange timer is set.
**From decomp**.

**More evility sites (2026-11, From decomp):**

| Evility | Where |
|---|---|
| 807 Stone Statue | `0x1400F3C20` (per-owner end of turn) increments unit `+0x22DE` (saturates at 10) while battle record `+0x4E8` is 0, else resets it; `FUN_1401B4010` turns it into DEF |
| 820 Physical Malice | `0x1402E99D0` summons a unit (level = max(1, holder level × (60 + rand 40) / 100), at most 32 active units; creation `0x1402AD4C0`) |
| 881 Unlosing Mode | A menu command (`0x654`): `0x1400F61F0` stores used = 1, remaining = 3 at unit `+0x236E`; `0x1400ED540` tests it; `0x1400F3C20` decrements |
| 691 Mind Killer | `FUN_1401946B0`: sums evilities 321 and 691 and subtracts from the target's SP pool (`+0xD18`) |
| 836 Vanishing Cat | `FUN_1402DD8B0`: for each target at distance 1, computes the relative angle and calls the facing setter |
| 802 Self-Destruct | `FUN_1402EE580`: emits the effect and damages the holder by its current HP |
| 864 Base Hospital | `0x1402EB670`: walks the party roster, skipping dead units and units that are on the map |
| 868 Necromancy | `0x1401946B0` → `0x1400F2680` |
| 129 Geo Energy | Class family `0x1E1`, colour at battle record `+0x25A`, total clamped at +100 |

The common stat aggregate is clamped at +100 / −50.

### Signature evility rules traced in November 2026

All **From decomp** (call sites and fields read in the executable; function numbers are `0x…` addresses of this build),
from the D5 Mod Kit development fork's signature-evility work. Rows that were later rebuilt for D5 and seen in a D5
battle are listed in [evilities-vs-d5.md](evilities-vs-d5.md#signature-rules-rebuilt-for-d5-2026-11).

- **The stat percent sum.** `0x1401BA0A0` adds one term per percent-type evility (Phantom Lover 875 `0x36B`, Eccarlate
  878 `0x36E`, Friends 840 `0x348`, Unlosing Mode 881 `0x371`, Geo Energy 129 `0x81`, …), clamps the total to
  **−50…+100** and returns it. Callers store it at battle-record `+0x5BE` and call `0x1401B4010(unit, percent, 1)`, which
  multiplies **exactly six values: ATK, DEF, INT, RES, HIT, SPD**. HP and SP are loaded separately and are not touched,
  so these evilities never change HP or SP. (An earlier port raised all eight.)
- **Medical Insertion (304).** `0x1400FF700` (the per-target result routine) is its only reader: for support-magic
  skills `value = (read(0x130) + 100) × value / 100` with signed truncation, then capped at 100.
- **Everyone's Mechanic (892).** In `0x1401B4010`, for every *other* active unit on the holder's team that is not a Geo
  Symbol, the sum of the evility's value is capped at 100 and multiplies the item stat of the weapon slots only
  (slot 0; slot 1 when unit `+0x20D8` is not −1) as `item × (100 + sum) / 100`, before the aptitude weight. Armour is not
  raised.
- **Base Hospital (864).** The loop sits in `0x1402EB670`, inside the per-unit pass that `0x1402EA550` state 1 runs;
  that state is entered once per round, after every side has acted (reached from battle-machine case 100 → 0x66). So one
  holder fires **once per round**, two holders fire twice. For each roster record with byte `+0x20D3` zero (apparently
  "not absorbed into another unit": set by the four routines that move a roster record into another unit's member list
  — a reading, not proven), alive, and not on the battle table, HP `+0xD10` rises by `max HP × value / 100` and SP
  `+0xD18` likewise, truncated (no minimum of 1), clamped at the maximum. There is no team filter on the recipient.
- **Tyrant Guard (667).** In `0x1400FF700`: the holder's hit counter is unit byte `+0x1183` (`c`), the threshold `T` is
  team-record byte `+0x120` (team table stride `0x124`). A hit that would leave HP above 0 is nullified while
  `c < max(1, T)`; a lethal hit always goes through. The counter rises by 1 (cap 255) in the HP-change script command
  `0x140123940` when the target's damage is above 0 — a nullified hit counts too — and resets at the end of the unit's
  turn (`0x1400F3C20`). `T` is the number of **Tyrant Cult (669)** holders on the target's team, tallied by `0x1402E9400`
  (found by searching for the stride, not the table symbol). No D4 class or unique carries 669, so in practice the rule
  is "the first non-lethal hit between two turn ends is nullified".
- **Evasion family.** `0x1400FF700` computes `HIT × 100 / SPD`, adds flat terms, subtracts the *defender's* Kyoko value
  (844, `0x34C`) only while the defender's class ID (`+0x1132`) is 350 and an adjacent same-team unit holds evility 1,
  caps at 100, floors at 0, then multiplies by the product of `(100 − v) / 100` over the defender's evilities 0x36, 0x277,
  **351 when the per-target direction class is 0 (front)**, 620 when it is 1 (side), 0x164, 0x376, 0x160/0x161 and
  0x162 (the float comes from `0x140107100`). The roll has two stages: a failed first `rand(101)` gets a second
  `rand(100)` that decides between a full miss and a weakened hit (result 2).
- **Speedy Move (690).** `0x1401BA0A0` writes six percent shorts at unit `+0x17F4…+0x1802`; term `0x2B2` runs after their
  clamp and adds `5 × move counter` to the SPD short at `+0x17FE` unclamped. The reader is `0x1401B6AA9` (inside
  `0x1401B4010`). The counter is battle-record `+0x4E8`, set by `0x1400F1200` to the path cost of the destination cell
  and zeroed by `0x1400F3C20` each turn.
- **Geo Energy (129).** The `0x81` term adds `count × value`, at most 100, where *count* is the number of distinct colour
  values (record `+0x25A`) among active, living units of family `0x1E1`; the holder is skipped when off the map, carried
  or itself of that family. A scan of the 448 map unit files found placement colours 0–6 only and at most 6 distinct on
  a map, so the bonus stays under the cap of 100 (**Verified offline**).
- **Rising Dragon (663).** `0x140106900` adds `10 × counter` (unit `+0x20D5`, signed byte) to the attack-power percent.
  `0x140123940` adds 1 (cap 20) per target whenever the target's result slot is not negative — hits, nullified hits,
  misses and buffs count, heals do not. `0x1402DD8B0`, run for every queue entry at the end of every action
  (counter-attacks included), sets the actor's counter to 0 with no condition.
- **Stone Statue (807).** The unit byte `+0x22DE` rises by 1 while below 10 if the battle-record move cost `+0x4E8` is 0,
  and is set to 0 otherwise (`0x1400F3C20`; writers of `+0x4E8`: `0x1400F1200` from eight callers, 0 on cancel).
  Acting, being hit, lifted or thrown do **not** reset it. `0x1401B4010` adds `10 × byte` to the DEF percent (cap 100).
- **Vanishing Cat (836).** `0x1402DD8B0` (called after the whole action script ends, so after damage) turns each surviving
  target at footprint distance 1 (`0x1400ED660`) whose killed flag (`action + 0x3E98 + i`) is 0 to face **away** from the
  attacker. The angle routine `0x14022F350` uses the dominant axis: with dx, dy the difference, `|dy| < |dx|` gives 90 or
  −90 by the sign of dx, otherwise 0 when dy > 0 and 180 when not; so ties go to the vertical, and a zero distance gives
  180.
- **Miasma of Silence (115).** The helper `0x140105640(unit, 0x73)` sums the value over adjacent, hostile, living,
  non-object units. Seven call sites in six functions consume it, and every one whose meaning was read treats it as
  **"silenced" (status kind 10)**: the two command-menu builders (`0x14030B440`, `0x14030D920`) clear the special-skill
  flag, two AI action blocks in `0x1402F5020` take the "cannot use skills" branch, and the AI's special-skill chance
  (`0x140231370`, `0x1402302B0`) becomes 0. Status kind 10 is not named in the executable; "Silence" is a reading from
  those consumers. There is no single shared predicate in D4 either.

## Skills: from battle to animation

1. The battle action takes the skill ID (signed 16-bit at `action+0x7A0`) and starts event **12,000,000 + skill ID**
   (the same scheme as D5; dispatch `0x1402DC370`, event ID stored at `action+0x4028`, started by `0x1402E0490`).
   As in D5, each skill also has an **animation-skipped** wrapper `12,100,000 + ID` and a **skipped-midway** wrapper
   `12,200,000 + ID`; the skipped-midway ones call the shared event 11120001 with per-skill arguments. A missing normal event falls back to 11,000,000; the
   alternate path tries +100,000 and finally 11,100,000.
   Engine: `0x140255730` loads `script.dat` and `talk.dat`; `0x14022A6C0` reads the index (count, offsets, IDs);
   `0x140227010` finds an event and allocates its `0x1A8`-byte script context; `0x140155810` runs contexts;
   `0x140123FE0` reads each **2-byte opcode, high byte first**, and binary-searches the **482 registered commands**
   (registered at start-up in `0x140001540`). The call command `0104` starts a child event.
   Motions: `6510` → `0x1400F2090`; the drawn animation is motion × 10 + direction (1 front, 7 back).
2. That wrapper event calls a scene program; scene events call other events and native actor, motion, camera, effect,
   particle, sound and battle commands.
3. Events live in `script.dat` (24,036 events, 15.2 MB, inside the database archive; D5's has 19,053 events, 10.6 MB). Bytecode is close to D5's: 2-byte opcodes, but **12 commands
   have different operand widths or are D4-only**. For example `7220` stores operands as 1/2/2 bytes where D5 uses 2/2/2.

**The first 12 commands found that differ**, with their D4 literal operand widths in bytes (from the game's command
constructors, traced October 2026). The later re-derivation below found 30 more, so this table is not the whole list:

| Opcode | D4 operand widths | Notes |
|---|---|---|
| `4410` | 1, 1, 1, 1 | Particles |
| `4418` | 1, 1, 1, 1, 1, 2 | Particles |
| `4620` | 1, 2 | Sound |
| `4622` | 1, 2, 1 | Sound |
| `646A` | 2, 1 | Actors |
| `668C` | 2, 2 | Actors |
| `7220` | 1, 2, 2 | Hit share (D5: 2, 2, 2) |
| `7226` | none | Remaining hit share |
| `7228` | 1, 1 | Combat finalization |
| `72D0` | 2, 2 | Battle |
| `3700`, `3701` | none | |

**Operand widths re-derived from D4's own parsers (2026-11-20, Verified offline).** Each registered command is an object
whose fifth vtable entry parses the command's operands. Running that parser in an emulator over a stream of literal
operands and watching which bytes it reads gives the widths. The method reproduces all 12 rows above and adds **30
more** where D4 differs from D5 or D5 lacks the command. Examples: voice `4630` is `{u16, u8}` in D4 against `{u32, u8}`
in D5; the sound commands `4600`…`460C` take a `u8` channel; `64A0` has a fourth operand; `6B0x` and `78xx` differ. The
wrong `4630` width alone had desynchronised 19 special-attack events; with the corrected table the 16 scene closures that
would not decode now do, and every event they reach re-encodes byte for byte. The earlier assumption that every command
not in the table follows D5's layout is therefore **withdrawn**: use the parser, not D5's table (D5 Mod Kit development
fork, `docs/D4_SCRIPTS.md`).

`script.dat` is a member of the language's database archive (`DATABASE_<LANG>.dat`). A translator to D5 must keep
signedness, scaling and sentinel values, and fix conditional jump offsets when instruction sizes change.

Other differences found while translating D4 scenes to D5 (**Verified offline** in an emulator unless noted):
- **Loop counter variable:** D4 `10100`, D5 `20100` (same meaning: the innermost loop's counter). D4's getter is
  `0x140154C70`; its loop-frame fields sit `0xC0` lower in the script context than D5's (counter `+0x13E` vs `+0x1FE`,
  depth `+0x1A0` vs `+0x260`).
- **`6510` motion selector literals differ** for some motions (e.g. D4 `202` / `215` vs D5 `0xE13` / `0xE1D`).
- Child scripts start at loop depth 0 in both games; depth clamps at 7.

Commands seen in skill scenes:

| Opcode | What it does |
|---|---|
| `6510` | Sets an actor's pose (motion) index; the drawn animation is pose × 10 + direction, so 400 becomes 4001 (front) / 4007 (back) |
| `3C00` | Loads an effect bank (`effect%03d.lzs`) and creates an effect instance |
| `7220` | Applies a share of the precomputed damage at a cinematic hit (Impaler Drop: 5% + 5% + 90%) |
| `7226` | Applies the remaining share |
| `7228` | Finalizes combat |
| `7810` | Carry: lifts a unit (Lift Catch uses `7810(101,151)`, which runs the carry subsystem, links carrier and carried, and picks their motions) |
| `6410` | Creates a script actor from `source`: mode = `source ÷ 10000`, ID = `source % 10000`. Modes 0/1 clone a current actor; mode 5 looks the ID up in `char.dat` (body `+0x196`, palette `+0x294`) and otherwise uses it as a raw sprite number. So `59100` means mode 5 / resource 9100, not character 59100 |

Details behind these (**From decomp**, D4 Complete+ PC):
- **Hit shares:** the precomputed total for target *i* is at `action + 8 + 8i` (read by `0x1401070C0`); `7220`'s callback
  (`0x140126070`) converts `total × percent / 100`, records the share used at `action + 0x3AD4 + i` and the amount at
  `action + 0x3B38 + 8i`, and caps the last share to what's left. `7228` finalizes through `0x140191170`.
- **Motions:** `0x1400F2090` stores the motion at actor `+0x262`; the tag lookup (`0x1404379E0`) falls back to the first
  tag when the requested one is missing, as in D5.
- **Effects:** `3C00` decodes the bank (`0x14025B050`, file name from `0x14025A930`), and `0x14015D970` takes one of **256**
  runtime effect slots for the instance.
- **Carry (`7810`):** `0x140144ED0` follows the lift stack (`0x14022F7A0`) to the top carrier and calls `0x1400EF8B0`, which
  sets motion 110 on the carrier and 106 on the carried unit, links them at actor `+0x29E` / `+0x2A0`, computes the stack
  height and sets unit flags `+0x180A` / `+0x180B`.

Examples:

| Skill | Wrapper event | Scene root(s) | Events reached |
|---|---|---|---|
| Lift Catch 13081 | 12013081 | 16060200 | 15 |
| Legit Shield 13082 | 12013082 | 16040200 | 15 |
| Impaler Prince 10011 | 12010011 | 15240600 | 28 |
| Bloody Hole 10012 | 12010012 | 15051100 | 21 |
| Tyrant Flughude 10013 | 12010013 | 15030600 | 24 |
| Impaler Drop 301 | 12000301 | 15035200 / 15042300 | 23 |

**D4 vs D5 versions of the same skill:** D5 already has Valvatorez's three D4 skills (Impaler Prince 10571 → scene
15017800, Bloody Hole 10572 → 15017900, Tyrant Flughude 10573 → 15018000). Their scripts share long identical runs but
differ in actor handles, variables, coordinates, art, effects and audio. Shared helper events aren't safe to assume
equal: D4's helper **12000036 doesn't exist in D5**, and **12000064** does something different.

All 143 events reached by these skills decode and re-encode byte for byte with the corrected D4 command table
(**Verified offline**).

**Commands checked by running their original code:**
- `0148` returns 1 and touches no state (same in D5).
- `3cf4 bank, value` **preloads an effect bank** in D4 (decodes it and registers it as bank ID + 700, via `0x14025B050`).
  In D5 the same command does nothing: D5 loads effect banks at start-up instead (loop `0x140709F20`). A D4 scene
  that relies on `3cf4` therefore needs its banks to be present in D5's start-up set.
- `1424 index, value` sets (value ≠ 0) or clears event flag 3900 + index, indices 0–99 only; identical in D5. Skill
  scenes use it as scratch flags (event 12000016 sets flags 3900–3902).
- Some skills are special-cased **in code by skill ID**: target selection (`0x14022ECC0`) compares the skill row ID
  with 13202 (Land Decimator) and then excludes certain targets. Copying such a skill's row to a new ID loses that.
- `3700` / `3701` restore a render-environment colour (two different fields). They are **not** end-of-scene markers:
  dropping them changes rendering.
- `0A00` (a counted wait): positive requests store `request − 1`; D5 additionally clears two globals on request 1.
- `72D0` sets two **scale factors** in shared script state (`/10`, then `/100`: `72D0(750,760)` gives 0.75 / 0.76,
  `72D0(1000,1000)` resets to 1). In D4, commands `6460` and `65A0` (actor scale), `6660` (actor position) and `6E32`
  (an interpolation target, possibly the message window) multiply by them when enabled. **D5's matching commands
  don't read these factors**, so a D4 scene copied literally to D5 loses that scaling: one tested pair, `6E32(2000,1500)`
  under `(750,760)`, lands at (1.5, 1.14) in D4 but (2, 1.5) in D5.
- **Actor selectors mean different things.** In D4, actor 101 is the **first attacker** only (even when the action
  has two), and 202 walks **every target** in order, duplicates kept, skipping flagged targets unless global flag
  0xFFF is set. In D5 the literal numbers 101 and 202 are plain dynamic actor slots; D5 writes the first cast member as
  6101 (second 6111) and every target as 3603. A D4 scene copied with its literal 101/202 picks the wrong actors in D5.
  (Original code run in an emulator for both games, D4 → D5 expansion research.)
- **`65A0` (actor scale) compounds** in D4 when the `72D0` factors are on: with factors 0.75 / 0.76, the first selected
  actor ends at (0.75, 0.76) and the second at (0.5625, 0.5776).
- Battle variables 10600 + i in D4 correspond to 20600 + i in D5. Script contexts are `0x1A8` bytes in D4 and `0x268`
  in D5.
- **Script variables:** D4 local float variables are IDs 10001–10016 (D5: 20001–20016), alongside the loop counter
  10100 / 20100. They live in different places in the script context, so no blanket `+10000` remap is proven.

### Script command callbacks, D4 and D5 side by side

Where each command's code lives, for anyone porting scenes or hooking a command (D5 addresses are build **B**; from the
registration code and the emulator runs in the D4 → D5 expansion research):

| Command | D4 | D5 | Notes |
|---|---|---|---|
| `0108` end (waits for children first) | `0x1401502F0` | `0x14018CD20` | D4 uses child query `0x1401523A0` and rewind `0x1401557B0` |
| `0148` Endif | `0x14014FD00` | `0x14018D340` | Both just return 1 |
| `0A00` counted wait | `0x140150430` | `0x14018D520` | |
| `1418` | `0x140149960` | | Helper `0x14015FBB0` |
| `1424` scratch flags | `0x140149C20` | `0x140184910` | D4 clear / set helpers `0x14015FCE0` / `0x14015FD00` |
| `3700` / `3701` render colour restore | `0x140146970` / `0x1401469F0` | | |
| `3C00` load / show effect | | `0x140179830` → `0x14018E690` | |
| `3CF4` effect preload | `0x140139EF0` | `0x14017E400` | No-op in D5. Registered at start-up by D4 `0x1400033DD` (constructor `0x140122A70`) and D5 `0x140010F3C` (constructor `0x140086790`) |
| `646A` (D5 `6468`) actor field | `0x140128850` | `0x14016B220` | Different fields, not interchangeable |
| `65A0` actor scale | `0x14012E260` | `0x14016E840` | D4 also uses `0x1400F7030`, `0x1400F6F20`; D5 `0x14014CA40`, `0x14014C3A0`. Scaling an empty part list is cancelled by D4 `0x140434A40` / D5 `0x1402F1FC0`; D5's ANM scaling is `0x1402F2CE0`. Actor fields it reads: normal body D4 `+0x1C0` / D5 `+0x1D0`; alternate model D4 `+0x1C8` / D5 `+0x1D8`, used when the short at D4 `+0x28E` / D5 `+0x2E2` is 2. D4 also requires actor byte `+0x29A` and script-state byte `+0x19` to be 1, a gate D5 doesn't have |
| `6660` actor position | `0x14012F5E0` | | |
| `668C` actor move | `0x140130700` | | |
| `6E32` interpolation target | `0x1401368C0` | `0x140176F10` | |
| `7226` remaining hit share | `0x140125B20` | | |
| `72D0` scale factors | `0x140125E10` | | Writes the shared state at `0x14175AD10` |
| Interpreter / dispatcher | `0x140155810` / `0x140123FE0` | `0x140191D40` | D4 command wrapper `0x140120020`; operand executors D4 `0x14011F960`, D5 `0x140086630` |
| Variable resolver | `0x140154C70` | `0x140192D20` | |
| Registered-command table | `0x1410EFD30` (482 × 8 bytes) | | Built by `0x140001540` with allocator `0x14010FA60` |

### Which commands behave the same in D4 and D5

From the D4 → D5 scene translator's verification (D4 Import Mod research, 2026-10; each verdict compares the two games'
own callback code instruction by instruction):

| Command | Same in D5? | Notes |
|---|---|---|
| `0104` call event | **Yes, with a guard** | D4 **zero-fills** the child's registers above the number of arguments passed; D5 **inherits** the parent's 16 registers. A callee that reads a register above its argument count behaves differently |
| `0108` end, `0120` wait for children, `0130` wait for all scripts | Yes | |
| `0140` If / `0148` Endif | Yes | `0140` operands: `s16 skip, u8 count`, then per condition `u8 mode, s16 left, u8 compare, u8 mode, s16 right` and a `u8` connector; compare codes `0x0A`–`0x0F` |
| `0150` / `0158` loop push / pop | Yes | Same depth cap and clamp |
| `0A00` wait frames | Yes | (D5 also clears two globals on a request of 1) |
| `1418` clear local flags | Yes | Clears the same 32 bytes |
| `1424` set / clear flag | Yes | Flag `0xF3C` (3900) + selector |
| `3C00` effect set | **No** | D4 first decodes the effect into a global table and uses a different slot table; D5 has no such step |
| `3C10` / `3C18` effect play | **No** | D4 plays every entry of a slot container; D5 resolves one slot and refuses some slot IDs |
| `1400` set system flag | Yes, literal values only | Seven operand bytes on both sides: `u8 storage` (unread), `u16 flag ID` (≤ 19,999), `u8 operator`, `u8 value mode`, `u16 value`. Same bit arithmetic into each game's flag array (D4 `0x140BE57F2`, D5 `0x144FCCB0E`; byte `ID >> 3`, bit `ID & 7`). A *variable* value differs: D4 reads it as a float variable and clears on failure, D5 falls back to an integer variable and treats the variable's ID as true |
| `1440` arithmetic on a local register | Yes, except division | Operations 0 add, 1 subtract, 2 multiply, 4 remainder match (operands in tenths). **Operation 3 differs:** D4 divides as floats (15 / 4 = 3.75), D5 takes the integer quotient (3), and a divisor under 1.0 is a divide-by-zero crash in D5. Registers live at D4 context `+0x100` (index − 1), D5 `+0x6C`; indices outside 1–16 are ignored by both. `1442` is the same with `/256` |
| `4612` sound effect | Yes, re-encode volume | Same layout (`s16 ID, u8 volume, s16 rate`). Volume is 1/255 in D4 (times the SE master) but a percentage in D5; D4 also normalises the rate to 22,050 Hz and the sound's own rate. D5 may swap the sound for a deployed leader's voice |
| `6510` set pose | Yes | An earlier verdict said D5 ignores the pose; that came from a decompiler listing that dropped arguments. Both store the value in the actor's pose field and re-select the current motion (D4 `+0x1E0` / alternate `+0x262`, D5 `+0x1F0` / `+0x2B0`; D5 writes a dword, D4 a word). D4 sets mode byte 4 for current motions 240/250 where D5 always writes 1; selector codes 202 / 215 become `0xE13` / `0xE1D` |
| `6468` draw layer | **No** | D4 keeps 0–11, turns negatives into 6 and ignores 12 and up; D5 maps 0 and 1 to 6 and has no upper limit |
| `6570` motion speed | Arithmetic same | `float(tenths / 100) × 65536`, truncated, in both; the unit selectors differ |
| `6480` colour | Partly | Same quantisation (`(v << 7) / 100`, clamped to 0–255); the code after it differs |
| `6530` restart motion | Not proven | D5 requires actor `+0x1D0` set and `+0x2E2` ≠ 2 and accepts a direct actor handle ≥ 10,000; D4's gate isn't read yet |
| `66B0` marker | Not proven | D5 indexes its marker table with `value − 300`; D4's offset is unknown |

By the fourth review round, 15 of the 116 command families that D4 skill scenes use had a proven translation (64.9 % of
the instructions in the test skill); the biggest remaining blockers are `6530`, `6570`, `66B0`, `6642` and `3C00`. One
lowered event, `12000031`, is a hit reaction: poses 117, 116, 115, 114, then 106, matching what D5 plays on a hit.

*(Status in 2026-11: that count was from the October review. The bulk work below superseded it: 77 of the 80 adapted
specials now convert offline.)*

### D4 special-attack scenes as a source (2026-11)

A special attack in D4 is a scene program: wrapper event `12,000,000 + skill ID`, then child events that move actors,
play motions, run the camera, spawn effects and apply damage in shares. The D5 Mod Kit development fork tested
how much of that converts into a D5 scene. Everything here is **Verified offline** (original D4 data and original
instructions run in an emulator) unless marked; nothing here was seen in D4 itself.

**What converts.** Of 80 adapted D4 specials (79 distinct skills; one has two owners), **77 convert to a D5 scene** that
keeps the D4 cue order. The scene closures run from 2 to 72 events (among the adapted attacks that call a literal 40x
motion, the smallest, Dragon Claw, is 13 events, 110 distinct opcodes and 8 effect IDs). The hit shares, the motion cues on the
caster, the target and the scene's own clones, and the camera all carry. Scenes that hit several targets were also
generated for every target count up to the attack's area size (2 to 9 targets).

**What does not convert, or only by substitution:**

| Piece | What happens |
|---|---|
| Audio commands (`46xx`) | Left out |
| Draw-layer command `6468` | Left out (D4 keeps layers 0–11, D5 has no upper limit) |
| Created actors | See below: only characters D5 also has can be reproduced; everything else becomes an invisible helper |
| Backdrop gradient (`2830`) and particle commands | Left out in the one scene checked (a night-sky backdrop and about 3,000 particle commands) |
| Damage popups | D4 places some at clones or locators (`7220` argument 1); the conversion shows them at the target |
| Scene that never ends offline | Fear the Great's child loops up to 1,000 × 40 frames until script variable `10500` equals 1; nothing in the scene or in its two start-up events writes it, and 468 other writers exist. The exit comes from outside the scene (**Unknown**) |
| Two effect textures | Effects 148 and 205 (Big Splash, Cruel Shot) are `color32` textures whose wrapper says width × height × 4 bytes, but the entry ends 16 bytes short of the pixels plus the 32-byte footer. D4's reader (`0x1404516D0`) was not traced for this case, so the pixels were not guessed (**Unknown**) |

**Actors and targets (From decomp, same body in D4 and D5 unless noted):**

| Meaning | D4 | D5 |
|---|---|---|
| Main target | actor 151 (`0x140244200`, field `+0x31C`) | 3600 |
| Area target *i* | 160 + *i*, up to 191 | 3000 + *i* |
| Every target | 202 (set `{202, 10000, 215, 20000, 25000, 30000}`, `0x1401276D0`) | 3603 (set `{3603, 10000, 3613, …}`) |
| Place ids | 201–214 (`0x1401241F0`), e.g. 212 is a high anchor used by about 117 effect anchors | 3602, 3604, 3605, 3608–3612 (201→3602, 203→3604, 204→3605, 210→3608, 211→3609, 212→3610, 213→3611, 214→3612) |

Scenes branch per target themselves: one scene hits actors 160–163 in the same frame, another loops over the targets 8
frames apart (scene lengths of 744, 752 and 760 frames for one, two and three targets). A "one-target" trace is
therefore not the scene for an area cast.

**Created actors.** `6402` creates an actor from a D4 character look (`0x140129CD0`, the same body as D5's `0x140169130`;
D5 spawns extra actors numbered 1000–1511). The 77 converted scenes create **156** such actors. D4's look number is a
D4 character ID, so it has to be mapped; 98 of the actors are event sprites (IDs in the 9xxx range), props or items with
no D5 look at all. `6400` copies another actor's look and `6412` makes a weapon dummy. When a converted scene commanded
an actor it never created, D5 **ignored every command to it and drew its clones empty** (no crash): the skull
effect of one scene appeared at the map edge because its anchor actor did not exist (**Verified in game**, in D5 build B with a modified executable).
Mapping the look to an invisible helper keeps positions, clones and attached effects working.

**Markers, places, camera.** D4's marker commands (`66B0` set, `66B8` offset; `0x1401316A0` / `0x1401317E0`) have D5
twins. D5's marker table keeps whatever the last scene wrote, so a conversion that dropped them left a target on a stale
panel: one live cast ended with the enemy listed on the caster's panel (**Verified in game**, D5 build B, seen twice). `6630 a, b` puts an actor on a place (`0x1401311B0`; 38 scenes end with `6630 a, a` to put a moved
unit back). Camera turns, lens and return (`3250`, `3258`, `3260`, `3268`, `3280`, `3290`), the mirror switch `72F0`
(40 scenes switch it on for two of the four facings), fades (`2810`) and the backdrop gradient (`2830`) are all D4
commands with D5 counterparts. Coordinates are the same space in both games (script units, offsets turned by the
caster's facing), and both use the same lens command and the battle value 5120. (**From decomp**, D4 and D5.)

**Effect slots.** D4's slot lookup (`0x140150670`) answers only slots 0–127, so effect commands on slot 128 and above are
no-ops there, while D5 indexes 0–255 and uses slots 100–255 about 11,000 times in its own scenes. Slots 0–127 keep
their number in a conversion.

**Arithmetic.** `1440` and `1442` both divide as floats in D4. `1442` is **1/256 fixed point** in both games: the
parser multiplies a variable operand by 256 (D4 `0x14011FE10`, D5 `0x140086120`) and the handler multiplies by
1/256 (D4 `0x140149810`, D5 `0x140184EA0`). Calls that pass the loop counter (`10100` in D4, `20100` in D5) pass its
value, not 0. These two rules also correct offline traces of D5's own scenes (78 stock roots change under `1442`).

**Effect models.** D4 effect textures seen across the special-attack catalogue are indexed 4-bit or 8-bit surfaces with
power-of-two sides from 16 to 512 and 2 to 16 palettes (some animate through palette frames), and materials of 60 or
72 bytes; some materials step through several texture entries (entry count in byte `+0x23`, mode in `+0x22`, value 0
or 2, entry display time in texture attribute byte 2). D5's own models carry 932 72-byte records and about 1,500
animated-entry records, so none of this is foreign to D5. Of 61 sampled models, 54 convert with every material byte,
the geometry and the trailer exact and every palette frame pixel-checked (**Verified offline**). Palette frames inside
animated entries and the larger sizes have not been drawn in a running game yet.

**Played in D5 (Verified in game, D5 build B with the D5 Mod Kit installed).** Ten generated attacks were cast in real
battles; six played and their motion cues landed on the frames the offline simulation predicted, one of them (Dragon
Claw) also passed skip-at-start and skip-midway and returned control to the player. The other four were never cast: the
test route could not confirm "from the caster" area attacks, and a stock D5 attack of the same kind failed the same way,
so this is a fault in the test route, not in the scenes. No effect model failed to load. Fidelity to the D4 scene was
judged only by the offline preview.

**Where the line is.** Offline checks prove that a scene decodes, converts and runs in the simulator; they do not prove it
looks like the D4 original. A scene is faithful only after captured D5 frames are compared with the source's cues, and
the D4 and D5 versions of a move both games have (Valvatorez's three) still differ in actor placement, event sprites and
sound. Hand-picked D5 scenes remain the stand-in for the rest.

### Other D4 anchors

| Function | Address | Notes |
|---|---|---|
| Skill-specific damage branch | inside `0x1400FF700` (block `0x140102B60`) | Tests skill 13202 (Land Decimator), rolls `0x140253AE0(100)` and caps the result at a target field |
| Random number in a range | `0x140253AE0` | Called with 100 for percentage rolls |
| Throw controller | `0x140314CE0` | Plays the throw voice via `0x140252410` (situation 6) |
| Recruiter voice preview | `0x140273640` | Situation 11 |
| Unit → actor lookup | `0x140244320` | |
| Cabinet table loader | `0x140070D44` (count `0x14076BA98`, rows `0x14076BAA0`) | `MinistryMap.dat` |
| Unit aptitudes | unit `+0x18A4` (8 × u16) | Rebuilt by the recalc `0x1401B4010`: a loop queries evilities 61–68 (one per stat) through the magnitude function, other sources add on, and each total is clamped to **10–300** before the low 16 bits are stored (at `0x1401B4E30`). Later stat code multiplies by these words, truncating by 100 and 1,000 |
| Panels-moved counter | battle record `+0x4E8` | Getter `0x1400EABF0`, setter `0x1400F1200` (called by `0x1402F5020` with the destination's path-cost value, and by `0x1403050A0` with the path cursor − 1). Evilities 355 and 690 read it (display capped at 100). The path flood is `0x1402376D0` → `0x140238060`, which adds 1 per ring |
| Script scale factors | set by `72D0`; initialised to 1.0 by `0x1401238B0` | `6460`'s callback `0x140128E70` multiplies an actor's values by them when state byte `+0x19` is 1 |
| Level-up voice | `0x140300BB0` | Reads level `+0x1130`, recomputes EXP, then plays voice situation 10 |
| Battle actors | `0x141184AA0` | 162 records × `0x7D0` bytes; the unit pointer is at `+0x1F0` and only counts while `+0x294` is non-zero (unit → actor lookup `0x140244320`) |
| Evility eligible-source membership | `0x14010DC80` | Companion of the magnitude function `0x14010DE70` |
| Learned-evility slot search | `0x1400E99D0` | Six u16 IDs at `unit+0x1192`; returns the first matching index or −1 (not a yes/no: index 0 is a hit) |
| Aptitude calculation | `0x1401B4010` | Queries evilities 61–68 (the aptitude evilities) in a loop, clamps to 10–300 and writes `unit+0x18A4` |
| Evility replacement query | `0x1401A4D00` | Called from the magnitude function |
| Callers of the damage routine | `0x14022FF60`, `0x1402E0490`, `0x1402E5FD0` | Their fifth argument (1 / 0 / 0) switches skill-specific branches off or on |
| Render environment selector / setters | `0x140250080` / `0x140444F80`, `0x140444FA0` | Used by `3700` / `3701` |
| Actor interpolation / ANM owner scale | `0x1400E5B90` / `0x1404346E0` | |
| Alternate-model test | `0x1400EBDC0` | Actor `+0x1C8` non-null and `+0x28E` = 2 |
| Interpolation target helper | `0x14022A630` | Used by `6E32` |
| Command constructors / wrapper | `0x140122620` (form 0), `0x140123370` (`65A0`), `0x140122C80`; wrapper `0x14011EF50` | |
| Native use of flag 3900 | `0x1402E4680` | Reads and clears it |
| Flag getter | `0x1403DF680` | Called with 3900–3902 from `0x1403DFB64…` |
| Unit records | `0x140BE67C8` | 64 records × `0x2800` bytes, searched by owner ID at `+0x22D8` (`0x14017F9C0`); a new unit is cleared in two blocks (`0x20E0` bytes, then `0x6E0` at `+0x2120`) by `0x1400EB4E0` |
| Voice situation jump table | `0x14014F858` | 12 entries, for situations 0–11 (dispatcher `0x14014F760`); selector −1 skips the situation check |
| Voice playback rate | `0x1404672A6` | Converts the row's signed rate field (`+12`) to float |
| Unit voice call | `0x140252260` | Reads the personality byte at `unit+0x1154` |
| Registered command records | from `0x14112E3D0`, 24 bytes each | In table order: `0108` is entry 3, `1418` entry 26, `1424` entry 27 |

## Unit animation: from action to motion ID (2026-11-24 and 25)

How a battle action becomes the motion a character plays. **Evidence:** **Verified offline** where the function was run in an
emulator on the retail `Disgaea4pc.exe` (Steam build 6150765) or the whole table was checked; **From decomp** where the code
was read but not run. Nothing here was watched in the running game. Where a row says "no motion found" it means no code path
reached a pose setter within the bounded search, not that the game was seen to play nothing.

**Motion ID = pose base × 10 + facing digit** (**Verified offline**, `0x1400F4F10`). The facing quadrant is `((camera yaw -
stored yaw) in degrees + unit facing at +0x26C + 180 + 22 + 360) mod 360 / 90`: quadrant 0 gives digit 1 (front), 1 gives 7
(back), 2 gives -7, 3 gives -1. A negative digit means the same motion mirrored (the final ID is `digit - base × 10`, and the
animation module negates the X scale). A missing ID plays the owner's first motion. Pose commands `6510`, `6514`, `651C` and
`6598` carry a **base**; `6500` and `6590` carry a whole motion ID. `0x1400F2090` stores the base and mode (mode 1 gives
digits 1, 7, -7, -1; mode 4, used by `6510` for character IDs 240 and 250 (Toro, Kuro), gives 1, 7, -7, 3 from the camera yaw
alone). Proven over 30,784 cases.

**Which event an action starts.** The action struct holds the acting units (`+0x308`, up to 10 signed 16-bit IDs, the actor
first), the affected units (`+0x31C`, first target first), the skill ID (`+0x7A0`) and the actor count (`+0x79C`).
`0x1402DE3F0` runs one action: skill ID 0 goes to the plain attack (`0x1402E13A0`), anything else to the skill path
(`0x1402E0490`). Both start the event through `0x140226F90` and `0x140227010` (a linear search of the `script.dat` event table;
-1 when absent) and fall back to event 11,000,000.

| Action | Event (**Verified offline** unless noted) |
|---|---|
| Plain attack, body type 0 (humanoid, the `char.dat` byte at `+0x298`) | `12,000,000 + 100 × t`, where *t* is the main weapon's type byte (0 counts as 1): fist 12,000,100, sword 12,000,200, spear 300, bow 400, gun 500, axe 600, staff 700. The second weapon (action `+0x37D0` set) uses the army record's `+0x2F7` |
| Plain attack, body type 1 (monster) | A non-zero form ID at army `+0x1178` gives `12,010,000 + form`. Otherwise character IDs of 2001 and above give `12,010,000 + sprite`; below that, the sprite when `0x14017B050(id)` says so (a 30-entry list in the executable), else `12,010,000 + (ID / 10) × 10`. Kuro's event 12,010,250 does not exist, so it falls back to 11,000,000 |
| Skill | `12,000,000 + skill ID` (the `magic.dat` ID, rows of `0x164` bytes, ID `u16` at `+0x28`); 420 of the 428 skill rows have their event. A skill is learnt when the army record's level (`+0x1130`) reaches its learn level. Learnset IDs 10344, 20071, 20072, 20211 and 20212 are not in `magic.dat`: `0x1401AF3D0` returns -2 and adds nothing, so they are never learnt |
| Shortened animation (option) | The same ID `+ 100,000`, then for skills `(event / 10 + 10,000) × 10`, then 11,100,000 (**From decomp**) |

Within an event tree the walk follows calls `0100`, `0104`/`0106` (the argument becomes a float local `$10001...` = value / 10),
`0110` and `0114` (**From decomp**). Actor IDs used in trees: 101 to 110 the acting units, 151 the first target, 160 to 191 the
target slots, 202 every target, 200 the event's own actor, 1000 to 1095 and 2000 to 2015 script objects. Commands `6400` and
`6410` copy a unit's body to an object (event 12,000,010 makes object 1064 a copy of actor 101 and plays the swing on it).
A humanoid plain attack uses actor bases 100, 104 and 110, a swing base of 199 + *t* (motions 2001 for fist to 2061 for
staff), and three target helper events (12,000,030 to 12,000,035) that play bases 114 to 125 ending on 106; a monster swings
with base 207 (2071). No script variable that encodes hit, miss, critical, counter or kill was found, so no branch can be
labelled with an outcome.

**Poses the engine sets itself** (**Verified offline**, `0x1400F0950`, `0x14028A800`; the rest **From decomp**). A census of
every direct call to a pose setter in the executable found 371 call sites; the constant bases are exactly 0, 1, 5, 10, 11,
100, 103, 105, 106, 110, 112, 115 and 118 (113 is written directly).

| Base | When |
|---|---|
| 100 / 106 / 113 | Refresh: wait / held (`+0x29E` of 0 or more) / holding (`+0x2A0` of 0 or more; holding wins). Inside a script sequence: 10 (0 for monsters) |
| 0, 1 | Stand; walking (`0x14023B360` stores base 1 while a unit moves) |
| 110, 106 | Lifter and lifted unit (`0x1400EF8B0`); the thrower plays its 110 motion out (`0x14030E7E0`), the thrown unit gets 106 for 10 steps on landing (`0x140312850`). The pose during the flight was not found |
| 105 | Level-up (`0x1402E3E20`: voice situation 10, base 105, sound 0x2332, a 90-step timer); also Dark Assembly senator reactions. 109 (humanoid) or 105 (monster) in `0x14028A800` |
| 118 | Item use: the receiving unit for 60 steps (battle states `0x46`, `0x47`, `0x48`); senator reaction. An EXP item that levels the unit adds 105; the user has no pose |
| 103, 11 | Move hop: a height change over 6 gives 103 (the army byte `+0x1159` of 0; 1 goes 103 down and 100 up; 2 uses base 10), then 11 with voice situation 7 for the landing. Motion 111 is not in `anm00001`, so humanoids play motion index 0 for the landing |
| 115, 106 | Chain-damage battle state (`0x1402CFAC0`, `0x1402CA7C0` and others) |
| 5, 112 | Dark Assembly proximity (`0x140288940`) and base-map objects (`0x14033B330`, `0x14033E360`) |

**Actions that play no pose.** Defend (command-menu case 8, `0x14030B440`) marks the unit as having acted, sets `+0x620` to 3
(keeping the old value at `+0x621`), adds 2 to `+0x506` (unless the unit has evility 0x267) and recomputes stats; those fields
are read only by turn bookkeeping, damage and counter code and the status display. Victory is battle state `0x41A`, which starts
script event 13 (no pose command). No pose setter is reachable within six direct calls of the menu, the victory state or the
damage command `0x140126070` (script starters and indirect calls were not followed), so D4 plays **no motion for defend, victory
or dying** (**Verified offline** for the reachability search). A unit whose HP falls below 1 at the refresh
(`0x1400F3370`, `0x1400F2FE0`) loses its animation actor and shows OBF effect `0x26C` instead (**From decomp**). A few poses are
hard-coded outside these paths (`0x1401E68A0`: -1551, `0x1401E8620`: 1521, `0x1401E86B0`: 1141).

**Open:** the throw flight pose; bases 5 and 112 in detail; weapon-mastery skills (no reader of `char.dat` `+0x94` / `+0x114`
was located); the exact Dark Assembly branches; any variable that tells hit from miss.

### How the engine decodes a script command (2026-11-24)

`script.dat` is a `u32` count, offsets, event IDs, then flat command lists (`u8` group, `u8` sub, operands; group 0 ends the
event). The start-up function `0x140001540` creates **482** command objects (126 inline, 356 through helper constructors), each
holding the command ID, a handler and a vtable that names the operand type list. An operand slot is a `u8` kind followed by a
literal of the type's width when the kind is 0, else a `u16` variable ID; four commands (`if`, set variable, call `0104`/`0106`,
and string `6E20`) read their own layout. Decoding with these widths, all **24,036** events end exactly on their terminator,
and a deliberately wrong width breaks that (**Verified offline**). D5's operand widths applied to D4 data decode only 19,595
events (4,441 raise errors and 5,086 of the rest read a different command sequence), so a D5 script reader cannot read about a
quarter of D4's events.

## Effect textures

D4 effect models (OBF v2) embed textures in the `nismultitexform` format. Reader `0x1404516D0` (dispatched from
`0x140451270`; storage chosen in `0x14045B840`): format at `+0x20`, palette count `+0x25`, width `+0x26`, height
`+0x28`, row pitch `+0x2C`, pixels from `+0x30`, all relative to the second magic. Format `0x0A` has 16-colour palettes,
`0x0E` 256-colour. In the traced skills, 223 textures expand to 341 palette surfaces; the single-palette ones convert
to D5's ARGB TXF without loss (**Verified offline**).

## Other anchors

| Function | Address | Notes |
|---|---|---|
| Scene-record caller | `0x140206D5F` | Its D5 counterpart is `0x1407122B9` (D5 build not stated) |

## Open questions

- Everything outside evilities, damage and skill scenes: menus, recruiting, saves, archive loading.
- The 30 widths added in 2026-11 are derived, not yet checked one by one against a running D4. The behaviour of most
  commands (as opposed to their operand widths) is checked only for the families in the tables above.
- What exits Fear the Great's loop (script variable `10500`), and the layout of the two 16-byte-short `color32`
  effect textures.
- Live checks of the Aegis rule at direction boundaries.
- The throw flight pose, bases 5 and 112, and whether any code plays a motion for defend, victory or dying through a script starter or an indirect call (the search followed direct calls only).

## Sources

- This project's D4 → D5 porting handoff and research (2026-10): execution path, Aegis trace, script statistics; the D4
  expansion reports (`rules/bloody-battle`, `rules/d4-activation`, `rules/d4-provider`, `mvp-evility-sweep`,
  `mvp-unique-type`, `mvp-script-c`, `effects`), which run original D4 instructions in an emulator.
- D5 Mod Kit development fork (Sean13128): `d5lib/d4source.py`, `d5lib/d4scripts.py`, `docs/D4_RULES.md`.
- 2026-11-24/25 additions (action to event to motion chain, pose census, command registration): this wiki's offline checks of the retail files (functions run in an emulator, whole-table checks).
- 2026-11 additions (operand widths from D4's parsers, special-attack scene conversion, effect extensions, signature
  evility rules): the same fork's `docs/D4_SCRIPTS.md`, `docs/D4_EFFECTS.md` and `docs/EVILITIES.md`, and this project's
  November special-attack and signature-evility reports (offline checks; the scenes were played in D5 only as stated
  above).
