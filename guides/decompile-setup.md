# Setting up a decompile of a Disgaea executable

How this project analyses the games' executables, so you can do the same with your own copies. The output is derived
from game code: **keep it private** and publish only function names, addresses and descriptions.

## Tools
- [Ghidra](https://github.com/NationalSecurityAgency/ghidra) (this project used 12.1, run headless), with a matching JDK.
- Ghidra's **BSim** (bundled) for matching functions between games.
- For x64 checks without Ghidra: Python with `pefile` and `capstone` (D5 Mod Kit's `EXE_ANALYSIS.md` uses these).

## Steps
1. **Import and auto-analyse** the executable in a new Ghidra project. Large ones take time (Disgaea 6: hours).
2. **Export** what you need to search: one file of decompiled C per function, an index (address, name, size), and
   cross-references (callers, callees, strings). Ghidra's headless analyser runs scripts that do this; give each
   function a timeout (this project used 60 s) and resume where it stopped.
3. **Name what you can.** Import known names (D5 has community labels; D2's Mac build ships C++ symbols).
4. **Match against a labelled game** with BSim: load every game's project into one database and match each against the
   labelled one. Treat matches as leads: similarity **≥ 0.9** was trustworthy; skip tiny functions
   (self-significance < 15).

## Per-game notes

| Game | Executable | Notes |
|---|---|---|
| Disgaea PC (D1) | `dis1_st.exe`, 32-bit | May be wrapped by Steam DRM: if most functions look like garbage, unwrap it first |
| Disgaea 1 Complete | Switch `main` (ARM64) | Extract from your own cartridge dump, then load with a Switch loader extension for Ghidra |
| Disgaea 2 PC | macOS build | Keeps C++ symbol names: start here ([D2 functions](../games/mainline/disgaea-2-cursed-memories/functions.md)) |
| Disgaea 4 Complete+ | `Disgaea4pc.exe`, x64 | Matches D5 well (~2,300 close matches) |
| Disgaea 5 Complete | `disgaea5.exe`, x64 | Two PC builds with different addresses ([D5 functions](../games/mainline/disgaea-5-alliance-of-vengeance/functions.md#which-build-an-address-belongs-to)) |
| Disgaea 6 Complete | `Disgaea6.exe`, x64 | Large; check for copy protection if output looks wrong |
| Disgaea 7 | `Disgaea7.exe`, x64 | Matches D5 best (~3,000 close matches) |

**Which file you have:** addresses on this wiki hold only for one build, so check the hash first (SHA-256 of the
Steam builds this wiki documents; full lists on each game's INDEX page):

| Executable | Build | SHA-256 |
|---|---|---|
| `dis1_st.exe` | 1375097 | `61328136fe2b947df7e69711d3eb337ea3a03ca888cf4875a96f5231a64297d1` |
| `disgaea2app.app/Contents/MacOS/disgaea2app` (the Mac binary inside the Steam install) | 2643362 | `c156ef4d03a6fffa4a0c88385d535b0da89120d375a640ae300e0ffdba876f77` |
| `Disgaea4pc.exe` | 6150765 | `2436d4b39aff4291dee71b65055a11161e7ec9186ff98cc2deebc4411614c765` |
| `disgaea5.exe` | 3528766 | `49a7530baa1c3e15e4bf9c63c56fefd71d7e51a6e641bce80280b37b2db282ce` (build B) |
| `Disgaea6.exe` | 9145810 | `712b30819310f0dd4c753b1e7156c15d251acf9559c1e7d395c5d6f6f7bc608c` |
| `Disgaea7.exe` | 13254462 | `116705949427cf665f1af1b93b2e6b881e96099906bbcb4e75051853a93dfa94` |

Match rates for the classic games (D1, D1 Complete, D2) are very low: different engines, compilers and architectures.
See [engines](../engines/README.md).

## Checking a finding by running the original code

A decompiler's output is a guess at what the code does. Much of this wiki's D4 and D5 engine detail (marked
**Verified offline** on the function pages) was checked a step further, by **running the game's own machine code in a
CPU emulator** on hand-built inputs. The method, so others can repeat or challenge a result:

1. Pin the executable: record its SHA-256 and read code only from that file, at its preferred image base.
2. Pick the smallest piece that answers the question: one whole function, or a named range inside one.
3. Load the executable's sections into an x64 emulator (for example [Unicorn](https://www.unicorn-engine.org/)) and
   build the inputs the piece reads: a unit record, a table row from the real data file, a few globals. Write down
   every input you invent.
4. Run it. **Never fake a helper's return value**: if the code calls something you haven't set up (a heap, the OS,
   Steam, a manager object), stop there and record it as the boundary of what you checked.
5. Check the result and that the code left the registers and stack as the Windows x64 calling convention requires.
6. Publish the facts (addresses, inputs, outputs, limits), not the decompiled code or the game's bytes.

**macOS tip:** Unicorn 2.1.4 on Apple Silicon crashes with `SIGILL` on its first memory mapping when it can't read
`hw.cachelinesize` (as happens in sandboxed runners): its fallback reads a CPU register that macOS forbids in user
space. Run outside the sandbox, or catch that one signal in your test harness.

What this proves: what that code does with those inputs. What it doesn't prove: that the game reaches it with those
inputs, or what happens in the code around it. Pages say which parts were run and which were only read.

## Sharing what you find
Write function names, addresses (with the executable's hash), and what each function does into the game's
`functions.md`. Don't commit decompiled code ([rules](../CONTRIBUTING.md)).
