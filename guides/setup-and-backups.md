# Setting up safely: clean installs and backups

Applies to every PC Disgaea game. **Not tested by this project as a procedure**; it collects the advice on the game pages.

1. **Start from a clean install.** In Steam: right-click the game → Properties → Installed Files → *Verify integrity of
   game files*. This also undoes mods that replaced game files.
   To *check* an install is clean, compare it with the hash list on the game's INDEX page (D1 PC, D2 PC, D4, D6 and
   D7 have one). For Disgaea 5, whose installs usually carry mod changes, Steam can download a specific clean build:
   open the Steam console (`steam://open/console`) and run `download_depot 803600 803601 2485590530922474185`
   (build 3528766, the build this wiki documents; see the [D5 INDEX](../games/mainline/disgaea-5-alliance-of-vengeance/INDEX.md)).
2. **Back up your saves** before installing any mod, and again before removing one. Locations: [saves](../formats/saves.md).
   Mods that add characters or items leave them in your saves; removing the mod can break those saves.
3. **Back up the files a mod replaces**, if it replaces game files directly (e.g. `Database_0.dat`, `SUBDATA.DAT`, the
   executable). Loader-based mods (Makai Kit, proxy DLLs) don't touch game files: remove the DLL to uninstall.
4. **One mod manager per game.** Don't mix a mod manager's installs with hand-replaced files; the manager may refuse a
   modified game (D5 Mod Kit does) or overwrite your changes.
5. **After a game update**, Steam puts vanilla files back. Reinstall mods, and check that each still supports the new
   build (D5's two builds have different code addresses: [D5 functions](../games/mainline/disgaea-5-alliance-of-vengeance/functions.md#which-build-an-address-belongs-to)).
6. **Turn off Steam Cloud** for the game while experimenting, so a broken save doesn't sync over a good one.
