# Disgaea 3: Absence of Justice — File Index

Releases: PS3 (2008) and PS Vita *Absence of Detention* (2012). No PC version.

- **Version / platform covered:** Disgaea 3: Absence of Detention, PS Vita (PCSE00022), as installed in the Vita3K emulator. The PS3 original isn't covered
- **Install / root path:** `app/PCSE00022/`
- **Folder tree with sizes:** [layout-vita-absence-of-detention.txt](layout-vita-absence-of-detention.txt)

## File map

| Path | Type / format | Contents | Notes |
|---|---|---|---|
| `eboot.bin` | Vita executable | | |
| `Data/START.dat` | NISPACK (little-endian) | `database.lzs` (another NISPACK, holding `char.dat`), `WF.dat` (145 face textures) | |
| `Data/CI.dat` | Archive (encrypted) | Illustrations as ANM (382 members) | |
| `Data/BU.dat` | Archive (encrypted) | Story picture atlases | |
| `Data/ANM.dat` | NISPACK | Sprites: `anm0000.lzs` (326 resources), `anm0001.lzs` (87); several bodies per member | |
| `Data/MAP.dat`, `BG.dat` | Archives | Maps, backgrounds | |
| `Data/sound/soundPack*` | Sound packs | `Bgm`, `Se`, `MsgEN`/`MsgJP`, `VoiceEN`/`VoiceJP` | |
| `Data/NisAnmShadow.txf`, `NisGraphics_Tool_Nagakute.txf` | TXF textures | Sprite shadow; a tool texture | |
| `Data/dis3.mp4` | MP4 | Movie | |
| `Data/savedata/` | | Saves | |
| `sce_module/`, `sce_sys/` | System | Libraries, LiveArea, manual, trophies | |

**Encryption:** on a Vita dump the files are **PFS-encrypted**; reading `Data/ANM.dat` directly gives encrypted bytes. Vita3K's
[psvpfsparser](https://github.com/Vita3K/psvpfsparser), with your copy's own license, decrypts them. Decrypted, they
are ordinary NISPACK archives.

Disgaea 4's opening movie file is still named `dis3_0.dat`: D4 appears to be built on D3's code (TCRF).

## Save files
`Data/savedata/`. Format not documented. A D3 Vita save lets Disgaea 4: A Promise Revisited recruit two extra characters.

## Sources
File listing and format notes: this project's scan and classic-art readers (2026-11-03) ([method](../../../CONTRIBUTING.md#file-listings)).
