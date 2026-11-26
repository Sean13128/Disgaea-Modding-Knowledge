# Automated testing of Disgaea 5 mods

How this project tests imported characters and engine patches without a person playing: what each tool checks, how it
reaches the game, and what it can't see. The tools aren't stored here (wiki rule 2); this page describes them so the
method can be repeated or challenged. Built and used on macOS with the Steam build under a Windows compatibility layer;
**native Windows is untested**.

**Status:** method used by this project, November 2026. The offline tools live in the D5 Mod Kit development fork
(Sean13128); the live harness is this project's own and isn't published.

## The idea: cheapest layer first

A check runs at the cheapest layer that can show the fault, and a live session is the last resort.

| Layer | Cost | Can show | Can't show |
|---|---|---|---|
| 1. File checks ("gates") | Seconds | A pack that breaks a known rule: ID collisions, missing motions, bad layout | Anything nobody has written a rule for |
| 2. The game's own code in an emulator | Seconds to a minute | What one original function returns for given data | Timing, drawing, sound, the whole load path |
| 3. A model of an installed game | About a minute | What the game will do with every installed pack, check by check | Freezes, battle effects, art fidelity |
| 4. The running game, driven by script | Minutes per session, and risk | What really happens | Why |

Every rule in layer 1 came from a fault first seen at layer 4. A fault found live is turned into a gate so it is never
found live again.

## Layer 1: file checks

Run on every pack an importer builds; a failing pack is built but marked not installable. The game fact behind each
gate is on the D5 pages ([limits](../games/mainline/disgaea-5-alliance-of-vengeance/modifiable.md),
[functions](../games/mainline/disgaea-5-alliance-of-vengeance/functions.md#findings-from-november-2026-build-b)).

| Gate | Fails when |
|---|---|
| Used IDs | Any ID a pack claims (sprite numbers including `N + 1` / `N + 2`, table rows, quest ID and progress slot, evility and skill rows, voice IDs) is taken by the clean game, by another importer's reservations, or by an installed mod |
| Evility budget | A learnable evility would sit past row position 999, an install would move an installed learnable row, more than 1,000 shop rows, or an ID a unit can't hold |
| Resident placeholder | A monster-bodied row's `N + 2` wouldn't end up in the always-loaded pack |
| Idle drawable | A body lacks animations 1001 / 1007 or their first frame draws no opaque pixel |
| Render root | The game's drawing rule (node `root + 1`) would draw different parts than the tool's preview |
| Cell pages | A drawn cell points at a sheet page the file doesn't have |
| Skill motions | A learn-list skill needs a caster motion the body lacks, unless vanilla runs the same skill the same way |
| Face template | A low-ID character would get no face animation |
| Voice | A linked voice has a missing, silent, unlinked or out-of-range clip, or a set exceeds 512 lines |
| Round trip | An `anm` file no longer parses and re-serializes byte-identical |

Two offline aids sit beside the gates: a **skill simulator** that reads the game's skill scripts and lists which motions
a skill puts on caster and target, and **contact sheets** that render every installed unit's stand, walk, attack, hurt
and skill poses, front and back, with pass / fail / not-checked marks per pose (the whole roster in under a minute).
Both show what the files say, not what the game loads.

## Layer 2: running the game's own code in an emulator

The method is on [Setting up a decompile](decompile-setup.md#checking-a-finding-by-running-the-original-code): map the
executable's sections into an x64 emulator, load the data tables where the executable expects them, build the inputs
by hand and call the original function. Used for:

- **Proving a patch.** Every evility hook is run through the real patched call site, in a normal mode and in a
  strict mode that checks registers the calling convention says must survive and 16-byte stack alignment. A proof also
  runs the previous build on the same inputs and compares, so a change that alters old behaviour shows up.
- **Reading a rule out of the game.** For example the Quest Shop board builder on a save's progress bytes, the stat
  calculation on a new character row, or the active-evility list of a freshly created unit.
- **Rebuilding data the game computes at start-up**, such as Disgaea 1's attack-shape lists
  ([D1 data](../games/mainline/disgaea-1-hour-of-darkness/data.md)).

Limits: one function at a time on hand-built state. A result here is **Verified offline**, not proof of behaviour in a
battle.

## Layer 3: a model of the installed game

One tool answers, per installed mod and per check, what the game will do with it, by following the game's own code
paths over the files the game reads (installed tables, animation archives, voice tables, the patched executable) plus
a copy of a save. Checks follow the order a player meets them: is the recruit quest on the board, does the Status page
have a portrait / name / innate evility, does the body draw on its idle, are the motions there, does the voice resolve,
are the creation stats sane.

Every result says what it rests on: **executed** (the original function ran in the emulator), **re-implemented** (the
rule was read in the decompile and replayed), **gate** (a layer-1 check) or **linked** (proven elsewhere). A path that
wasn't followed is reported as **not modelled**, and that is never a pass. The tool is kept honest by a regression set
of known-bad states rebuilt from earlier faults, each labelled by live play or an offline report (871 cases at the
time of writing). When a false pass is found it becomes a control case.

## Layer 4: driving the running game

### How it reaches the game

- The Kit's runtime, loaded inside the game, serves a **named pipe** when a debug setting is on. One text command in,
  one reply out. A small relay program inside the same compatibility-layer prefix passes commands from the host.
  Nothing is injected from outside the game's process.
- Commands used: read the game state (frame counter, scene, battle state, unit list); send key presses with hold and
  gap lengths in frames; wait a number of frames; read and write memory; take a screenshot; fake window focus; hide or
  move the window; trace animation lookups and voice requests; sample the frame thread; a few native shortcuts (open a
  facility, leave a battle).
- **Screenshots come from the game's back buffer at `Present`,** not from the host's screen, so they work with the
  window hidden.
- Navigation is done the way a player does it (key presses, one menu level at a time), and each step is confirmed by
  reading state. Jumping to a screen by writing a scene number leaves it half set up
  ([limits](../games/mainline/disgaea-5-alliance-of-vengeance/modifiable.md#hard-limits-and-crash-causes)).

### Safety rules, each from an incident

| Rule | Why |
|---|---|
| While the script is driving, the runtime drops real keyboard, mouse and controller input, and the script checks that guard is on before its first key press | The game reads controllers in the background; a pad in use for something else changed settings and started a new game |
| While the script is driving, the runtime refuses to open save files, the key settings and the configuration for writing | A stray confirm must never overwrite a save |
| Only save slots on an allow-list are loaded | One slot is known bad; the cursor starts on the autosave row |
| Count the prefix's registry keys before and after every session, and stop below a floor | The registry was once silently emptied ([Mac and Linux](modding-on-mac-and-linux.md)) |
| One driver at a time: refuse to start while a game process is running, matched on the **whole command line** of the game's own program | Two drivers corrupt each other's run. A marker file that meant "a person is playing" was later made a note only (it had failed a good install's boot step for no game reason); a stray `find` for the executable's name once counted as a running game and stalled a launch for 300 s |
| Hide the window; never minimise it | A minimised window sets the game's own "minimised" state and it skips work |
| End a session by quitting inside the prefix and waiting for the compatibility layer's processes to go | A killed server leaves orphan processes that hang the next launch |
| No memory writes that swap a unit's class, and no fast memory sampling while a turn ends | A class swap doesn't go through the real load path (it hid one bug and is suspected of causing a freeze) |

### The acceptance pipeline

An install is never done by hand. One script takes a small description of the change and runs fixed steps in order,
stopping at the first failure of a gating step and always handing the game back:

| Step | Does |
|---|---|
| guards | Registry key count, no game running, no player marker, disk space, used-ID gate on the folders to install |
| base | The installed executable and mod list are what the description expects |
| backup | Saves and the as-found install state |
| kit code | Optionally deploy library modules from a named commit; refuse a hand-edited module |
| install | Stage whole folders and run the Kit's installer |
| readback | Read the installed tables back and compare with the expectation |
| boot, load | Start through Steam, wait for the pipe, load an allowed slot |
| facility, soak | Open the screens the change touches; press through lists while watching for a freeze (the pipe staying silent) |
| hit | Enter a battle, attack and be attacked, capture the unit |
| crops | Cut the evidence images |
| handback | Quit, restore or keep, turn remote control off, put the player marker back |

Each step returns pass, fail, **not readable** or not reached. A check that couldn't have shown a failure is "not
readable", never a pass. Without an explicit "keep", a run rolls the install back and proves the files match the
backup. The pipeline's own logic has a test suite that runs against a fake driver (about 490 tests in November 2026),
so the ordering and rollback rules are tested without the game.

### What live runs taught about testing itself

- **Recruit a fresh unit to test a body.** Swapping an existing unit's class in memory skips the load path that fails.
- **A freeze needs a control run, and a log taken at once.** One rare freeze passed on identical content when re-run;
  without the re-run it would have been blamed on the pack under test. It was later caught with the compatibility
  layer's log saved first, and turned out to be a crash in the game's own drawing code, not a hang
  ([Mac and Linux](modding-on-mac-and-linux.md#the-rare-freeze-was-a-crash-2026-11-13)). Copy the compatibility layer's
  log and the runtime's log at the first sign of a wedge, before anything restarts.
- **Check both camera views.** A unit's facing decides front or back idle; about half the units in a line-up show only
  one of the two.
- **A waiting loop must not match itself.** Waiting on "no process matches this name" never ends if the waiting command
  contains the name; wait on a log line or a process ID.
- **A quest board that seems to wrap may not have.** Request rows sort after the main list, so a fast walk can misread
  the end.

## Sweeps: testing many attacks or characters

Written from two long sweeps of converted special attacks (about 75 attacks per sweep) and one assessment of where the
time went. Method only; the game facts they found are on the [D5 pages](../games/mainline/disgaea-5-alliance-of-vengeance/functions.md).
All timings are **Verified in game** (Steam build B, patched by the D5 Mod Kit, one Mac, hidden window, November 2026).

### Where the time goes

| Share of a sweep | What |
|---|---|
| 7-8% over a 5-hour sweep, 16% in a later one | The attack scene itself (about 13 s of an 80 s attempt) |
| about 30% | Giving up the battle and entering and deploying the next caster, 40 to 120 s each |
| about 13% | Menu navigation and targeting |
| about 11% each | Ending turns; finding and leaving the battle |

So **one battle per caster is the main cost**, not the scene. A faster scene (see "Skipping the scene" below) saves
little. Three changes were ranked highest, in this order of ease: a placement rule (no game needed to write), one
battle for several casters, and answering "does this exhaust a pool?" offline before casting.

### Placement rules for skills that start at the caster

A skill whose range is 0 hits an area placed relative to the caster, and the targeting screen answers "no target" when
no enemy stands in that area.

- Put the enemy **2 cells away in line** with the caster. Distance 1 gave "no target" for 8 of the 9 failed attempts in
  the later sweep; one skill and one class family needed **3**. 29 other skills worked at distance 1, so a blanket
  default of 2 is unproven for them; keep a per-skill table.
- **Read positions again before every cast.** The nearest enemy walks to the caster during the enemy phase, so a distance
  worked out once per battle is stale from turn 2. Every failure of this kind in the later sweep happened on turn 2 or 3.
- One skill refused four different geometries in two sessions. Treat that as **unproven**, not as a retry.
  **Update 2026-11-21:** two such skills were later explained by the geometry the skill's own data asks for, not by a
  conversion fault. Read the attack's area shape from its data before choosing a cell: **Final Arm** (a Disgaea 4 special
  converted to D5) hits one panel **3 ahead in line**, and its shape also moves the caster onto the panel *behind* it, which
  must be free; an enemy standing 1 or 2 panels ahead answers "Attack zone obstructed". **Panzer Parrot** hits the three panels
  3, 4 and 5 ahead. With those geometries Final Arm cast on the first attempt (the caster moved onto the panel behind, as the
  shape says) and Panzer Parrot cast too (**Verified in game**, build B). The earlier refusals therefore were the
  placement, and a diagonal placement the driver had tried was wrong. A fixed distance table is the fallback; the area shape
  is the rule.
- 9 of 54 attempts (17%) failed this way in the later sweep, eight of them skills the earlier sweep had already
  explained: the knowledge sat in a report, not in the driver. Put each learned rule into the driver's table.

### Designing the sweep driver

- One unattended script, not hand-stepping through menus: a resume file with one record per cast (cast, no cast,
  crash), where "cast" and "crash" are final and "no cast" is retried; a marker written before each cast so that a
  leftover marker on restart is recorded as a crash and a resume never loops on a cast that killed the game; crash
  capture (compatibility layer's fault line, the runtime's log tail) before the game is shut down; an outer loop that
  restarts the game and resumes.
- Stop on a counter, not on time (see the next section).
- **Several casters in one battle** (each on its own tile and lane, one cast per caster per round, one End Turn per
  round) share the 30% overhead. **Verified in game** (2026-11-21, build B): three casters deployed in every battle of a nine-
  battle run, the second and third were commanded and cast after the first one's scene, one End Turn per round served
  all of them, and each caster got a different enemy. The limit is **not below three**; a fourth caster was not tried, so the
  limit above three is **Unknown**. The third caster is the slowest to deploy (28 to 88 s each). A formation that does not fit
  beside the other casters should fall back to one caster per battle. The first placement move often leaves the unit where it
  was, so the driver tries again, picking another enemy, rather than failing.
- Log the frame rate once a minute for the whole run, with the front application on macOS (the front-application query
  returns nothing from a background process with no window-server connection, so record it from an interactive shell or
  leave it unknown; a first version logged junk). The runtime's own `perf` line
  was capped at 60 lines and only wrote when three frames in ten seconds were slow, so after 14 minutes it fell silent
  and a doubling of deploy times could not be tied to frame rate (see
  [Mac and Linux](modding-on-mac-and-linux.md#the-hidden-window-can-drop-to-1-frame-per-second)).
- Skipping the scene: holding the left Alt key from the Execute prompt started the scene and skipped it, cutting it from
  about 11 s to 6.7 s in 9 of 10 attacks; a mid-scene skip key works too (**Verified in game**). In a later run the
  skipped scene took about 3.0 s against 3.8 to 7.5 s normally, with the effect arena unchanged (no effect loads). Use it only when the
  question is damage or targeting: it skips effect loads, so it proves nothing about loading or looks.
- Backing out of a refused target: pressing Cancel on the map **un-queues** the unit's pending action, and pressing it too
  often undoes the move as well. An earlier run saw "Execute does nothing" for a queued attack; the cause found later was
  that the driver's own cancel key had un-queued it (**Verified in game**; the earlier symptom was the same, which is
  an inference).

### A slot-count probe for resource limits

A game with fixed-size tables turns a leak into a crash with no warning. Find the table's header, read its used count
after every distinct action, and graph it.

- Disgaea 5's effect model arena is a table of **1,024 records** with no NULL check. Reading its used count (header
  `+4`, `0x146A07110`) after every cast showed it rise by 18 to 82 on an attack's first cast, stay flat on repeats, and
  never fall, even after leaving the battle. The session crashed at a NULL write at `0x140308C5D` after about 80 distinct
  casts; restarting the game reset the count (**Verified in game**).
- The first reading was "a cache that is never freed". A later offline diagnosis replaced it: the kit's on-demand effect
  loader built each model's vertex data twice and freed one, so every load leaked a record (**Verified offline**:
  the game's own allocator run in an emulator crashes at the same instruction after 1,023 cycles; a simulation of the
  whole session predicted exhaustion at cast 77 of 78, and the live crash came at cast 78). Lesson: a probe finds the
  limit, but only reading the code finds the cause; and a session simulator driven by the attack lists predicts a
  counter better than casting does. Whether the fix holds live is **Unknown** until a long soak finishes. **Update 2026-11-21:** a later run of 50 first-time
  attacks in one session saw the used count rise with each new attack and **plateau at 423 of 1,024**, with no crash in nine
  runs, the same plateau as the run before (**Verified in game**, build B). That agrees with a fixed leak, but it is not the
  long soak; the second arena (terrain and overlays) was left alone.

### Testing effects that need hits to land

A level-20 enemy against high-level allies misses almost every attack; a test that needs damage needs a setup.
Findings from five effect-checking sessions (**Verified in game**, build B):

- Writing an enemy's attack, accuracy or defence into its unit record is undone by the game's recalculation within about
  a second. Writing the **base terms** (the unit's HP base at `+0x2D18` and its stat bases) persists: zeroing the defence
  base took a unit from defence 170 to 2, after which hits landed.
- Reads taken within 2 s of End Turn returning are one step stale; wait about 10 s.
- **The flip trick:** writing a side flag in an ally's unit record (`+0x2E32` and `+0x2DF8`) and ending the turn makes it
  an enemy-side unit and a legal target; the target card then shows the game's **hit chance**, direction tag included.
  That reads a rule deterministically but is a preview, not rolled hits.
- Counters that an evility reads (a round counter, a turn counter in the stat record) can be written directly. That
  proves the game **reads** the input, not that it **produces** it; say so in the verdict. For example a "+50% in rounds
  1 to 3" row was shown by writing the round counter (ratio 1.50 against the same unit at round 4).
- **Numbers near the damage floor are not evidence.** A damage aura was shown by direction and onset (every hit with 20 or
  more defeats at least 52, every hit with none at most 22, no overlap) but its per-step values could not be matched
  because minimum-damage hits of 1 mixed in. Do not quote the step values.
- Give every check a control: the same unit without the row, or the same row with the condition unmet. Checks run without
  one were recorded as "qualified", not "pass".
- Deploy by (name, class) pair. One sweep deployed a unit from another game that happened to be the first list row with
  the class name, and every conclusion from that battle was wrong.

Effect rows checked this way and passing, with a control each: SP drain on hit (3 SP from a 7 HP hit at 50%), stats
+5% per allied unit of a class, stats +50% while the round is 3 or less, stats stacking +10% per turn, a first enemy
action nullified, a hostile heal reduced by 35%, benched-unit healing, and a per-round counter table. Several others
stayed "not readable" and are recorded as such.

### Testing attacks that hit several targets

A multi-cell attack needs several enemies in its area, and a battle with four enemies that never move has no natural
clusters. Arrange them: write each enemy's actor position (the pair of floats that hold the cell centre, 12 × cell + 6, and
the unit's two u16 cell coordinates), then read the cells back through the harness before casting, since the game's own area
selection is the thing under test. The memory write does not change what is tested (which units the skill's area takes).
Findings from 44 multi-cell attacks, 50 casts (**Verified in game**, build B, attacks converted from Disgaea 4):

- Check each target's HP changes in **decrement steps** (the HP bar steps down once per hit) as well as amount. Add a
  **control**: a single-cell attack on the same formation, with one enemy in the area and three beside it, must hurt exactly
  one. Add a **bystander** outside the area and require it to lose nothing. No area enemy was missed and no bystander was
  hurt in any of the 50 casts.
- A control that cannot be read is "not readable", not a pass: Final Arm's control was blocked because the formation put a
  neighbour on the line in front of it.
- **A target losing HP twice is not necessarily a double hit.** Six attacks showed one target hit twice in some casts
  (the same attack and geometry passed on the repeat). The cause, found by reading the game's action queue every 0.3 s
  while the scene played, was a **normal Disgaea 5 mechanic: a counter to a counter**. An enemy hit by the special can
  counter-attack the caster; the caster can counter that counter; the second attack is the target's second HP step. The
  special's own hit landed once on every target, at one instant (**Verified in game**, build B: 9 doubled casts across five of
  the six attacks, every one a depth-2 counter; the sixth drew no counter in two casts and is the same by analogy, a
  reading). The stock D5 attack tested in the same formation drew counters from enemies in three of three casts. It
  only appears when a target stands next to the caster, which is what let them counter: single-target casts at a distance of 2
  or 3 panels never showed it, because melee enemies could not reach.
  Offline signs that pointed the same way: the same attack and geometry passed on one cast and failed on the next; the extra
  amount did not repeat as the target's own hit did (an independently rolled attack); failing casts lasted longer than passing
  repeats; and it happened with a single caster, which ruled out an ally's Support Attack.
- So a multi-target verdict should count the cast's own steps (one per target), and **record, not fail on**, the reaction
  steps that come from queued counters (and report them separately). Read the queue (the battle manager's pending-action list,
  each entry's actor, skill, depth and targets) to attribute every HP step to an action.

### Finding who writes a memory location

When a global is corrupted and a breakpoint is unavailable, a **software write watch** can name the writer. It was built into
the runtime as a command (off by default, available only when remote control is on) and tried under CrossOver and Rosetta:
protect the page read-only, catch the access fault with a first-in-chain vectored exception handler, restore write access, set
the CPU's trap flag so the very next instruction faults a single step, compare the watched bytes on that step, and record the
faulting instruction's address, its bytes, the old and new bytes, the registers, and an unwind naming modules. Re-protect and continue.

- **Verified in game** (build B): the watch ran under the compatibility layer at an unchanged frame rate (60 fps), a self-test
  passed (fault, single step, a change record naming the writing function), and on a game global that increments every frame it
  named the exact instruction. Cost: two exceptions per write to the watched page (a page written twice a frame cost nothing
  measurable). It found the writer of the corrupted allocator handle in minutes (an unclamped palette copy, see
  [anm](../formats/anm.md)), in two separate boots at the same instruction.
- Limits: while a thread single-steps its write the page is writable for every thread, so another thread's write in that
  window is missed or misattributed; writes the OS makes on the game's behalf fail instead of faulting; at most 64 bytes
  per watch; the fallback that re-protects from a helper thread raced and was not used. A control run (the unfixed content
  crashing three of three while the fixed content ran six of six with zero writes to the page) is what showed the cause was
  right, not the watch alone.
- Make the arming command impossible to reach unless remote control is on, and test that wiring offline: nothing is
  protected or hooked until a `watch` command arrives.

### Pipeline fixes from the same sessions

- **Roll back only what the run wrote.** A file the install created (the effect archive, absent at backup time) is removed on
  rollback only when a receipt written after the install completed (path, size, hash) matches the file's bytes. A file
  that existed before, or changed since, is left and logged. The earlier rule, "absent at backup, present now, so ours",
  could delete a file a failed install had never made.
- **Check the files before backing up.** A baseline that lists a staged addition (a file the install will create) has no
  hash; hashing every listed path crashed the backup. Split the baseline into present, staged additions and missing, and
  fail in the base step, before any backup, when a declared baseline file is missing.
- **A dry run must run the verifier.** Execute the verifier command line in the dry run and fail only when the line itself
  is wrong (a path argument missing, an argument-parser error, an import error); a missing installed-game file is "depends
  on the install", not a failure. One verifier line pointed one folder level too high and the dry run missed it.
- **A failed boot marks the later steps "not reached"**, so a report never shows a pass for a step that could not run.
- A harness command that patches the kit's own helper files should do it through the pipeline's code-deploy step (which
  backs up, rolls back and checks the hash), not by hand-patching the installed copy.

## Limits and open questions

- Nothing here judges how a character *looks* or *sounds*; a person still does that, with the contact sheets and
  evidence images as a short list of what to look at.
- Evility effects in battle were proven at layer 2 only until 2026-11-12. Live checks since then cover about twenty rows
  (see "Testing effects that need hits to land"); most evilities are still proven at layer 2 only.
- Multi-target and counter findings come from one formation (three enemies on the four panels around the caster and a
  bystander diagonal), one facing and one height. Other formations, facings and heights were not tried.
- The live harness depends on this project's machine setup (macOS, one compatibility layer); whether the same pipe and
  guards behave the same on Windows or Proton is **Unknown**.
- The rare freeze under the compatibility layer is explained (a stale bank record in the game's drawing code) and was not
  seen again in 2,519 presses on the same page after the fix, one boot ([Mac and Linux](modding-on-mac-and-linux.md)). One run is
  not proof it is gone.

## Sources

This project's harness and test reports (2026-10 to 2026-11); the D5 Mod Kit development fork's `docs/GAME_TRUTH.md`,
`docs/IMPORT_GATES.md`, `docs/EVILITIES.md` and `docs/ANM.md`. Sweep, effect-check and pipeline-fix notes, 2026-11-12 to 2026-11-20:
this project's live-run reports; multi-caster, multi-target, counter-reaction and write-watch notes, 2026-11-21: the same. The remote-control idea follows
[disgaea5autoplay](https://github.com/Grid487/disgaea5autoplay) by Grid487 (a scene registry and menu actions); the
resident-placeholder and facility shortcuts follow Cryswar's Overlord. D5 Mod Kit is by eraser4521 (MIT).
