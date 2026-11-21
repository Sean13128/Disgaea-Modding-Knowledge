# Your first Disgaea 5 mod (D5 Mod Kit)

Make a new playable character from an existing one and share it. **Built from D5 Mod Kit's documentation; not tested by
this project.** Back up first ([setup and backups](setup-and-backups.md)).

## 1. Install the Kit
Download `D5ModKit.zip` from the [D5 Mod Kit releases](https://github.com/eraser4521/D5ModKit) (it includes its own
Python), unpack it, and open `START HERE - Guide.html`. Point it at a **clean** Steam install of Disgaea 5 Complete:
the Kit refuses a game other tools have already changed. It backs up every file it changes.

## 2. Make a character
On **Characters & Classes**, select a character to use as the template and start the new-character wizard:
- **Template:** the new character copies its row: stats, skills, evilities, Overload.
- **Look:** the template's (with its own copy of the sprite to repaint), or any outfit from an enabled mod.
- **Stats:** keep the template's or apply a preset from a playable character.
- **A mod of its own** (the default): one character per mod, easy to share.
- **Recruit quest:** the Kit adds a quest that creates the unit.

New characters get **unused IDs below 1000** (the game treats 1000+ as generic classes), so existing saves stay valid
([D5 data](../games/mainline/disgaea-5-alliance-of-vengeance/data.md)).

## 3. Shape it
- *Copy from another…*: stats, aptitudes, movement, weapon mastery, resistances, evilities, Overload, skills.
- **A skill of its own:** a copy of any skill's row and animation, with an ID next to the character's own.
- **An evility of its own:** its own name and description, but it **works like an existing evility** you choose
  (D5's evility behavior is code keyed by ID; the Kit adds the existing one alongside). Write `%d` for numbers and the
  full-width `％` for percent signs: a plain `%` in a description crashes the Skill Shop
  ([crash causes](../games/mainline/disgaea-5-alliance-of-vengeance/modifiable.md#crash-causes-found-in-practice)).
- **An Overload:** Skills & Animations → New Overload.

## 4. Install and test
Press **Install**, then **restart the game**: tables load at startup. Recruit the character through its quest.

## 5. Share it
- With other Kit users: right-click the mod → **Share (export as .zip)**. On import, clashing IDs are moved and
  remembered, so later updates keep saves valid.
- With players: `tools/export_pack.py` builds one installer `.exe` that installs and uninstalls cleanly
  ([D5 tools](../games/mainline/disgaea-5-alliance-of-vengeance/tools.md)).

## Sources
D5 Mod Kit [README](https://github.com/eraser4521/D5ModKit), [CHARACTER_STUDIO.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/CHARACTER_STUDIO.md), [SHARING.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/SHARING.md).
