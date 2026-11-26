# Disgaea RPG — Engine Functions

A Unity game built with IL2CPP: game code is compiled to native code. In the shipped client the code and the
`il2cpp/Metadata` file are protected, so **the metadata does not give class or field names** (corrected 2026-11-25; an
earlier version of this page said it did). The table field names on [data.md](data.md) come from the data bundles
themselves, and class names of the game's own scripts come from the type information stored inside the bundles
(`CharacterAnimationController`, `CommandAnimationController` and the effect components appear on every unit and
effect prefab).

Out of scope for an online game (see [modifiable.md](modifiable.md)): no addresses are listed and no client behaviour
is documented here beyond what decides how the **art and audio data** are used. Those findings, traced in the client's
code by the D5 Mod Kit development fork's source-format work (**From decomp**; a lead, not a run), are on
[assets.md](assets.md#unit-prefabs-and-animation-2026-11-24-to-11-25). The classes involved:

| Class (Assembly-CSharp) | What it decides |
|---|---|
| `CharacterAnimationController` | The unit's animation trigger, clip events, body-sprite swap, weapon images, voice cue playback, movement to a target |
| `CommandAnimationController` | A skill's effect prefab: its sprite lists, clip events and voice cues (played on the acting unit's bank) |
| `BattleCharacterUnit`, `BattleCharacter` | Finds the slot (`p1`..`p5`, `e1`..`e5`), instantiates the unit's prefab, requests motions |
| `AssetBundleManager`, `ResourceLoader` | Bundle paths for prefabs, atlases, pictures and weapon sprites; colour variants |
| `AudioManager`, `VoicePlayer` | Cue sheet named by the character ID, bank loading, playback, "voice is not loaded" when absent |
