# Disgaea RPG — Assets

| Asset | Where | Notes |
|---|---|---|
| Battle sprites | `atlas/chara/battle/{front,back,wait_front,wait_back}` | Four sprite atlases per look; D5's generic classes are drawn the same way |
| Faces | `images/chara` (`face`) | |
| Cut-ins | `images/chara` (`ci`) | 205 of the 216 looks used by the D5 importer have one; the other 11 have none in the game |
| Voices | CRI ADX2 cue/waveform banks (`.acb` + `.awb`; the Windows Global build ships them as `*_acb.bytes` / `*_awb.bytes`). Sampled clips are **HCA** (mono, 48 kHz or 22.05 kHz) | The asset list has 2,815 `sounds/` records, 2,289 of them `sounds/voice/` (1,999 battle, 208 story, 82 preview). Cue names are plain text in every bank checked (2026-11-25; an earlier note that only `voice_attack` was readable was wrong); no table maps characters to banks, the bank folder is the character ID |

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
- **Cue sheet details (2026-11-25, Verified offline; two independent decoders agree on cue resolution):** the ACB is CRI
  `@UTF` (version 1.32 in 321 tracked banks, 1.31 in 16), waveforms are Streaming with EncodeType 2 (HCA); the AWB is
  AFS2 v2.4.2; HCA is v2.0 with cipher type 0 (no key), mono except looks 129, 342, 355, 371, 372 and 374 (stereo),
  at 48,000, 22,050, 44,100 or 16,000 Hz. A cue's sequence is type 0 (plays every track, each with an optional start
  delay from command `0x07D1`, 130 cues), type 3 (random over 2 to 4 tracks) or, three times, type 1; the track event
  is command `0x07D0`. `voice_attack`, `voice_h_attack` and `voice_magic` are two- or three-take random sequences in most
  banks; the random weights belong to the CRI runtime and weren't decoded. Core cues in every tracked bank:
  `voice_attack`, `voice_damage`, `voice_dead`, `voice_get`, `voice_glad`, `voice_h_attack`, `voice_magic`,
  `voice_recover`, `voice_turn`, plus `voice_sp01` to `voice_sp22` (a few banks write `voice_sp010` to `sp017` with a
  zero). 53 cues in 25 looks are silent (an empty track command), and the wave's recorded sample count exceeds the HCA
  content for 531 waves in 74 looks (128 trailing samples in the one wave tested).
- **Which cue plays when (Verified offline in the clips, From decomp for the playing side; proven):** animation events
  named `VoicePlay` carry the cue name. Every weapon attack clip and the lift and throw clips play `voice_attack`
  (313 of 313 and 359 of 359); `magic_start` and `angry` play `voice_magic`; `damage` plays `voice_damage`; `dead` plays
  `voice_dead` (357 of 360; three `front_dead` clips have none); walk, wait, jump, `magic_end` and the feed states play
  nothing. Skill voices live in the skill's effect prefab, `prefabs/effect/command/{front,back}/command<N>`, where `N` is
  the `command` master's `resource_id` (2,242 of 2,243 distinct non-zero IDs exist; 0, in 1,482 commands, means no
  effect prefab); 5,314 of 5,670 effect prefabs carry `VoicePlay` events (`voice_attack` 2,907, `voice_h_attack` 2,433,
  `voice_magic` 1,631, `voice_sp01` 1,475, `voice_turn` 733, `voice_glad` 497, `voice_get` 398, `voice_recover` 326
  and more). The client plays a cue on the **acting unit's** bank (the bank's folder is the character ID), loads it
  with the battle (`sounds/voice/battle/<id>/<id>_acb` and `_awb`), makes no random choice of its own, and, if the bank
  isn't loaded, logs a warning and plays nothing (no fallback bank or cue). `voice_glad` is played on victory and
  `voice_get` from the gacha. Which skills use which `voice_spNN` is only knowable per effect prefab. Japanese only.
- Some monster frames draw a small companion piece apart from the body (a floating orb); cutting sprites by connected
  region splits it off, so front and back region counts stop matching.

## Cut-ins and portraits (2026-11-12 to 11-14, Verified offline)

- **Where they live:** `assetbundle/images/chara/ci/<look id>` is one 260 × 836 Texture2D with a white background.
  Siblings in the same folder family: `chara/face/<id>` 200 × 200, `chara/illust/<id>` 640 × 960 (full-body standing
  art), `chara/command/<id>` 412 × 440 (battle command-menu crop). Looks 60019 and 60021 (the two Colossal Prinny
  colours), 60025 (Dark Lord Valvolga) and 426 (a Halloween costume of Higan) have **no `ci`** in the client list or on
  disk; all four have an `illust`, `command` and `face`. The client's picture loaders have **no fallback** (2026-11-25, **From decomp**): each requests
  `images/chara/<kind>/<id>` and sets no size or crop. What a screen shows when the file is absent is **Unknown**.
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

## Unit prefabs and animation (2026-11-24 to 11-25)

Status labels: **Verified offline** = read from the bundles; **From decomp** = also traced in the client's code, which
is protected, so no addresses or method are given here (a lead, not a run); "proven" below means the project's reader
agrees with the traced code and fails when the statement is broken. **Reading** = inferred. Unity 2022.3.62f2, client
4.6.2.

- **Identity.** One integer is the character, the look, the prefab and every file stem (proven). `masters/character`
  has 952 rows (ID ranges 1 to 423, 1001 to 1010, then 10000s, 20001+, 21001+, 30001+, 40001+, 50001+, 60001+,
  70001); 946 have a prefab and atlases, 951 a voice bank. Other masters (`enemy`, 149,362 rows over 800 IDs,
  `guestcharacter`, `charactersubinfo`) point at the same ID; there is no model or costume ID.
- **Prefab** `prefabs/characters/<look>` (1,011 in the client, 19 structures): a 256 × 256 canvas root whose children
  are UI Images: `back_parts`, `weapon_back`, `chara_img` (the body, 170 × 180 in 556 prefabs, 170 × 170 in 180),
  `shadow`, `weapon_front`, `arrow`, effect objects and `front_hand`. A clip animates each Image's sprite (pointer),
  anchored position, size, scale, Z rotation, active flag, enabled flag and alpha. Curve bindings are the CRC-32 of the
  path and of the property; keys are Unity streamed clips (of about 9.9 million keys, 8.92 M are flat, 265 k spline,
  83 k linear; 34,721 clips decode with no error). Drawing order is sibling order under a world-space canvas
  (override sorting, order 0 in 1,008 of 1,011): weapon_back, chara_img, shadow, weapon_front, arrow, shot effects. The
  shadow sits after the body, which would draw it over the feet; why that looks right is unexplained.
- **Controller** `Battle_Chara`: 42 states, one trigger parameter per state, one any-state transition per trigger (no
  blend), default state `blank`, which exits to `front_wait` at normalised time 1.0. 313 of the 360 looks the project
  tracks use the 42-state controller, 41 the monster one (28 to 31 clips: `front_attack`, `back_attack`, `angry`, fewer
  lifts), a few specials 9 to 10 clips. States: `wait`, `walk`, `jump`, `damage`, `dead`, `magic_start`/`magic_end`,
  `<fist|sword|spear|ax|arrow|wand|gun>_atk`, the lift family (`lift`, `lift_wait`, `lift_top`, `lift_while`, feed
  in/out), `throw`, `throw_landing`, `jump_out`, each with `front_` and `back_` forms. Attack, damage, `magic_end` and
  throw exit to the matching wait state; jump, walk, dead, `magic_start` and the lifts don't. There is no victory clip.
- **Timing** (**Verified offline**, reading): every clip has sample rate 60, stop times that are multiples of 1/60 s, and
  loops only for wait and walk. Median stop times: wait 0.85 s (0.68 to 1.68), walk 0.517, weapon attacks 1.767 (arrow
  1.017 front, 0.85 back; gun 1.017), damage 0.85 to 0.9, dead 0.833 (front up to 2.0), magic start 0.18. The client
  runs at 30 fps, or 60 with the player's high-frame-rate option, and the battle-speed option changes the engine time
  scale; unit code never sets the animator speed.
- **Which trigger the client fires** (**From decomp**, proven): every unit animation request goes through one
  `SetAnimation(action)` method. The trigger name is the action's base name prefixed `back_` for player units and
  `front_` for enemies. JUMP always fires `front_jump`, also for player units; ANGRY fires the unprefixed `angry`
  (only looks 60019, 60021 and 60025 have it); an attack fires `<weapon>_atk` by the weapon type, `fist_atk` for an
  empty hand or knuckle, and `attack` for a monster; an unknown weapon type fires nothing.
  **`back_jump`** exists in 359 looks but the standard action path never requests it; JUMP is requested for living
  player units on wave clear, victory (with a looping variant), resurrection and as a skill-target hop. Throw and union
  attacks set other triggers (`back_jump_s`, `back_jump_l`) on animators that were not traced. Computed trigger names
  and timeline-driven paths weren't checked, so "never shown" would be too broad.
- **Clip events** call same-named methods on the unit's controller (18 names, all with a handler):
  `StartMoveToTarget(n)` (n frames at 60 per second; the data has 20, 25 and 30) tweens the prefab instance to the
  target with an offset of (−46, −46) for players and (+50, +50) for enemies, `StartMoveToDefault` returns it to the
  slot origin, `EndDamage`, `FinishAnimation`, `EndJump`, `AttackEffectPlay`, `DeadEffectPlay`, `TargetLiftUp`,
  `DispDamage`, `SEPlay(name)` and `VoicePlay(cue)`. One data anomaly: look 346's `front_wait` carries an event with an
  empty function name.
- **Placement** (**From decomp**, proven, plus **Verified offline**): the battle scene has slots `p1`..`p5` and
  `e1`..`e5`, each 256 × 256 at scale 1. The unit's prefab is instantiated under its slot at **local position 0,
  scale 1, identity rotation**; the prefab's authored root position, rotation and scale are discarded. 19 of 398
  hierarchy entries (18 looks) author a nonzero root position, and those equal battle-slot positions (for example
  (77, 183) is slot `e1`), so they look like the spot each prefab was authored at; no clip animates the root.
  Movement and targeting use the slot origin as the reference. The master `character.pos_x`, `pos_y`, `scale_x`,
  `scale_y` arrays have no reader among the traced methods, so **the data and the traced code give no unit scale
  and no mirror**: facing is a clip choice (`back_*` for players, `front_*` for enemies), not a flipped image.
- **Ground:** no foot marker exists in the slots or prefabs (a literal scan of 22,089 client strings found no battle
  shadow, foot or ground lookup). The `shadow` Image (anchored y −74, size 200 × 100 in 99% of looks) is the only
  ground-like object, and clips can move it (386 of 16,130 decoded clips change its position or size). A calibration
  against Disgaea 5 art that is the same drawing in both games (**Verified offline**, reading) puts the D5 ground
  point **76 px below the slot origin** (x −1 in the front view, −3 in the mirrored back view; 21 humanoid looks, spread
  1 px), 2 px below the shadow. Looks with a displaced shadow are 4.5 to 6.7 px off the shadow while the art stays
  where it is, so the shadow is a close proxy for humanoids but doesn't mark the feet. Beasts and flyers share no
  constant.
- **Sprite swap** (**From decomp**, proven): every frame the controller swaps the sprite of the body Image only: it
  takes the unit's own atlas (`{back|front}<id>`) by the current texture name, or the wait atlas when the sprite name
  contains `wait`, and uses a same-named sprite by dictionary lookup (none when absent). Every other Image draws its
  literal sprite. Of 91,143 sprite switches in the 360 tracked looks, 25,692 point into the unit's own atlases and
  65,451 into template atlases (`front1`/`back1` mostly), in 333 of 360 looks, so most clips name a template sprite
  that the game replaces by name with the unit's own. The battle unit never uses the `*_inversion` atlases (24 looks
  have them; master `inversion_type` is 1 for 21 rows and 2 for two); skill effects use them only for
  `inversion_type` 2 with an image flag that is 0 in all 650 effect prefabs that have it. `<id>_<n>` prefabs (59, for
  56 rarity-4 characters) are colour variations, loaded when the unit's colour variation is above 0. The game
  substituting another clip when one is missing (a unit without back clips) is **Unknown**.
- **Weapons:** unit prefabs hold weapon Images (`sword` and `sword_handle`, `spear`, `ax`, `arrow`, `wand`, `gun_*`)
  and the controller holds per-weapon image lists (8 to 16 entries). The art comes from
  `atlas/weapon/<weapon type>/<visual weapon id>` (374 bundles, sprites `<type>_0` to `<type>_3`), assigned once at
  setup by the current sprite's name; a clip's own sprite curve on a weapon Image overrides it while it plays.
  Placement, angle and movement come from the prefab hierarchy and clip curves, not the assignment. Monster prefabs have
  no weapon layers.
- **Battle atlases:** `atlas/chara/battle/{front,back,wait_front,wait_back}/{front|back}<look>`: one Texture2D in ASTC
  6 × 6 (Unity format 50; 1024 × 1024 for 351 of 360 looks) and its Sprite objects (front about 52, back 48, wait 5 to
  11). Sprites have integer rectangles, pixels per unit 100 and centre pivots, except 36, 36, 171 and 101 sprites
  (pivot (0, 1) with an offset, mostly wait atlases). Atlases are straight alpha (**reading**: one atlas checked: 27%
  of fully transparent pixels carry colour). About 34 sprites per humanoid atlas are used by no unit clip.
- **Story atlas:** `atlas/chara/story/<look>` holds 1 to 12 sprites (460 × 680 up to 2048 × 2048) for 306 of 360 tracked
  looks; where each is shown is UI code that wasn't traced.

### Sprite names

The atlas sprites carry the developer's own pose names: `stand`, `walk01` to `walk06`, `damage`, `squat`, `dash`,
`take_out`, `one_arm_up`, `two_arm_up_jump02`/`03`, `landing02`, `launch02` to `04`, `attack_fist`/`sword`/`lance`/
`arrow`/`gun`/`ax` `02` to `04`, `rolling01` to `03`, and so on, and 127 of 170 looks the project's reader covers
(75%) share one 48-front, 43-back slot layout (the monsters and a few others don't). Because Disgaea 5's and Disgaea 1
Complete's shared humanoid motion sets draw the same pictures at identical rectangles, each of their cells can take
the RPG name: all 120 distinct cells of D1C's shared block match D5 cells, and 122 of D5's 129 block-1 cells map to a
named RPG slot. Disgaea 4 and Disgaea D2 draw their own art here, so only 1 of 140 (D4) and 1 of 544 (D2) cells match
and no such naming is possible. These are the only pose names the project found in any Disgaea file it could read ([motion names](../../../formats/motion-names.md)). **Verified offline.**

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

**Decoding:** ffmpeg decodes the sampled HCA clips to WAV (**Verified offline**: two clips). All 360 banks the project tracks parse, use HCA
with cipher type 0 (no key) and decode with a second decoder as well (**Verified offline**, 2026-11-25); the other voice folders
(`story`, `preview`) weren't traced, so whether any bank outside the tracked 360 is encrypted is **Unknown**.

## Sources
2026-11-24 to 11-25 additions (prefabs, animation, placement, ground point, voice routing, sprite names): this project's source-format study of the JP client (bundle reads and traced client code), the D5 Mod Kit development fork's calibration against Disgaea 5 art, and a second-opinion review of the animation trigger rules.
2026-11 additions (cut-ins, rebuild): the D5 Mod Kit development fork's cut-in measurements and importer docs (`docs/SOURCE_PREP.md`).
D5 Mod Kit ([CLASSES.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/CLASSES.md), [MONSTERS.md](https://github.com/eraser4521/D5ModKit/blob/main/docs/MONSTERS.md), [README](https://github.com/eraser4521/D5ModKit)); this project's DRPG mapping, art extraction and voice reports (2026-10); [SteamDB: Global Windows depot](https://steamdb.info/depot/1791621/).
