# Disgaea 5 — Learned-evility bitmap: every code site

Where the game reads or writes a unit's learned-evility bits. Needed by anyone raising the 1,000-evility limit or
changing how learning works ([why](modifiable.md)): every site below assumes 1,000 bits at `unit+0x2E70…+0x2EEC`.

**Build B** (Steam, SHA-256 `49a7530b…db282ce`). **Verified offline**: a machine-code scan of the executable for
calls to the bit getter and setter, inline uses of the `+0x2E70` field, and references to `Skill.dat`'s position
tables (D5 Mod Kit fork census, 2026-10). A site the scan couldn't tie to a unit record isn't listed. Addresses are the
instruction; the first column is the function it's in.

**The pieces:** bit *n* = the *n*-th row of the sorted `Skill.dat`. ID → position uses the sorted key array at
`0x140A04E48`, the highest index at `0x140A04E38` and the row array at `0x140A04E50` (0x60-byte rows). Getter
`0x14015E630` (test a bit), setter `0x14015E6D0` (set or clear one).

## Calls to the getter (20 sites)

| Function | Call sites |
|---|---|
| `0x14018A480` | `0x14018A537` |
| `0x1401AEB10` | `0x1401AFC6D` |
| `0x1401D2180` | `0x1401D230C` |
| `0x140287A10` | `0x1402890E1` |
| `0x140294AA0` | `0x140294BED` |
| `0x1402C0220` | `0x1402C0359`, `0x1402C0451` |
| `0x1407CD960` | `0x1407CD986` |
| `0x1407CD9C0` | `0x1407CDB08` |
| `0x1407CDB30` | `0x1407CDC2C` |
| `0x1407CFA40` | `0x1407D0513` |
| `0x1407D1480` | `0x1407D1525` |
| `0x1407D3910` | `0x1407D43D1` |
| `0x140809E10` | `0x14080A1DE` |
| `0x140832120` | `0x1408321DC`, `0x14083221F` |
| `0x1408331C0` | `0x140833265` |
| `0x140841E00` | `0x1408422DE` |
| `0x14084EB50` | `0x14084F1EC`, `0x14084F22E` |

## Calls to the setter (123 sites)

| Function | Call sites |
|---|---|
| `0x14018A480` | `0x14018A54C` |
| `0x1401AEB10` | `0x1401AFC85` |
| `0x1401D2180` | `0x1401D2321` |
| `0x1401DA4E0` | `0x1401DADAD`, `0x1401DADD5`, `0x1401DADE6`, `0x1401DAE09` |
| `0x140218C90` | `0x140218D51` |
| `0x140219440` | `0x1402194FE` |
| `0x140287A10` | `0x1402890F3` |
| `0x1403BEE00` | `0x1403BF08F`, `0x1403BF0B5`, `0x1403BF0DB`, `0x1403BF101`, `0x1403BF127`, `0x1403BF14D`, `0x1403BF173`, `0x1403BF199`, `0x1403BF1BF`, `0x1403BF1E5` |
| `0x1403D1520` | `0x1403D1A8A`, `0x1403D1AAF`, `0x1403D1AD4`, `0x1403D1AF9`, `0x1403D1BD6`, `0x1403D1C86` |
| `0x1404D29C0` | `0x1404D3B11` |
| `0x1404D61A0` | `0x1404D620B`, `0x1404D6257`, `0x1404D62B2`, `0x1404D62FE`, `0x1404D634A`, `0x1404D639D`, `0x1404D63F8`, `0x1404D6444`, `0x1404D6490`, `0x1404D64EB`, `0x1404D6537`, `0x1404D6583`, `0x1404D65D6`, `0x1404D6631`, `0x1404D667D`, `0x1404D66D8`, `0x1404D6724`, `0x1404D677A`, `0x1404D6796`, `0x1404D67F1`, `0x1404D683D`, `0x1404D6898`, `0x1404D68E4`, `0x1404D693F`, `0x1404D6987`, `0x1404D69DA`, `0x1404D6A2D`, `0x1404D6A80`, `0x1404D6AD3`, `0x1404D6AF8`, `0x1404D6B53`, `0x1404D6B78`, `0x1404D6BC4`, `0x1404D6BE9`, `0x1404D6C35`, `0x1404D6C5A`, `0x1404D6CB1`, `0x1404D6CD6`, `0x1404D6CFB`, `0x1404D6D5B`, `0x1404D6D80`, `0x1404D6DA5`, `0x1404D6DCA`, `0x1404D6DEF`, `0x1404D6E14`, `0x1404D6E39`, `0x1404D6E5E`, `0x1404D6E83`, `0x1404D6EA8`, `0x1404D6ECD`, `0x1404D6EF2`, `0x1404D6F0F`, `0x1404D6F34`, `0x1404D6F59`, `0x1404D6FAC`, `0x1404D703D`, `0x1404D7093`, `0x1404D70B8`, `0x1404D70DD`, `0x1404D711F`, `0x1404D7144`, `0x1404D7169`, `0x1404D71AB`, `0x1404D71D0`, `0x1404D71F5`, `0x1404D7237`, `0x1404D725C`, `0x1404D7281`, `0x1404D72C3`, `0x1404D72E8`, `0x1404D730D`, `0x1404D734C`, `0x1404D7371`, `0x1404D7396`, `0x1404D7427`, `0x1404D744C`, `0x1404D7471` |
| `0x140552D20` | `0x1405530DC`, `0x140553101`, `0x140553115`, `0x14055313A` |
| `0x1406A0B80` | `0x1406A0CCC`, `0x1406A0E1C` |
| `0x1407C0BB0` | `0x1407C0BD9`, `0x1407C0BF6` |
| `0x1407C0C40` | `0x1407C0C69`, `0x1407C0C86` |
| `0x1407C0E80` | `0x1407C0EA9`, `0x1407C0EB6` |
| `0x1407C1540` | `0x1407C156B`, `0x1407C1588` |
| `0x1407C15D0` | `0x1407C15F2` |
| `0x1407CFA40` | `0x1407CFAC9` |
| `0x1407D3910` | `0x1407D3999` |
| `0x140809E10` | `0x140809F29` |
| `0x140859BB0` | `0x14085A2DB` |

## Inline reads and writes of the bits (no call to hook)

| Function | Instructions |
|---|---|
| `0x14015BB30` | `0x14015BBBB` |
| `0x14015E630` | `0x14015E6AD` |
| `0x140295BA0` | `0x140296053` |
| `0x1403B7A30` | `0x1403B8127`, `0x1403B8133`, `0x1403B8196`, `0x1403B8297`, `0x1403B82A3` |
| `0x1407C0A00` | `0x1407C0AD5`, `0x1407C0AE0`, `0x1407C0B53`, `0x1407C0B5E` |
| `0x1407C0CD0` | `0x1407C0DA5`, `0x1407C0DB0`, `0x1407C0E23`, `0x1407C0E2E` |
| `0x1407C0F00` | `0x1407C0FB9`, `0x1407C0FC4`, `0x1407C1033`, `0x1407C103E` |
| `0x1407C1090` | `0x1407C1165`, `0x1407C1170`, `0x1407C11F5`, `0x1407C1200`, `0x1407C1275`, `0x1407C1280`, `0x1407C12F5`, `0x1407C1300`, `0x1407C1375`, `0x1407C1380`, `0x1407C13F5`, `0x1407C1400`, `0x1407C1475`, `0x1407C1480`, `0x1407C14E3`, `0x1407C14EE` |
| start not recorded | `0x14052599C`, `0x1405259A8` |
| inside the setter `0x14015E6D0` | `0x14015E753`, `0x14015E765`, `0x14015E77D` |

## Code that takes the bitmap's address (copy, clear, clone)

| Function | Instructions |
|---|---|
| `0x14015E170` | `0x14015E1F7`, `0x14015E239` |
| `0x14015E4B0` | `0x14015E520`, `0x14015E53B` |
| `0x14015FAA0` | `0x14015FACA` |
| `0x1401623E0` | `0x140162439` |
| `0x1401B17D0` | `0x1401B1B67` |
| `0x14020D1C0` | `0x1402108CF`, `0x1402108DB` |
| `0x140219C40` | `0x140219F1F`, `0x14021A005` |
| `0x14028F3C0` | `0x14029068E` |
| `0x1402914C0` | `0x1402924AE` |
| `0x1402E7B30` | `0x1402E7B8D` |
| `0x1403B7430` | `0x1403B78E7`, `0x1403B78FA` |
| `0x1403ECBA0` | `0x1403EE757` |
| `0x140673380` | `0x140673A7F` |
| `0x1407139C0` | `0x140713C18` |
| start not recorded | `0x140064042` |
| start not recorded | `0x140217F28` |
| inside the unit creator `0x1402EA3E0` | `0x1402EA6E7` |
| start not recorded | `0x1404A99D4` |
| start not recorded | `0x14021B591` |

## References to the position tables

| Function | Table | Instructions |
|---|---|---|
| `0x14008D0F0` | key array | `0x14008D0FD` |
| `0x14008D0F0` | max index | `0x14008D109` |
| `0x14008D0F0` | row array | `0x14008D0F0`, `0x14008D14E` |
| `0x14015BB30` | key array | `0x14015BB3C` |
| `0x14015BB30` | max index | `0x14015BB4E` |
| `0x14015E630` | key array | `0x14015E635` |
| `0x14015E630` | max index | `0x14015E647` |
| `0x14015E6D0` | key array | `0x14015E6DA` |
| `0x14015E6D0` | max index | `0x14015E6EC` |
| `0x1401AEB10` | row array | `0x1401AFD36` |
| `0x1401D2180` | row array | `0x1401D24E8` |
| `0x14027F540` | row array | `0x14027F909` |
| `0x140283690` | row array | `0x14028391B` |
| `0x140287A10` | row array | `0x14028910D` |
| `0x140293280` | key array | `0x1402933C8`, `0x140293D7B` |
| `0x140293280` | max index | `0x1402933D4`, `0x140293D87` |
| `0x140293280` | row array | `0x1402933B6`, `0x140293974`, `0x140293D6F`, `0x14029453B` |
| `0x140294AA0` | max index | `0x140294BA1` |
| `0x140294AA0` | row array | `0x140294B8A`, `0x140294C95` |
| `0x140294E80` | key array | `0x140294FE3`, `0x1402950BC`, `0x140295111`, `0x1402951EC` |
| `0x140294E80` | max index | `0x14029503E`, `0x14029516E` |
| `0x140294E80` | row array | `0x140294FDC`, `0x1402950B5`, `0x14029510A`, `0x1402951E5` |
| `0x140295BA0` | key array | `0x14029696E`, `0x140296FAA`, `0x140297137` |
| `0x140295BA0` | max index | `0x140296A28`, `0x140296C9A`, `0x140296D26` |
| `0x140295BA0` | row array | `0x140296964`, `0x140296F9B`, `0x14029713E` |
| `0x140297670` | key array | `0x140297691` |
| `0x140297670` | max index | `0x14029771F` |
| `0x140297670` | row array | `0x1402976A5` |
| `0x14029EA20` | row array | `0x14029ECA9` |
| `0x1402A4D30` | key array | `0x1402A50A3`, `0x1402A5693` |
| `0x1402A4D30` | max index | `0x1402A50AF`, `0x1402A569F` |
| `0x1402A4D30` | row array | `0x1402A5097`, `0x1402A5687` |
| `0x1402BEEC0` | row array | `0x1402BEF46` |
| `0x1403B7A30` | key array | `0x1403B80C6`, `0x1403B80E0`, `0x1403B8235`, `0x1403B8250` |
| `0x1403B7A30` | max index | `0x1403B80D0`, `0x1403B823F` |
| `0x140426220` | row array | `0x140426400` |
| `0x140468CF0` | row array | `0x140468EE3` |
| `0x140475250` | row array | `0x140475EFD`, `0x140476CC0` |
| `0x14047F1C0` | row array | `0x14047FE52`, `0x140480E17` |
| `0x1405247D0` | key array | `0x1405258AC`, `0x1405259C8` |
| `0x1405247D0` | max index | `0x1405258DF` |
| `0x1405247D0` | row array | `0x1405258A1`, `0x1405259CF` |
| `0x14065FEB0` | row array | `0x140660B72`, `0x140661C85` |
| `0x140662CE0` | row array | `0x140663103` |
| `0x1406A0B80` | key array | `0x1406A0C1A`, `0x1406A0CD8`, `0x1406A0E28` |
| `0x1406A0B80` | max index | `0x1406A0C5A`, `0x1406A0DA7` |
| `0x1406A0B80` | row array | `0x1406A0C13`, `0x1406A0CD1`, `0x1406A0E21` |
| `0x14073B660` | row array | `0x14073B8F3` |
| `0x1407A1BD0` | row array | `0x1407A1CF3` |
| `0x1407A50E0` | row array | `0x1407A540A` |
| `0x1407B1470` | row array | `0x1407B14B0` |
| `0x1407C0A00` | key array | `0x1407C0A73`, `0x1407C0AF4` |
| `0x1407C0A00` | max index | `0x1407C0A7F`, `0x1407C0B00` |
| `0x1407C0CD0` | key array | `0x1407C0D43`, `0x1407C0DC4` |
| `0x1407C0CD0` | max index | `0x1407C0D4F`, `0x1407C0DD0` |
| `0x1407C0F00` | key array | `0x1407C0F5D`, `0x1407C0FD8` |
| `0x1407C0F00` | max index | `0x1407C0F69`, `0x1407C0FE4` |
| `0x1407C1090` | key array | `0x1407C1103`, `0x1407C1184`, `0x1407C1207`, `0x1407C1287`, `0x1407C1307`, `0x1407C1387`, `0x1407C1407`, `0x1407C1487` |
| `0x1407C1090` | max index | `0x1407C110F`, `0x1407C119C`, `0x1407C120E`, `0x1407C128E`, `0x1407C130E`, `0x1407C138E`, `0x1407C140E`, `0x1407C148E` |
| `0x1407CDB30` | max index | `0x1407CDB87`, `0x1407CDBCA`, `0x1407CDD2F`, `0x1407CDE38` |
| `0x1407CDB30` | row array | `0x1407CDBC0`, `0x1407CDD28`, `0x1407CDE31` |
| `0x1407CDE50` | max index | `0x1407CDE9C`, `0x1407CDED4`, `0x1407CE02B`, `0x1407CE153` |
| `0x1407CDE50` | row array | `0x1407CDECB`, `0x1407CE022`, `0x1407CE14A` |
| `0x1407CE3E0` | row array | `0x1407CE4B0` |
| `0x1407CFA40` | row array | `0x1407D054B` |
| `0x1407D1480` | max index | `0x1407D14E6` |
| `0x1407D1480` | row array | `0x1407D14F5` |
| `0x1407D22B0` | row array | `0x1407D237B` |
| `0x1407D3910` | row array | `0x1407D4406` |
| `0x1407D8830` | max index | `0x1407D8892` |
| `0x1407D8830` | row array | `0x1407D88A2` |
| `0x1407DAFB0` | max index | `0x1407DB1F7`, `0x1407DB3E2` |
| `0x1407DAFB0` | row array | `0x1407DB20A`, `0x1407DB3F5` |
| `0x1407E4920` | row array | `0x1407E50B2` |
| `0x1407ED230` | row array | `0x1407ED55A` |
| `0x1407EF420` | row array | `0x1407EF72D` |
| `0x140809E10` | row array | `0x140809F60`, `0x14080A227`, `0x14080A7ED` |
| `0x140812240` | row array | `0x14081248D` |
| `0x140832120` | max index | `0x14083216D`, `0x1408321A0` |
| `0x140832120` | row array | `0x140832192`, `0x1408321BA` |
| `0x1408331C0` | max index | `0x1408331F8` |
| `0x1408331C0` | row array | `0x140833226`, `0x1408333E1` |
| `0x140834420` | max index | `0x140834434` |
| `0x140834420` | row array | `0x14083443E` |
| `0x140843A80` | row array | `0x1408454FC`, `0x140845C84` |
| `0x14084EB50` | max index | `0x14084F179`, `0x14084F1AE` |
| `0x14084EB50` | row array | `0x14084F1A0`, `0x14084F1CA` |

## Sources
D5 Mod Kit development fork, `EVCAP2-CENSUS.txt` (machine-code census of the pinned build-B executable). Function
names from other projects' labels are left out; a function is given by its start address.
