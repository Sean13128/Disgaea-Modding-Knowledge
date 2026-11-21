# Disgaea PC — Script command reference

Every command of Disgaea PC's two script languages with its argument layout, as pg_disatools' script test runner
(`DisaScriptTest`, `PG_DisaScriptExecutor.cpp`) reads them. What the languages do and how scripts are chosen:
[scripts.md](scripts.md).

**Evidence:** **Community-reported** (pg_disatools source by ProgSys, LGPL-3.0, read 2026-11-04). Argument names are
pg_disatools'. Little-endian; `s16` = signed 16-bit, `u24` = 3-byte unsigned. Sizes are argument bytes, not counting the
opcode (and, in SCRIPT, the size byte).

## SCRIPT.DAT

Each command is `opcode, size byte, arguments`. The game advances by a size it knows for each command, not by the size
byte; the two only disagree for `D0`. Slots are explained on [scripts.md](scripts.md#scriptdat).

| Op | Name | Arguments (in order) |
|---|---|---|
| `00` | Error | — The game reads no body and stops on it |
| `01` | Sleep | ticks (u16) · 2 B |
| `02` | Return | — |
| `03` | RepeatStart | start (s16), end (s16) · 4 B |
| `04` | RepeatEnd | — |
| `05` | LockUser | — |
| `06` | UnlockUser | — |
| `07` | If | — Variable: see [If](scripts.md#scriptdat) |
| `08` | SetFlag | flagType (u8), flag (u16), mode (u8), valueType (u8), value (u16) · 7 B |
| `09` | Nop | — |
| `0A` | SetFade | opacity (s8), duration (u16) · 3 B |
| `0B` | SleepSkippable | ticks (u16) · 2 B |
| `0C` | ShakeScreen | unused (u8), strength (u8), duration (u16) · 4 B |
| `0E` | SetFadeColor | r (u8), g (u8), b (u8) · 3 B |
| `11` | LoadTalkScript | scriptId (u24), dungeonId (u16), flag (u16) · 7 B |
| `12` | RunScript | scriptId (u24) · 3 B |
| `15` | SetDungeon | dungeonId (u16) · 2 B |
| `16` | EnterDungeon | mode (s16) · 2 B |
| `17` | LoadDungeonGeometry | dungeonId (u16) · 2 B |
| `18` | WaitForMapLoad | — |
| `1B` | SetScreenColor | duration (u16) · 2 B |
| `1C` | SetActorRawPos | slot (u16), x (s16), y (s16), depth (u24) · 9 B |
| `23` | ClearActorData | slot (u16) · 2 B |
| `24` | SetRenderGroup | group (u16), duration (u16), r (u8), g (u8), b (u8), a (u8), rotX (s16), rotY (s16), rotZ (s16) · 14 B |
| `27` | SetCursorPos | parent (u16), x (s16), z (s16), duration (u16) · 8 B |
| `28` | SetCameraMode | mode (u8) · 1 B |
| `29` | SetCameraZoom | duration (u16), distance (s16) · 4 B |
| `2A` | RotateCamera | duration (u16), rotX (s16), rotY (s16), rotZ (s16) · 8 B |
| `2B` | SetCameraFOV | duration (u16), value (u16) · 4 B |
| `2C` | SetCameraPos | parent (u16), x (s16), y (s16), z (s16), duration (u16) · 10 B |
| `2D` | SetActorGravity | slot (u16), gravity (s16) · 4 B |
| `2E` | SetCameraRotation | duration (u16), rotX (s16), rotY (s16), rotZ (s16) · 8 B |
| `32` | Text | — Variable (text) |
| `33` | ClearText | — |
| `34` | WaitForUser | — |
| `37` | ShowText | mode (u8), startY (u8) · 2 B |
| `38` | ResetTextScroll | — |
| `39` | LoadPortrait | slot (u8), imageId (u16) · 3 B |
| `3A` | ShowPortrait | slot (u8) · 1 B |
| `46` | SetUnitField | characterId (u16), mode (u8), value (s16) · 5 B |
| `4E` | SpawnPartyMember | slot (u8), characterId (u16) · 3 B |
| `4F` | AddPartyMember | characterId (s16), level (u16) · 4 B |
| `50` | AddMoney | amount (s32) · 4 B |
| `51` | GiveItem | itemId (u16), level (s16) · 4 B |
| `53` | RequestTalk | index (u16) · 2 B |
| `54` | WaitForFlag | flag (u16) · 2 B |
| `55` | WaitForPortrait | — |
| `5A` | LoadData | loader (u8), entry (u8) · 2 B |
| `5C` | SetTextColor | r (u16), g (u16), b (u16), a (u16), duration (u16) · 10 B |
| `61` | RunScriptOnActor | scriptId (u24), slot (u16) · 5 B |
| `64` | MoveActor | slot (u16), parent (u16), x (s16), y (s16), z (s16), duration (u16), mode (u8) · 13 B |
| `65` | SetActorPos | slot (u16), parent (u16), x (s16), y (s16), z (s16), duration (u16), mode (u8) · 13 B |
| `66` | SetActorAnimation | slot (u16), animation (s16) · 4 B |
| `67` | SetActorAnimationSet | slot (u16), animationSet (u16), facing (u8) · 5 B |
| `68` | ContinueActorAnimation | slot (u16) · 2 B |
| `69` | SetActorGroup | slot (u16), group (u8) · 3 B |
| `6A` | SetActorPoseDir | slot (u16), animationBase (u16), facing (u8) · 5 B |
| `6B` | SetActorAnimationMode | slot (u16), mode (u8) · 3 B |
| `6D` | SetActor | slot (u8), characterId (s16), level (u16), allegiance (u8) · 6 B |
| `6E` | SetActorSprite | slot (u16), characterId (s16) · 4 B |
| `6F` | PlayEffect | effectId (u16), parent (u16), x (s16), y (s16), z (s16), scale (u16), speed (u16), group (u16) · 16 B |
| `70` | SetMapTile | parent (u8), x (s16), z (s16), type (u8), value (s16) · 8 B |
| `71` | PlayAimedEffect | effectId (u16), source (u16), x (s16), y (s16), z (s16), scale (u16), speed (u16), group (u16) · 16 B |
| `72` | PlayEffectEx | effectId (u16), parent (u16), x (s16), y (s16), z (s16), scale (u16), speed (u16), group (u16), extraA (u16), extraB (u16) · 20 B |
| `73` | SetActorRotation | slot (u8), duration (u16), rotation (s16) · 5 B |
| `74` | PlayAimedEffectEx | effectId (u16), source (u16), x (s16), y (s16), z (s16), scale (u16), speed (u16), group (u8), extraA (u16), extraB (u16) · 19 B |
| `77` | OrbitActor | slot (u16), duration (u16), center (u16), x (s16), z (s16), y (s16), angle (s16) · 14 B |
| `78` | DoAction | slot (u8), action (u8) · 2 B |
| `79` | SetActorFlag | slot (u16), value (u8) · 3 B |
| `7A` | SetMultRGBA | slot (u16), r (u16), g (u16), b (u16), a (u16), duration (u16) · 12 B |
| `7B` | SetActorPalette | slot (u16), palette (u8), variant (u8) · 4 B |
| `82` | LoadStream | streamId (u16) · 2 B |
| `83` | PlayStream | streamId (u16), volume (u8) · 3 B |
| `87` | PlayVoice | voiceId (u16), channel (u8) · 3 B |
| `88` | StopVoice | characterId (u16), value (u8) · 3 B |
| `8C` | PlaySound | soundId (u16), volume (u8), frequency (u16) · 5 B |
| `8D` | FadeSoundVolume | target (u8), duration (u16) · 3 B |
| `91` | PlayBGM | bgmId (u8), volume (u8) · 2 B |
| `92` | FadeBGM | volume (u8), duration (u16) · 3 B |
| `93` | PlayBGMDirect | bgmId (u16) · 2 B |
| `94` | PlayBGMWait | bgmId (u16), volume (u16) · 4 B |
| `95` | PlayBGMAt | bgmId (u16), offsetMs (u24) · 5 B |
| `98` | SetUnitScript | unit (u16), scriptId (u24) · 5 B |
| `C8` | RunFunction | function (u16), value (u24) · 5 B |
| `C9` | SetSceneMode | mode (u16) · 2 B |
| `CA` | SetActorFlag2 | slot (u16), value (u8) · 3 B |
| `CB` | SetActorDepth | slot (u16), depth (s32) · 6 B |
| `CC` | PlayEffectId | handle (u16), effectId (u16), parent (u16), x (s16), y (s16), z (s16), scale (u16), speed (u16), group (u16) · 18 B |
| `CD` | PlayAimedEffectId | handle (u16), effectId (u16), source (u16), x (s16), y (s16), z (s16), scale (u16), speed (u16), group (u16) · 18 B |
| `CE` | PlayEffectExId | handle (u16), effectId (u16), parent (u16), x (s16), y (s16), z (s16), scale (u16), speed (u16), group (u16), extraA (u16), extraB (u16) · 22 B |
| `CF` | PlayAimedEffectExId | handle (u16), effectId (u16), source (u16), x (s16), y (s16), z (s16), scale (u16), speed (u16), group (u8), extraA (u16), extraB (u16) · 21 B |
| `D0` | SetEffectParam | handle (u16), value (u8) · 3 B **The stored size byte says 6, the game reads 3**: trust 3 |
| `D1` | SetEffectValue | handle (u16), value (s32) · 6 B |

## TALK.DAT

Each command is `opcode, arguments`, with no size byte.

| Op | Name | Arguments | Size |
|---|---|---|---|
| `01` | Dialog | — | variable |
| `02` | Confirm | — | 0 B |
| `03` | ConfirmClose | — | 0 B |
| `04` | CharLeft | slot (s16), animation (u16), modifier (u8) | 5 B |
| `05` | Portrait | portraitId (s16) | 2 B |
| `06` | Voice | voiceId (u16) | 2 B |
| `07` | SpeechBubble | type (u8), arrow (u8) | 2 B |
| `08` | NameLabel | nameId (s16), position (u8) | 3 B |
| `09` | Set | flag (u16) | 2 B |
| `0A` | Clear | flag (u16) | 2 B |
| `0B` | Script | scriptId (u24) | 3 B |
| `0C` | Call | talkScriptId (u24) | 3 B |
| `0D` | ScriptCustom | — | variable |
| `0E` | CharMid | slot (s16), animation (u16), modifier (u8) | 5 B |
| `0F` | CallCustom | — | variable |
| `14` | MenuEntry | — | variable |
| `18` | CharRight | slot (s16), animation (u16), modifier (u8) | 5 B |
| `64` | LoadImg | value (u24) | 3 B |
| `65` | ConfirmAuto | delay (u16), delayAlt (u16), keepOpen (u8) | 5 B |
| `C9` | ShowBox | show (u8) | 1 B |
| `FA` | Nop | skipped (u8) | 1 B |
| `FD` | MenuEnd | — | 0 B |
| `FE` | Restart | — | 0 B |
| `FF` | Return | — | 0 B |

`0D` and `0F` appear in pg_disatools' table with names only. `14` is both MenuStart (with an entry count) and
MenuEntry; see [scripts.md](scripts.md#talkdat).

## Sources
[pg_disatools](https://github.com/ProgSys/pg_disatools) source: `src/executables/DisaScriptTest/Script/PG_DisaScriptExecutor.cpp`
(`scriptCommands()`, `talkCommands()`), `src/modules/Disa/Disa/PG_DisaScriptsModel.cpp`.
