# Disgaea RPG — Assets

| Asset | Where | Notes |
|---|---|---|
| Battle sprites | `atlas/chara/battle/{front,back,wait_front,wait_back}` | Four sprite atlases per look; D5's generic classes are drawn the same way |
| Faces | `images/chara` (`face`) | |
| Cut-ins | `images/chara` (`ci`) | 205 of the 216 looks used by the D5 importer have one; the other 11 have none in the game |
| Voices | CRI ADX2 cue/waveform banks (`.acb` + `.awb`; the Windows Global build ships them as `*_acb.bytes` / `*_awb.bytes`). Sampled clips are **HCA** (mono, 48 kHz or 22.05 kHz) | The asset list has 2,815 `sounds/` records, 2,289 of them `sounds/voice/` (1,999 battle, 208 story, 82 preview). Only `voice_attack` cue names are readable; no table maps characters to banks |

Extract with UnityPy or AssetStudio. Sprites are packed atlases with sprite-reference timelines, not D5-style anm files.

## Voice banks in detail (JP Android client, 2026-11, Verified offline)

- Files: `assetbundle/sounds/voice/battle/<id>/<id>_acb` + `<id>_awb`, one pair per character or costume ID. Each is a
  UnityFS bundle (LZ4 blocks) holding one TextAsset: a CRI ACB cue sheet (`@UTF`, big-endian) and an AFS2 wave bank of
  **unencrypted** mono HCA at 22,050 or 48,000 Hz. Japanese only.
- Cue → clips: CueName → Cue (ReferenceType 3) → Sequence.TrackIndex → Track.EventIndex → TrackEvent command 2000
  (reference kind, index) → Synth.ReferenceItems → Waveform.StreamAwbId. A sequence with several tracks is a set of
  alternative takes. Reference kind 7 (3 banks) wasn't read.
- 992 battle banks carry the cues `voice_attack`, `voice_h_attack`, `voice_damage`, `voice_dead`, `voice_magic`,
  `voice_recover`, `voice_turn`, `voice_get`, `voice_glad` and 0–25 `voice_spNN`. Other cue names are birthday, promo
  and shop lines. A take under 0.08 s or silent is the game's placeholder.
- Many of these recordings are the same takes Disgaea 5 uses, which is how D5's voice situations were labelled
  ([D5 assets](../../mainline/disgaea-5-alliance-of-vengeance/assets.md#voice-situations)).
- Some monster frames draw a small companion piece apart from the body (a floating orb); cutting sprites by connected
  region splits it off, so front and back region counts stop matching.

## Cut-ins and portraits (2026-11-12 to 11-14, Verified offline)

- **Where they live:** `assetbundle/images/chara/ci/<look id>` is one 260 × 836 Texture2D with a white background.
  Siblings in the same folder family: `chara/face/<id>` 200 × 200, `chara/illust/<id>` 640 × 960 (full-body standing
  art), `chara/command/<id>` 412 × 440 (battle command-menu crop). Looks 60019 and 60021 (the two Colossal Prinny
  colours), 60025 (Dark Lord Valvolga) and 426 (a Halloween costume of Higan) have **no `ci`** in the client list or on
  disk; all four have an `illust`, `command` and `face`. Whether the game falls back to `illust` where `ci` is missing is
  **Unknown** (no client code was read).
- **A cut-in is a separate drawing, not a crop of the illustration.** Across the 946 looks that have both, only 6
  registered by feature matching (4 of 316 under a relaxed fit); only a prop (look 40005) gave a clean fit (zoom 1.76).
  Measured on all 946: a median of 89.5% of the cut-in is non-white, the figure fills about 92.5% of the frame, ink
  touches the top row and both side columns in every one, and the figure is centred (centroid x 0.51, y 0.50). The
  close-up zoom is estimated at 2.25 cut-in pixels per illustration pixel (a floor; real cut-ins are drawn at higher
  resolution, so one made from a 640 × 960 illustration is softer).
- **Disgaea 5 side:** the D5 Mod Kit development fork builds an own cut-in for those four looks by cutting a
  260:836 window from the illustration at that zoom, top-anchored on the figure's top, then fitting it to the D5
  portrait size (336 × 1080). The framing is weak for Valvolga (the top of the figure is a moon and a wing, so the window
  misses the face).

## Rebuilding Disgaea 5 packs from the client (2026-11-20, Verified offline)

The kit builds 146 base and 89 costume looks from the client files. One input can't be rebuilt from the sources: a
learned humanoid pose reference (an 18 MB file, not in Git, with no rebuild script) that maps the humanoid animation
cells to atlas slots. It is pinned by hash. Without it 4 of 146 packs fail to build (142 build, 139 identical to the
installed ones, 3 sample packs differ by design). **Unknown:** how to regenerate it.

## Compatibility with other games

| To | Asset | Works? | Evidence |
|---|---|---|---|
| **Disgaea 5** | Generic class sprites | Yes, closely: D5's generics and their Disgaea RPG twins are the same drawings at the same size. D5 Mod Kit maps each D5 animation cell to a DRPG atlas slot | **Verified offline (Kit)**: rebuilt D5 classes match the originals frame by frame |
| **Disgaea 5** | Outfits for D5 characters | Yes | **Community-reported**: D5 Mod Kit ships 175 outfits for 26 characters |
| **Disgaea 5** | Monsters | Yes, by pose role: monster atlases are packed by size, so poses are matched by what the engine plays | **Community-reported** (Kit MONSTERS.md) |
| **Disgaea 5** | Skills and passives | Mapped, not ported: of 337 personal skills, 17 exact D5 matches, 23 adapted, 267 lookalike, 30 omitted. Of 824 leader skills, 27 exact evilities, 623 closest | **Community-reported** (project mapping) |
| **Disgaea D2** | Costumes | Yes, in the D2 native port | **Verified in game** (port): two Etna outfits |

The main mismatch: Disgaea RPG passives are **party-wide**; D5 has few map-wide evilities.

**Decoding:** ffmpeg decodes the sampled HCA clips to WAV (**Verified offline**: two clips). Whether every bank is HCA,
and whether any is encrypted, is **Unknown**.

## Sources
2026-11 additions (cut-ins, rebuild): the D5 Mod Kit development fork's cut-in measurements and importer docs (`docs/SOURCE_PREP.md`).
D5 Mod Kit ([CLASSES.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/CLASSES.md), [MONSTERS.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/MONSTERS.md), [README](https://github.com/eraser4521/D5ModKit)); this project's DRPG mapping, art extraction and voice reports (2026-10); [SteamDB: Global Windows depot](https://steamdb.info/depot/1791621/).
