# Disgaea 5 — Where the game checks the generic class ranges

Disgaea 5 decides whether a class is a "generic" human (IDs 1000–1310) or monster (2010–2300) by comparing the class
ID against those bounds at many separate places: Recruiter lists, rank unlocks, sub-classes, mastery, portraits,
status screens and more. A class outside both ranges gets none of that ([what that means](modifiable.md)). Making
the game treat a new ID range as generic means changing **every** one of these checks.

**Build B** (Steam, SHA-256 `49a7530b…db282ce`). **Verified offline**: a scan of the executable for the range-check
instruction patterns (D5 Mod Kit fork scanner, 2026-10). Addresses are where each check starts. The two human lists
describe the same checks two ways (jump-out site vs. immediate operand); a patch uses one or the other, not both.

| Kind | Sites | Addresses |
|---|---:|---|
| Checks of the generic **monster** range (2010–2300) | 62 | `0x1400B00B6`, `0x140143730`, `0x140146894`, `0x1401517A3`, `0x14015C389`, `0x14015DB39`, `0x14015DB94`, `0x14015E7AE`, `0x1401608AE`, `0x140160E45`, `0x1401A5967`, `0x1401A598E`, `0x1401C611F`, `0x1401CCE96`, `0x140217AE7`, `0x140218009`, `0x14021AC9B`, `0x14021B670`, `0x14021BD40`, `0x14021BE67`, `0x14021EE88`, `0x14021F140`, `0x14021F4EF`, `0x14021F82E`, `0x14029A48D`, `0x14029C3BE`, `0x14029D029`, `0x14029F75F`, `0x1402BBE85`, `0x14031234E`, `0x140454D58`, `0x1404550D0`, `0x14045ECAA`, `0x14045ED54`, `0x14046EAEC`, `0x1404725B3`, `0x1404727B1`, `0x1404728F4`, `0x140478A7F`, `0x14047C7F0`, `0x14047D8A5`, `0x14048167F`, `0x14048330F`, `0x1404A8F69`, `0x1404CA434`, `0x1404CA755`, `0x1404CA8FD`, `0x1406510DA`, `0x140652702`, `0x140652848`, `0x140654E3E`, `0x140654F4A`, `0x1406551D8`, `0x140665581`, `0x14066898F`, `0x1406A76EF`, `0x140725F2F`, `0x1407329DE`, `0x1407E5BC3`, `0x140842869`, `0x140851AB5`, `0x140851FF7` |
| Checks of the generic **human** range (1000–1310) made on a register value | 8 | `0x1401517AE`, `0x140160E4F`, `0x1406510E4`, `0x14065270D`, `0x140652853`, `0x140654F56`, `0x1406551E3`, `0x1407329F2` |
| Checks of the human range, as sites where a jump can widen the upper bound past 1310 | 56 | `0x1400B00C3`, `0x14014373D`, `0x1401468A2`, `0x14015C397`, `0x14015DB47`, `0x14015DBA3`, `0x14015E7BF`, `0x1401608BB`, `0x1401A5974`, `0x1401A599B`, `0x1401C612E`, `0x1401C62B4`, `0x1401CCEA7`, `0x140217AF5`, `0x140218016`, `0x14021ACA8`, `0x14021B67D`, `0x14021BD4D`, `0x14021BE74`, `0x14021EE95`, `0x14021F14D`, `0x14021F4FC`, `0x14021F83B`, `0x14029A49B`, `0x14029C3CB`, `0x14029D037`, `0x14029F76C`, `0x1402BBE92`, `0x14031235E`, `0x140454D65`, `0x1404550DD`, `0x14045ECB8`, `0x14045ED62`, `0x14045F430`, `0x14045F46F`, `0x14046EAF9`, `0x1404725C0`, `0x1404727BE`, `0x140472901`, `0x140478A8C`, `0x14047C7FD`, `0x14047D8B2`, `0x14048168C`, `0x140483320`, `0x1404A8F76`, `0x1404CA442`, `0x1404CA763`, `0x1404CA90B`, `0x140654E4C`, `0x14066558E`, `0x1406689A1`, `0x1406A76FC`, `0x140725F3C`, `0x1407E5BD0`, `0x140851AC3`, `0x140852005` |
| The same human-range checks, as the immediate operand (the upper bound) inside the instruction | 57 | `0x1400B00CA`, `0x140143744`, `0x1401468AA`, `0x14015C39F`, `0x14015DB4F`, `0x14015DBAC`, `0x14015E7C6`, `0x1401608C2`, `0x1401A597B`, `0x1401A59A2`, `0x1401C6135`, `0x1401C62BB`, `0x1401CCEAE`, `0x140217AFD`, `0x14021801D`, `0x14021ACAF`, `0x14021B684`, `0x14021BD54`, `0x14021BE7B`, `0x14021EE9C`, `0x14021F154`, `0x14021F503`, `0x14021F842`, `0x14029A4A3`, `0x14029C3D2`, `0x14029D03F`, `0x14029F773`, `0x1402BBE99`, `0x140312365`, `0x140454D6C`, `0x1404550E4`, `0x14045ECC0`, `0x14045ED6A`, `0x14045F437`, `0x14045F476`, `0x14046EB00`, `0x1404725C7`, `0x1404727C5`, `0x140472908`, `0x140478A93`, `0x14047C804`, `0x14047D8B9`, `0x140481693`, `0x140483327`, `0x1404A8F7D`, `0x1404CA44A`, `0x1404CA76B`, `0x1404CA913`, `0x140654E54`, `0x140665595`, `0x1406689A9`, `0x1406A7703`, `0x140725F43`, `0x1407E5BD7`, `0x14084287F`, `0x140851ACB`, `0x14085200D` |

## Sources
D5 Mod Kit development fork, generic-class site scan (`GENERIC-SCAN.md`, 2026-10-26); scanner code not published, its
results are the table above. See also the range tests listed on [functions.md](functions.md).
