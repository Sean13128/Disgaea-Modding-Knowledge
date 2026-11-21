# OBF (3D objects and effects)

**Used by:** Disgaea 5 (map meshes, map objects, effect models; 1,135 files round-trip byte for byte in D5 Mod Kit),
Disgaea 4 (effect models, OBF v2).

- Container of sections; textures inside are TXF (D5) or the legacy `nismultitexform` wrapper (D4).
- D5 Mod Kit reads, edits textures in, and exports OBF to OBJ (`d5lib/obf.py`). Sections 2–6 (hierarchy, motions)
  aren't fully understood.
- D4 effect models convert to D5 by re-encoding their textures (offline only; rendering not accepted yet).

See [D5 assets](../games/mainline/disgaea-5-alliance-of-vengeance/assets.md) and [D4 assets](../games/mainline/disgaea-4-a-promise-unforgotten/assets.md).
