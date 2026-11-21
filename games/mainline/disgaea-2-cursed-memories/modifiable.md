# Disgaea 2: Cursed Memories — What Can Be Modified

Little is documented for Disgaea 2 PC.

| Area | What's known |
|---|---|
| Rendering | Pretty Prinny removes the 720p internal resolution cap and fixes sprite-smoothing artifacts; configured with `PrettyPrinny.ini` (**Community-reported**) |
| Saves | Decodable and re-packable with HenryEx's QuickBMS script |
| Archives, tables, sprites | **Unknown** whether pg_disatools (made for Disgaea PC) handles them; no confirmation found |
| Loose files | The file system has a **bypass path** (`NisFs::SetBypassPath`). By its name, a folder read instead of the archives: a possible loose-file mod route. **Unknown**: untested ([functions.md](functions.md#files-and-archives)) |
| Tables | Binary-searched by ID (keep rows sorted); same database framework as Disgaea 5 PC ([data.md](data.md)) |

## Sources
[GamePressure: Pretty Prinny for D2 PC](https://www.gamepressure.com/download/disgaea-2-pc-pretty-prinny-v07x-mod/zd10a7d), [TweakTown coverage](https://www.tweaktown.com/news/51096/disgaea-pretty-prinny-mod-removes-resolution-cap-adds-msaa-ssao/index.html), [ZenHAX save thread](https://www.zenhax.com/viewtopic.php@t=4698.html).
