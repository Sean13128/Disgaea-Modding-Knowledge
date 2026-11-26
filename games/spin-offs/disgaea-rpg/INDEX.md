# Disgaea RPG — File Index

- **Version / platform covered:** Japanese Android client (`com.disgaearpg.forwardworks`), version 4.6.2 (code 452,
  minimum Android 24), Unity 2022.3.62f2, client build `c30e3059-56c0-4ca9-b3a7-134eea3bea26` (from `il2cpp/unity.ver`),
  October 2026. The Global client isn't covered
  (its character IDs are a subset of the JP ones)
- **Install / root path:** the APKs, plus the app data folder `Android/data/com.disgaearpg.forwardworks/`
- **Folder tree with sizes:** [layout-android-jp.txt](layout-android-jp.txt)

**Scope:** Disgaea RPG is an online game. These pages document its data and art for reference and for use in other
games' mods. Changing the live client or its traffic isn't covered.

## File map

| Path | Type / format | Contents | Notes |
|---|---|---|---|
| `base.apk`, `split_config.*.apk` | Android packages | Unity game, IL2CPP build | |
| app data `files/assetbundle/` | Unity AssetBundles | Downloaded assets: art, data tables, text | Most content downloads after install |
| `files/assetbundle/masters/` | 267 AssetBundles (21 MB) | **Data tables** ("masters"), one per bundle | [data.md](data.md) |
| `files/assetbundle/gametext`, `text` | AssetBundles | Game text | |
| `atlas/chara/battle/{front,back,wait_front,wait_back}` | AssetBundles | Character battle sprite sheets (four per look) | |
| `images/chara` | AssetBundles | Faces (`face`), cut-ins (`ci`), standing illustrations (`illust`), command-menu crops (`command`) | [assets.md](assets.md) |
| `prefabs/characters/<look>` | AssetBundles (1,011) | The unit: a canvas of UI Images plus its Animator controller and 9 to 42 clips | [assets.md](assets.md#unit-prefabs-and-animation-2026-11-24-to-11-25) |
| `prefabs/effect/command/{front,back}/command<N>` | AssetBundles | Skill effect prefabs, with voice and sound cues | |
| `atlas/weapon/<type>/<id>`, `atlas/chara/story/<look>` | AssetBundles | Weapon sprites (374 bundles); story art | |
| `assetBundleList.txt` | gzip-compressed JSON | 55,427 bundle records | |
| `assetBundleConfig.txt`, `assetbundleManifest` | Lists | Bundle dependencies | |
| `files/il2cpp/` | IL2CPP metadata | Encrypted in this build, so it doesn't give class or field names (corrected 2026-11-25; the table field names come from the data bundles) | |
| ACB cue sheets | CRI ADX2 audio | Voices | Cue names are readable (`voice_attack`, `voice_damage`, `voice_spNN` …); see [assets](assets.md) |

## Archives & containers

Unity AssetBundles, read with [UnityPy](https://github.com/K0lb3/UnityPy) or [AssetStudio](https://github.com/Perfare/AssetStudio).
Each master bundle holds one `MonoBehaviour` whose `DataList` is the table: a list of rows with named fields.

```python
env = UnityPy.load(path)
mono = next(o for o in env.objects if o.type.name == "MonoBehaviour")
rows = mono.parse_as_dict()["DataList"]
```

## Data tables
See [data.md](data.md).

## Sources
This project's Disgaea RPG table and art extraction reports (2026-10-27, 2026-11-03). **Verified offline.**
2026-11-25 additions (version, prefab and effect-prefab rows): this project's source-format study of the JP client. **Verified offline.**
