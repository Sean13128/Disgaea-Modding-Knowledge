# Disgaea 7 — Assets

**Evidence:** model, texture and voice formats from this project's readers ([NMB / NMA](../../../formats/nmb-models.md),
[NLSD](../../../formats/nlsd.md)); archive names from the install.

## Formats

| Asset | Format | Where |
|---|---|---|
| **3D models** | `.nmbm` (rig / model), `.nmbs` (animation set), `.nmba` (animations) ([format](../../../formats/nmb-models.md)); [FAD / FANM](../../../formats/fad.md) parts compose body and expression layers | `data/model_1_00.dat`, e.g. `model/character/<folder>/<id>/rig_<id>.nmbm` |
| **Textures** | `.nltx` ([header](../../../formats/nmb-models.md#textures-nltx)) | `data/texture_1_00.dat` |
| **Faces, icons** | Atlas crops (`icon` table) of textures | `texture_1_00.dat` |
| **Maps** | Not documented here yet | `data/map_1_00.dat` (FAFULLFS, uncompressed) |
| **Voices** | `.nlsd` | `data/voice_1_00.dat`, `voice/en|jp/` |
| **Music, sound effects** | Archives | `bgm_1_00.dat`, `se_1_00.dat` |
| **Text** | Archive | `msg_1_00.dat` |
| **Skill effects** | Model and texture files under `model/act/` | `model_1_00.dat` |
| **Skill / event logic** | Lua (`.lua`) | `data/script/` |

## Compatibility with other games

| From | Asset | Works in D7? | Evidence |
|---|---|---|---|
| **Disgaea 6** | Character models, animations, skill effects, voices, Lua skill scripts | **Unknown** | The two games share model formats ([NMB / NMA](../../../formats/nmb-models.md)). Overlord Edition's author listed D6 character ports as a goal and warned file formats might prevent it (**Community-reported**: [D7 beta post](https://reddit.com/r/Disgaea/comments/1q2dj5n/disgaea_7_overlord_edition_mod_beta/), 2026-01-02) |
| **Disgaea 1–5** | 2D sprites | Not directly | Earlier games use 2D sprites; D7 uses 3D models. Conversion isn't known to have been done |

## Open questions

- `.nmbs` (model sets) and the remaining chunk types. Models, animations and textures are mapped on
  [NMB / NMA](../../../formats/nmb-models.md); `.nlsd` on [NLSD](../../../formats/nlsd.md).
- Whether D6 models need any conversion beyond new records.

## Sources

- This project's model and voice readers (see the format pages).
- [D7 beta post](https://reddit.com/r/Disgaea/comments/1q2dj5n/disgaea_7_overlord_edition_mod_beta/) by Cryswar (Aryuto on Reddit).
