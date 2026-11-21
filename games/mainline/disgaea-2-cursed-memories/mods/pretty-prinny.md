# Pretty Prinny

- **Author:** Kaldaien · **Games:** Disgaea PC and Disgaea 2 PC · **Version described:** 0.7.x (Disgaea 2 PC)
- **Get it:** [source on GitHub](https://github.com/Kaldaien/PrettyPrinny) (Disgaea PC version; last commit May 2017) · [GamePressure listing](https://www.gamepressure.com/download/disgaea-2-pc-pretty-prinny-v07x-mod/zd10a7d) (Disgaea 2 PC) · **License:** GPL-3.0 (the GitHub source)

This page documents what the mod does and how it interacts with the game. It does not reproduce the mod.

## What it does
Rendering fixes, to bring the PC ports up to the consoles' quality: removes the internal resolution cap (720p on
Disgaea 2 PC), fixes sprite-smoothing artifacts, and on Disgaea PC adds MSAA and SSAO.

## How it loads
Extract its files into the game folder (beside the executable); configure it in `PrettyPrinny.ini`. It works on the
renderer at runtime; game data files aren't changed.

From the Disgaea PC source: Pretty Prinny is a **plug-in for Kaldaien's Special K injector**. The injector sits in the
game folder as a wrapper of the game's OpenGL library (it writes `logs/OpenGL32.log` and `logs/steam_api.log`) and
calls the plug-in's `SKPlugIn_Init`; Pretty Prinny then hooks functions with MinHook. Settings it documents:
`SceneResX` / `SceneResY` (internal resolution; the game renders at 1280 × 720, the default setting is 1920 × 1080),
`RemoveFringing` (sprite edge artifacts), `WrapXInput` (turn off if a controller stops working), and Steam Controller
support through `Pad.cfg`. `UninstallPrettyPrinny.bat` removes it. (**Community-reported**: the source and its README.)

## Compatibility
Works alongside data mods. Graphics-hook tools that hook the same renderer may conflict. On Disgaea PC, pg_disatools'
wiki reports that **Pretty Prinny crashes the game when pg_disatools custom characters are installed**; pg_disatools now
renders at native resolution itself.

## Sources
[GamePressure](https://www.gamepressure.com/download/disgaea-2-pc-pretty-prinny-v07x-mod/zd10a7d), [TweakTown](https://www.tweaktown.com/news/51096/disgaea-pretty-prinny-mod-removes-resolution-cap-adds-msaa-ssao/index.html), [Disgaea PC mod index thread](https://steamcommunity.com/app/405900/discussions/4/371918937281416805/?ctp=27). **Community-reported.**
