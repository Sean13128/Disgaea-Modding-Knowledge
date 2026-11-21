# Disgaea PC HD sprite mod

- **Author:** a community member (Steam discussion post); exe patch by Maide · **Game:** Disgaea PC (Steam)
- **Get it:** [the Steam post](https://steamcommunity.com/app/405900/discussions/4/1742260837206441731) (Google Drive) · **License:** none stated

This page documents what the mod does and how it interacts with the game. It does not reproduce the mod.

## What it does
- HD sprites for every generic human and monster class, plus Laharl, Etna and Flonne. Named NPCs (Gordon, Thursday,
  Jennifer) and weapons keep their original sprites.
- New colour palettes; monster weapons grant INT.
- Removes the cut-in pictures in double, triple and quadruple attacks.

## How it loads
Replace `SUBDATA.DAT` with the mod's, and `dis1_st.exe` with a patched one. Or keep your own `SUBDATA.DAT` and patch the
mod's `anm00.dat` into it with pg_disatools' File Manager.

## What it touches

| Part of the game | How | Details |
|---|---|---|
| `SUBDATA.DAT` → `anm00.dat` | Replaced member | The sprite data |
| `dis1_st.exe` | Replaced (patched by Maide) | Removes the crash at launch when `SUBDATA.DAT` exceeds **180 MB** |

## Compatibility
Any other mod that replaces `SUBDATA.DAT` or the executable conflicts. Back up both files first.

## Lessons for modders
- `SUBDATA.DAT` had a hard size limit in the executable; a patched exe lifts it.
- pg_disatools can patch one member into your own archive instead of replacing the whole file.

## Sources
[Steam post](https://steamcommunity.com/app/405900/discussions/4/1742260837206441731). **Community-reported.**
