# Modding on macOS and Linux

The PC Disgaea games are Windows programs. What's known about running and modding them elsewhere. **Not tested by this
project as a procedure.**

## Running the games
- **macOS:** through a Windows compatibility layer configured in Steam (D5 Mod Kit's authors tested with
  CrossOver-based setups). Disgaea D2 has a native Mac port (static recompilation, Apple Silicon): see
  [D2 tools](../games/spin-offs/disgaea-d2-a-brighter-darkness/tools.md).
- **Linux / Steam Deck:** Proton. Makai Kingdom: Reclaimed and Rebound is Steam Deck verified; D7 mods are reported to
  load on Steam Deck ([Nexus thread](https://forums.nexusmods.com/topic/13308945-disgaea-7-mods-for-steam-deck)).
- Under Wine, **keep an audio device enabled**: with none, Disgaea 5 crashes at boot inside XAudio2. (A per-program
  Wine override, `HKCU\Software\Wine\AppDefaults\disgaea5.exe\DllOverrides` with `winecoreaudio.drv` set to empty,
  does leave the game with zero devices, and **the game then crashes** in XAudio2, at `xaudio2_9.dll+0xC88E`, confirmed
  in a live run.) To run it silently, for
  example for automated tests, zero the game's own four volume masters in memory instead
  ([D5 functions](../games/mainline/disgaea-5-alliance-of-vengeance/functions.md#runtime-state-and-hook-sites-build-b)).
- **Disgaea 5 hangs about 20 s after start-up under Wine on macOS:** the opening movie (`Data/Movie/dis5_en.wmv`)
  can stall the game before the title screen, with no input needed to trigger it. Setting **`SkipMovie=1`** in the
  `config.ini` next to the saves skips the movie and the game boots normally (**Verified in game**, repeated across
  many launches, 2026-11-02). Set it back to 0 to see the movie again.
- **Texture size limit on macOS:** Wine draws through Metal, which refuses textures wider or taller than **16,384
  pixels** (`MTLTextureDescriptor has width … greater than the maximum allowed size of 16384` in the Wine log). A D5
  sprite bank with a texture page past that froze the game when the page loaded. Windows GPUs share the same
  common limit, so keep texture pages within 16,384 × 16,384 everywhere (**Verified in game** on macOS, 2026-11-02).
- **Disgaea 5 reads controllers in the background** (XInput and DirectInput), and a hidden window still receives
  keys. During an automated run a pad or keyboard in use for something else therefore reaches the game; an automation
  layer has to drop real input itself while it is driving (**Verified in game**, 2026-11).
- **A prefix's registry can be silently emptied (CrossOver).** If `system.reg` can't be opened (missing or
  unreadable) at the moment a wineserver starts, the server starts with an empty `HKLM`, and when it exits it
  overwrites the roughly 4 MB file with one of about 19 keys; no rebuild follows. Reproduced on copies of a prefix
  (**Verified offline**); why the file was unreadable in the real incidents is **Unknown**. A healthy Disgaea 5 prefix
  has about 17,800 keys (`grep -c '^\[' system.reg`) and gains about 21 hardware keys per boot, so counting keys before
  and after an automated session catches it. Back the prefix up before automating launches, and don't start a second
  wineserver against a prefix while a launcher is shutting one down.
- A Wine client whose server is killed while it starts hangs forever and ignores `SIGTERM`.
- Under Wine, the process's private-memory and handle counts read 0, so a leak can't be watched that way.
- A screenshot taken inside the game (from the back buffer at `Present`) works with the window hidden or off-screen.
- A screenshot of a Wine dialog window from the host fails without macOS Screen Recording permission; the in-game
  back-buffer screenshot is unaffected (**Verified in game**, 2026-11-13).

## The rare freeze was a crash (2026-11-13)

An earlier note here called a rare Disgaea 5 freeze unexplained, with the compatibility layer suspected by elimination.
It was caught and is **a crash in the game's own drawing code**, not a hang in Wine.

- What it looks like: about once in 700 key presses on one menu page the game stopped answering, the frame counter
  stood still, and a small window titled "Exception raised" (Yes / No, "Do you wish to debug it?") sat on screen. That
  dialog is Wine's unhandled-exception prompt; the game's frame thread waits in its message loop, which is why every
  earlier sampling showed "a modal message loop" and no game frames. The game's own message-box code and crash handler
  never ran (**Verified in game**, build B, 2026-11-13: one wedge caught after 1,847 presses, 45 minutes).
- The fault: a read at address `0x24` at `0x1402F3744`, in the function that flushes queued sprite draw packets
  (`0x1402F3650`): it reads the format byte of the packet's texture object (`packet + 0x20`) without a NULL check. A
  second variant reads address `0x5` at `0x1402F7A4C` in the packet builder, where the texture header pointer was `1`,
  not NULL, so a NULL-only guard misses it (**Verified in game**, 81 presses into another run).
- The likely cause (**From decomp**; no live reproduction of the stale record itself, and who overwrote the pointer with `1`
  could not be found offline): the game draws from a bank record that is
  not live, either freed by the package release (which zeroes the record's texture and palette tables and frees the
  array; reused memory then gives junk) or still being built (the tables are filled one texture per ~1 ms slice). A
  humanoid unit binds a second part picture, the bank at sprite ID `N + 2`, while its main bank loads, and the vanilla
  game keeps every humanoid's `N + 2` in the always-loaded pack so it is never released. Packs that shipped `N + 2` as a
  loose file let it be released while a node still drew from it. See [anm](../formats/anm.md#the-banks-texture-objects-and-the-null-texture-crash).
- The fix: put every humanoid class's `N + 2` into the resident pack (about 0.14 MB per class), and skip a packet whose
  texture is NULL (that part is missing for one frame; a counter reads how many were skipped). After the change,
  **2,519 presses** on the same page (2,100 of them fast, every row visited, one boot) gave no crash and a skip counter of
  0 (**Verified in game**, 2026-11-14). One boot is not proof; before the change the rate was 1 crash in 1,847 and 1 in 81.
- If a Wine prompt like this appears during a run, copy the compatibility layer's log and the runtime's log first. The
  prompt's text names the address.

## The hidden window can drop to 1 frame per second

With the game window hidden (moved off-screen, never minimised), macOS gave the game **one frame per second** while
another application held the screen: the runtime logged "10 frames in 10 s", key presses took 14 s instead of 0.3 s,
and an install pipeline failed at the save list and the soak step. Three minutes after that application closed the same
install ran 425 to 553 frames per 10 s (**Verified in game**, 2026-11-14). The mechanism is a guess (the hidden window
getting one drawable a second); only the before and after is measured.

- When a live step fails with slow keys or a stuck command loop, read the frame rate before suspecting the build:
  the front application on macOS is `lsappinfo front`. In another sweep the game ran 22 to 100 frames per second (mean
  53) with the window off-screen and the Mac otherwise idle.
- A runtime's own log may not show it: this runtime logged a `perf` line only when three frames in ten seconds were slow
  and stopped after 60 lines. Count frames yourself once a minute.
- Hide by asking the **game** to move its window past the right and bottom edge of all displays (read the display bounds
  through CoreGraphics, which needs no permission), not through System Events: moving another application's window needs
  Automation permission and fails with error -1743 when it is missing. A fixed off-screen position can land on a second
  monitor.

## Crash from a full fixed table (2026-11-20)

A Wine message of the form `Unhandled page fault on write access to 0000000000000000 at address 0000000140308C5D`
is the game writing through a NULL record: Disgaea 5's effect model arena holds 1,024 records and has no NULL check.
Restarting the game resets it. See [automated testing](automated-testing-disgaea-5.md#a-slot-count-probe-for-resource-limits)
(**Verified in game**, build B).

## Saves and settings under Wine / Proton
Saves live in the prefix's Windows `AppData\Roaming` folder (e.g. Proton: `steamapps/compatdata/<app id>/pfx/`). See
[saves](../formats/saves.md) for each game's path.

## Tools
- **D5 Mod Kit** runs natively on macOS (Python 3.10+ with PyQt6, numpy, scipy, Pillow, pefile, keystone-engine,
  capstone; `Start D5 Mod Kit.command`). It launches the game through Steam and finds the Windows process under Wine;
  for separate save profiles, `APPDATA` must point at the prefix's Roaming folder
  ([MACOS.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/MACOS.md)).
- **Proxy-DLL loaders** (Makai Kit's `dinput8.dll`, D5 mods using `dinput8.dll` or `XINPUT9_1_0.dll`) need Wine to load
  the local DLL instead of its own: set a DLL override (`WINEDLLOVERRIDES="dinput8=n,b"`, or in the Proton launch
  options) (**general Wine practice**, not checked per game).
- Python-based tools and readers run anywhere Python does; point them at the game folder inside the
  prefix or Steam library.
