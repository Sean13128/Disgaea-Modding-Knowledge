# Disgaea 5 — Where the game checks each evility

D5 evilities are mostly hard-coded: the code tests a unit for a literal evility ID and does something. This index
lists, for every evility ID that is tested anywhere, **which functions test it**. To change what an evility does,
these are the places to patch or hook. Overview and method: [functions.md](functions.md#where-the-game-checks-for-evilities).

**Build B** (installed Steam exe, SHA-256 `49a7530b…db282ce`). 416 functions, 1149 ID tests, 666 distinct IDs.
Built from the D5 Mod Kit fork's decompile, then **every test re-checked against the executable's own bytes** with
Capstone (a list load at `unit+0x2F30` followed by a compare with the ID, or a call to the shared check `0x1402068B0`
with the ID as its argument). **Verified offline.** Function names are left out on purpose; look them up in your own
disassembly. Evility names are in `Skill.dat` ([data reference](data-reference.md)).

**How a test is written:** *inline* = the compiler copied the binary search into the function; *helper* = a call to
`0x1402068B0` ("has any of these evilities"). Two small wrappers test one fixed ID each: `0x140162010` (4503) and
`0x140204030` (5000).

## By topic

| Topic | Functions | ID tests | Distinct IDs |
|---|---:|---:|---:|
| other / not yet classified | 252 | 492 | 252 |
| damage calculation | 44 | 338 | 309 |
| AI decisions | 23 | 27 | 7 |
| command-menu predicates | 15 | 21 | 15 |
| carry / throw | 13 | 20 | 14 |
| turn order and round upkeep | 12 | 21 | 18 |
| evility stat aggregation (potency, growth, status, list rebuild) | 10 | 154 | 149 |
| movement / pathing | 9 | 11 | 10 |
| battle model state | 8 | 16 | 16 |
| deployment | 7 | 7 | 5 |
| script commands | 5 | 8 | 6 |
| battle flow | 5 | 5 | 5 |
| unit stat aggregation | 5 | 6 | 5 |
| battle setup | 2 | 12 | 12 |
| world map | 2 | 4 | 2 |
| battle record helpers | 1 | 1 | 1 |
| geometry / misc | 1 | 1 | 1 |
| battle result | 1 | 3 | 3 |
| specials / reinforcements | 1 | 2 | 2 |

## By evility ID

| ID | Tested in (function address: topic, how) |
|---:|---|
| 11 | `0x1401F6300` damage calculation, inline; `0x14020D1C0` other, helper |
| 12 | `0x1401FB2E0` damage calculation, inline |
| 13 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 14 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 15 | `0x1402042E0` damage calculation, helper |
| 16 | `0x1401F6300` damage calculation, inline |
| 40 | `0x1401F8FD0` damage calculation, inline |
| 42 | `0x1401E3D50` battle result, inline |
| 43 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 46 | `0x1401FDBC0` damage calculation, inline |
| 50 | `0x1401F8700` damage calculation, inline |
| 51 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 52 | `0x1401F6300` damage calculation, inline |
| 53 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 54 | `0x1401EEE30` evility stat aggregation (potency, growth, status, list rebuild), inline; `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 63 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 64 | `0x1401E3D50` battle result, inline |
| 65 | `0x140200310` damage calculation, inline |
| 66 | `0x1401FB2E0` damage calculation, inline |
| 70 | `0x1401FB2E0` damage calculation, helper; `0x14020D1C0` other, helper |
| 71 | `0x1401F6300` damage calculation, inline; `0x14020D1C0` other, helper |
| 72 | `0x1401E4390` other, inline; `0x1405299C0` other, inline |
| 73 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 74 | `0x1401E62D0` other, inline; `0x14021BFB0` script commands, inline |
| 75 | `0x1401FD950` battle model state, inline; `0x14020D1C0` other, helper |
| 76 | `0x1401F5410` damage calculation, helper |
| 101 | `0x1401F8FD0` damage calculation, inline |
| 103 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 104 | `0x1401E3D50` battle result, inline |
| 106 | `0x1401FB2E0` damage calculation, inline |
| 121 | `0x1401E1B30` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 122 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 123 | `0x1401F8FD0` damage calculation, inline; `0x14020D1C0` other, helper |
| 124 | `0x1401F8700` damage calculation, inline; `0x14020D1C0` other, helper |
| 125 | `0x1401E5AF0` turn order and round upkeep, helper |
| 140 | `0x1401F6300` damage calculation, inline; `0x14020D1C0` other, helper |
| 141 | `0x1401FFF40` damage calculation, helper |
| 142 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 143 | `0x1401ADA40` other, inline; `0x1402032A0` other, inline |
| 144 | `0x1402029B0` other, inline |
| 145 | `0x14020D1C0` other, helper; `0x1404A8D10` battle model state, inline |
| 150 | `0x1401E5AF0` turn order and round upkeep, inline; `0x1401E5F60` battle setup, inline |
| 160 | `0x1401E5F60` battle setup, inline |
| 161 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 162 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline; `0x1402073D0` other, inline |
| 165 | `0x1401EEE30` evility stat aggregation (potency, growth, status, list rebuild), inline; `0x140200D20` damage calculation, inline |
| 170 | `0x1401F6300` damage calculation, inline; `0x14020D1C0` other, helper |
| 240 | `0x1401F6300` damage calculation, inline |
| 241 | `0x1401EB390` other, inline |
| 242 | `0x1401F6300` damage calculation, inline; `0x14020D1C0` other, helper |
| 243 | `0x1401F8FD0` damage calculation, inline |
| 244 | `0x1401E3840` other, inline |
| 245 | `0x1401EB390` other, inline |
| 410 | `0x1401FB2E0` damage calculation, helper |
| 1011 | `0x1401E1B30` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 1012 | `0x1401F8FD0` damage calculation, inline |
| 1013 | `0x140161A20` damage calculation, helper; `0x140204720` other, inline |
| 1015 | `0x1401FB2E0` damage calculation, inline; `0x14020D1C0` other, helper |
| 1020 | `0x1401F8FD0` damage calculation, inline; `0x14020D1C0` other, helper |
| 1021 | `0x1401E1B30` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 1022 | `0x1402069A0` other, helper |
| 1023 | `0x1401C0C60` damage calculation, inline |
| 1025 | `0x1401EC0B0` other, inline |
| 1030 | `0x1401F4830` other, inline; `0x1401F6300` damage calculation, inline |
| 1031 | `0x1401E1B30` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 1032 | `0x140207D70` other, inline |
| 1033 | `0x1401EC5C0` command-menu predicates, inline; `0x1401F4830` other, inline |
| 1034 | `0x14060BC30` other, inline; `0x14060C090` other, inline; `0x14060C550` other, inline; `0x14060D430` other, inline; `0x14062D2B0` command-menu predicates, inline |
| 1035 | `0x1401EDF90` other, inline; `0x1401F6300` damage calculation, inline; `0x140551AC0` turn order and round upkeep, inline |
| 1040 | `0x1401F4830` other, inline |
| 1041 | `0x1401E1B30` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 1044 | `0x140203400` other, inline |
| 1045 | `0x1401F6300` damage calculation, inline |
| 1050 | `0x1401F6300` damage calculation, inline |
| 1051 | `0x1401FB2E0` damage calculation, inline |
| 1052 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 1053 | `0x140202120` other, inline |
| 1054 | `0x1401F60A0` damage calculation, inline |
| 1055 | `0x1401ECE00` other, inline |
| 1060 | `0x1401F5410` damage calculation, helper; `0x14020D1C0` other, helper |
| 1061 | `0x1401EC9E0` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 1062 | `0x1401E55D0` movement / pathing, inline |
| 1063 | `0x1401F5410` damage calculation, inline |
| 1064 | `0x1401F8FD0` damage calculation, inline |
| 1065 | `0x1401F5410` damage calculation, helper |
| 1070 | `0x1401BF3C0` damage calculation, inline |
| 1071 | `0x1402069A0` other, inline |
| 1072 | `0x1401F4D80` AI decisions, inline |
| 1073 | `0x1401E51B0` other, inline |
| 1075 | `0x140201B80` other, inline |
| 1080 | `0x1401FFA50` damage calculation, inline |
| 1081 | `0x1401E1B30` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 1082 | `0x1401F4D80` AI decisions, inline |
| 1085 | `0x140202D40` other, inline |
| 1090 | `0x1401E3C00` other, inline; `0x1404CF1E0` damage calculation, inline |
| 1091 | `0x1401E1B30` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 1092 | `0x1401EADC0` other, helper |
| 1093 | `0x1404A7BE0` specials / reinforcements, inline |
| 1094 | `0x1401ECBD0` other, inline; `0x140533B50` damage calculation, inline |
| 1095 | `0x1401ECB50` other, inline; `0x140533850` battle model state, inline |
| 1101 | `0x1401E6950` other, helper; `0x1401FB2E0` damage calculation, inline; `0x1401FFA50` damage calculation, helper |
| 1103 | `0x1401C8F40` other, helper; `0x1401C9220` other, helper |
| 1104 | `0x1401E83C0` other, inline; `0x140529590` other, inline |
| 1105 | `0x1404A3AB0` other, inline |
| 1110 | `0x14020D1C0` other, helper; `0x1404D23A0` other, inline |
| 1111 | `0x1401E1B30` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 1112 | `0x1401EC9E0` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 1113 | `0x140202120` other, inline |
| 1114 | `0x1401F6300` damage calculation, inline |
| 1115 | `0x1401F8FD0` damage calculation, inline |
| 1120 | `0x1401EC4D0` movement / pathing, helper; `0x140416850` other, inline; `0x140417480` other, inline |
| 1121 | `0x1401F5410` damage calculation, inline |
| 1122 | `0x1401EC9E0` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 1123 | `0x1401F6300` damage calculation, inline |
| 1124 | `0x1401532E0` command-menu predicates, inline |
| 1125 | `0x1401EC4D0` movement / pathing, helper; `0x140416850` other, inline; `0x140417480` other, inline |
| 1130 | `0x1401F5410` damage calculation, helper |
| 1131 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 1132 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 1134 | `0x1401F8FD0` damage calculation, helper |
| 1135 | `0x1401EEE30` evility stat aggregation (potency, growth, status, list rebuild), inline; `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 1140 | `0x1401F8FD0` damage calculation, inline |
| 1142 | `0x1401F8FD0` damage calculation, inline |
| 1143 | `0x1401FDBC0` damage calculation, inline |
| 1144 | `0x1401F8FD0` damage calculation, inline |
| 1145 | `0x1401F6300` damage calculation, inline; `0x1401F8700` damage calculation, inline; `0x14020D1C0` other, helper |
| 1150 | `0x140206480` other, inline; `0x14020D1C0` other, helper |
| 1152 | `0x1401FB2E0` damage calculation, inline; `0x14020D1C0` other, helper |
| 1153 | `0x1401E7960` carry / throw, inline; `0x1401E7C20` movement / pathing, inline; `0x1401E7EE0` movement / pathing, helper |
| 1154 | `0x1401ECE00` other, inline |
| 1155 | `0x1405408C0` other, inline |
| 1160 | `0x1401F6300` damage calculation, inline |
| 1161 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 1162 | `0x140200390` damage calculation, inline |
| 1163 | `0x1402031D0` other, inline |
| 1165 | `0x1401F4AC0` other, inline |
| 1180 | `0x1401FEFA0` damage calculation, inline |
| 1181 | `0x1402069A0` other, inline |
| 1182 | `0x140201D90` other, inline |
| 1183 | `0x140201D90` other, inline |
| 1185 | `0x1401E7330` carry / throw, inline |
| 1190 | `0x1401E7210` carry / throw, inline |
| 1191 | `0x1401E1B30` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 1192 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 1193 | `0x1401E7190` command-menu predicates, inline |
| 1194 | `0x1401FB2E0` damage calculation, inline; `0x14020D1C0` other, helper |
| 1195 | `0x1401F8FD0` damage calculation, helper |
| 1200 | `0x1401F8FD0` damage calculation, inline |
| 1201 | `0x1401E1B30` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 1202 | `0x1401AEB10` damage calculation, helper |
| 1203 | `0x1403C40F0` other, helper |
| 1204 | `0x1401FB2E0` damage calculation, inline |
| 1205 | `0x1401E3CE0` other, helper; `0x140527060` battle flow, helper |
| 1210 | `0x1401F8FD0` damage calculation, inline |
| 1211 | `0x1401E1B30` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 1212 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 1213 | `0x1401C0C60` damage calculation, inline |
| 1214 | `0x1401F5410` damage calculation, helper |
| 1215 | `0x1401F8FD0` damage calculation, inline |
| 1220 | `0x1401E85B0` other, inline; `0x140542190` other, inline |
| 1221 | `0x1401E1B30` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 1222 | `0x1401FB2E0` damage calculation, inline |
| 1223 | `0x1401FB2E0` damage calculation, inline |
| 1224 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline; `0x14020D1C0` other, helper |
| 1225 | `0x14020D1C0` other, helper |
| 1230 | `0x1401ECFA0` battle setup, inline |
| 1231 | `0x1401ECFA0` battle setup, inline |
| 1232 | `0x1401ECFA0` battle setup, inline; `0x1401F5410` damage calculation, helper |
| 1233 | `0x1401ECFA0` battle setup, inline; `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline; `0x1401F60A0` damage calculation, inline |
| 1234 | `0x1401ECFA0` battle setup, inline |
| 1235 | `0x1401ECFA0` battle setup, inline |
| 1236 | `0x140160720` battle model state, inline; `0x1401EADC0` other, inline; `0x1401ECFA0` battle setup, inline; `0x1402038C0` other, inline |
| 1237 | `0x1401F5410` damage calculation, helper; `0x1401FB2E0` damage calculation, inline |
| 1239 | `0x1401E1B30` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 1240 | `0x1401E5000` other, inline |
| 1241 | `0x1401E1B30` evility stat aggregation (potency, growth, status, list rebuild), inline; `0x1401EEE30` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 2010 | `0x140160720` battle model state, inline; `0x1401EADC0` other, inline; `0x1402038C0` other, inline |
| 2011 | `0x140777EF0` other, inline |
| 2012 | `0x1402038C0` other, inline |
| 2014 | `0x1401EADC0` other, inline |
| 2015 | `0x1401E44D0` battle model state, inline |
| 2020 | `0x1401F8FD0` damage calculation, inline |
| 2021 | `0x1401EBDE0` turn order and round upkeep, inline |
| 2023 | `0x1401BA890` unit stat aggregation, inline; `0x14021BFB0` script commands, inline; `0x1402AA0E0` other, inline; `0x1402B0320` other, inline |
| 2031 | `0x1401FF070` damage calculation, inline |
| 2032 | `0x140205950` damage calculation, inline |
| 2033 | `0x1401F6300` damage calculation, inline; `0x14020D1C0` other, helper |
| 2035 | `0x1401F5410` damage calculation, inline; `0x1401F6300` damage calculation, inline |
| 2040 | `0x1401F8FD0` damage calculation, inline |
| 2041 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 2042 | `0x140205950` damage calculation, inline |
| 2044 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 2045 | `0x1401FDBC0` damage calculation, inline |
| 2051 | `0x1401FF070` damage calculation, inline |
| 2052 | `0x140205950` damage calculation, inline |
| 2053 | `0x1401ADA40` other, inline; `0x140203020` other, inline |
| 2054 | `0x1401E4170` other, helper |
| 2055 | `0x1401ADA40` other, inline |
| 2060 | `0x1401FB2E0` damage calculation, inline |
| 2061 | `0x1401FF070` damage calculation, inline |
| 2062 | `0x140205950` damage calculation, inline |
| 2063 | `0x1401FB2E0` damage calculation, inline |
| 2064 | `0x1401FB2E0` damage calculation, inline |
| 2065 | `0x1401F6300` damage calculation, inline; `0x14020D1C0` other, helper |
| 2070 | `0x1401FB2E0` damage calculation, inline |
| 2071 | `0x140205950` damage calculation, inline |
| 2072 | `0x1401FE0D0` damage calculation, helper |
| 2073 | `0x1401F8FD0` damage calculation, inline |
| 2074 | `0x1401F5410` damage calculation, helper |
| 2080 | `0x1401FF070` damage calculation, helper |
| 2081 | `0x1401FF070` damage calculation, inline |
| 2082 | `0x140205950` damage calculation, inline |
| 2083 | `0x1401EBDE0` turn order and round upkeep, helper |
| 2084 | `0x1401EB980` damage calculation, helper |
| 2085 | `0x1401F8FD0` damage calculation, inline |
| 2090 | `0x1401ADA40` other, inline; `0x1402030F0` other, inline |
| 2091 | `0x140205950` damage calculation, inline |
| 2092 | `0x1401F8FD0` damage calculation, inline |
| 2093 | `0x1401F8700` damage calculation, inline |
| 2094 | `0x140202D40` other, inline |
| 2101 | `0x1401FF070` damage calculation, inline |
| 2102 | `0x1401E44D0` battle model state, inline |
| 2103 | `0x1401FB2E0` damage calculation, inline |
| 2104 | `0x1401ED6A0` turn order and round upkeep, inline |
| 2105 | `0x1402012A0` damage calculation, inline |
| 2120 | `0x1401EB390` other, inline |
| 2121 | `0x140220470` other, inline |
| 2122 | `0x1401F8FD0` damage calculation, inline |
| 2123 | `0x1401F6300` damage calculation, inline; `0x1401F8700` damage calculation, inline |
| 2124 | `0x1401FDBC0` damage calculation, inline |
| 2125 | `0x1401E6B00` other, inline; `0x140547030` other, inline |
| 2131 | `0x1402069A0` other, helper |
| 2132 | `0x140207470` other, helper |
| 2133 | `0x1401FFA50` damage calculation, helper |
| 2134 | `0x1401E55D0` movement / pathing, helper |
| 2141 | `0x1401FF070` damage calculation, inline |
| 2142 | `0x1401F8FD0` damage calculation, inline |
| 2143 | `0x140202120` other, inline |
| 2151 | `0x1401FF070` damage calculation, inline |
| 2153 | `0x1401E44D0` battle model state, inline |
| 2154 | `0x1401FDBC0` damage calculation, helper |
| 2155 | `0x1401FE0D0` damage calculation, helper |
| 2160 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 2161 | `0x1401F8FD0` damage calculation, inline |
| 2162 | `0x140147290` damage calculation, inline; `0x140207C30` other, inline |
| 2163 | `0x140497180` other, inline |
| 2164 | `0x1401EE670` turn order and round upkeep, inline |
| 2165 | `0x1401B1FD0` damage calculation, inline; `0x1401F8FD0` damage calculation, helper; `0x140201850` other, inline; `0x14020D1C0` other, helper |
| 2170 | `0x1401F6300` damage calculation, inline; `0x14020D1C0` other, helper |
| 2171 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 2172 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 2173 | `0x1401FD950` battle model state, helper |
| 2174 | `0x140203400` other, inline |
| 2175 | `0x1401F8FD0` damage calculation, inline; `0x14020D1C0` other, helper |
| 2181 | `0x1401F8FD0` damage calculation, inline |
| 2182 | `0x1401F5410` damage calculation, helper |
| 2183 | `0x140202120` other, inline |
| 2184 | `0x1401F8FD0` damage calculation, inline |
| 2185 | `0x1401F8FD0` damage calculation, inline |
| 2191 | `0x1401F8FD0` damage calculation, inline |
| 2192 | `0x1401FB2E0` damage calculation, inline |
| 2193 | `0x1401E97A0` deployment, helper |
| 2194 | `0x1401F8FD0` damage calculation, inline |
| 2195 | `0x1401E9270` turn order and round upkeep, helper; `0x1401E9540` other, helper |
| 2200 | `0x1401EB390` other, inline; `0x14020D1C0` other, helper |
| 2201 | `0x1401E6F20` other, inline |
| 2202 | `0x1401F8FD0` damage calculation, helper; `0x14020D1C0` other, helper |
| 2203 | `0x1401FE0D0` damage calculation, inline |
| 2204 | `0x1401F8FD0` damage calculation, helper |
| 2205 | `0x1401F8FD0` damage calculation, helper |
| 2210 | `0x1401FB2E0` damage calculation, inline |
| 2211 | `0x1401F8FD0` damage calculation, helper |
| 2212 | `0x1401F8FD0` damage calculation, helper |
| 2213 | `0x1401EEE30` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 2214 | `0x1401FB2E0` damage calculation, inline; `0x14020D1C0` other, helper |
| 2215 | `0x1401F6300` damage calculation, inline; `0x14020D1C0` other, helper |
| 3001 | `0x1401F5410` damage calculation, inline; `0x1401F8FD0` damage calculation, helper |
| 3003 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 3004 | `0x1401FF070` damage calculation, inline |
| 3005 | `0x1401FF070` damage calculation, inline |
| 3006 | `0x1401FB2E0` damage calculation, inline |
| 3008 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 3009 | `0x1401EB390` other, inline |
| 3010 | `0x1401EE010` turn order and round upkeep, inline |
| 3011 | `0x1401ADA40` other, inline; `0x140202BC0` other, inline |
| 3012 | `0x1401FF070` damage calculation, inline |
| 3013 | `0x1401F8FD0` damage calculation, helper |
| 3014 | `0x1401E9AB0` other, inline; `0x140220C10` damage calculation, inline |
| 3015 | `0x1401FF070` damage calculation, inline |
| 3016 | `0x1401B0910` damage calculation, inline; `0x140200C60` other, inline |
| 3017 | `0x1401FB2E0` damage calculation, inline |
| 3018 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 3019 | `0x1401E51B0` other, inline |
| 3020 | `0x140202120` other, inline |
| 3021 | `0x140203400` other, inline |
| 3022 | `0x1401FB2E0` damage calculation, inline |
| 3023 | `0x1401EA9C0` movement / pathing, inline |
| 3024 | `0x14015C490` damage calculation, inline; `0x1401EEE30` evility stat aggregation (potency, growth, status, list rebuild), inline; `0x140205BF0` other, inline |
| 3025 | `0x1401E1B30` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 3026 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 3027 | `0x1401E6D30` other, inline |
| 3028 | `0x1402016F0` damage calculation, inline |
| 3029 | `0x1401F8FD0` damage calculation, helper |
| 3030 | `0x140202120` other, inline |
| 3032 | `0x1401AEB10` damage calculation, inline; `0x140200FA0` other, inline |
| 3033 | `0x140202120` other, inline |
| 3034 | `0x1401BE860` battle flow, inline |
| 3035 | `0x1401EE670` turn order and round upkeep, inline |
| 3036 | `0x1401AEB10` damage calculation, helper; `0x1402011B0` other, inline |
| 3037 | `0x1401FF070` damage calculation, inline |
| 3039 | `0x1401EEE30` evility stat aggregation (potency, growth, status, list rebuild), helper |
| 3040 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 3041 | `0x1401E7030` AI decisions, inline |
| 3042 | `0x140202120` other, inline |
| 3043 | `0x1401E3840` other, inline |
| 3044 | `0x1401E7030` AI decisions, inline |
| 3045 | `0x1401FF070` damage calculation, inline |
| 3200 | `0x1401F4AC0` other, inline |
| 3201 | `0x1401F4AC0` other, inline |
| 3202 | `0x1401F4AC0` other, inline |
| 3203 | `0x1401E5F60` battle setup, inline |
| 3300 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 3301 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 3302 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 3303 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 3304 | `0x1402069A0` other, inline; `0x14020D1C0` other, helper |
| 3305 | `0x1401EC5C0` command-menu predicates, inline; `0x1401FB2E0` damage calculation, inline; `0x1401FFF40` damage calculation, inline |
| 3306 | `0x140207470` other, inline; `0x14020D1C0` other, helper |
| 4001 | `0x140203BB0` other, inline |
| 4002 | `0x140203BB0` other, inline |
| 4003 | `0x140203BB0` other, inline |
| 4004 | `0x140203BB0` other, inline |
| 4005 | `0x140203BB0` other, inline |
| 4006 | `0x140203BB0` other, inline |
| 4007 | `0x1401FF070` damage calculation, inline |
| 4008 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4009 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4010 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4014 | `0x1401FE0D0` damage calculation, inline |
| 4015 | `0x1401FE0D0` damage calculation, helper |
| 4016 | `0x1401FE0D0` damage calculation, inline |
| 4017 | `0x1401F8FD0` damage calculation, helper |
| 4018 | `0x1401F8FD0` damage calculation, helper |
| 4019 | `0x1401F8FD0` damage calculation, helper |
| 4020 | `0x1401F8FD0` damage calculation, helper |
| 4021 | `0x1401F8FD0` damage calculation, helper |
| 4022 | `0x1401F8FD0` damage calculation, helper |
| 4023 | `0x1401F8FD0` damage calculation, helper |
| 4024 | `0x1401F8FD0` damage calculation, helper |
| 4025 | `0x1401F8FD0` damage calculation, helper |
| 4026 | `0x1401F8FD0` damage calculation, helper |
| 4027 | `0x1401F8FD0` damage calculation, helper |
| 4028 | `0x1401F8FD0` damage calculation, helper |
| 4029 | `0x1401F8FD0` damage calculation, helper |
| 4030 | `0x1401F8FD0` damage calculation, helper |
| 4031 | `0x1401F8FD0` damage calculation, helper |
| 4032 | `0x1401F8FD0` damage calculation, helper |
| 4050 | `0x1401F6300` damage calculation, helper |
| 4051 | `0x140220720` other, inline |
| 4052 | `0x1401F6300` damage calculation, helper; `0x140207B90` other, inline |
| 4053 | `0x1401F8FD0` damage calculation, helper |
| 4054 | `0x1401F8FD0` damage calculation, helper |
| 4055 | `0x1401F6300` damage calculation, inline; `0x14020D1C0` other, helper |
| 4056 | `0x1401C1D00` damage calculation, inline; `0x1401F6300` damage calculation, inline |
| 4057 | `0x1401FB2E0` damage calculation, inline |
| 4058 | `0x1401FF070` damage calculation, inline |
| 4081 | `0x1401EEE30` evility stat aggregation (potency, growth, status, list rebuild), inline; `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4082 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4083 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4087 | `0x1401F0280` evility stat aggregation (potency, growth, status, list rebuild), helper |
| 4088 | `0x1401F0280` evility stat aggregation (potency, growth, status, list rebuild), helper |
| 4089 | `0x1401F0280` evility stat aggregation (potency, growth, status, list rebuild), helper |
| 4090 | `0x1401F0280` evility stat aggregation (potency, growth, status, list rebuild), helper |
| 4091 | `0x1401E7330` carry / throw, inline |
| 4092 | `0x1401F0280` evility stat aggregation (potency, growth, status, list rebuild), helper |
| 4093 | `0x1401F0280` evility stat aggregation (potency, growth, status, list rebuild), helper; `0x14020D1C0` other, helper |
| 4094 | `0x1401F0280` evility stat aggregation (potency, growth, status, list rebuild), helper; `0x14020D1C0` other, helper |
| 4095 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4110 | `0x1401FFA50` damage calculation, inline |
| 4111 | `0x1401FFA50` damage calculation, inline |
| 4112 | `0x1401FFA50` damage calculation, inline |
| 4113 | `0x1401FFA50` damage calculation, inline |
| 4114 | `0x1401F5410` damage calculation, helper; `0x14020D1C0` other, helper |
| 4115 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4116 | `0x1401C1D00` damage calculation, inline |
| 4117 | `0x1401E7030` AI decisions, inline |
| 4118 | `0x1401F5410` damage calculation, inline |
| 4120 | `0x1401E3840` other, inline; `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4121 | `0x14015C490` damage calculation, inline; `0x1401EEE30` evility stat aggregation (potency, growth, status, list rebuild), inline; `0x140205BF0` other, inline |
| 4122 | `0x140542890` other, inline |
| 4123 | `0x1401EEE30` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4124 | `0x1401EE670` turn order and round upkeep, inline |
| 4125 | `0x1401E6950` other, inline |
| 4126 | `0x140202D40` other, inline |
| 4127 | `0x1401EC0B0` other, inline |
| 4128 | `0x1401E6D30` other, inline; `0x1404AFF00` deployment, inline |
| 4129 | `0x1401EEE30` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4130 | `0x1401FDBC0` damage calculation, inline |
| 4131 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4150 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4151 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4152 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4153 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4154 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4155 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4156 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4157 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4158 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4159 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4160 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4161 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4162 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4163 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4164 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4165 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4166 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4167 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4168 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4169 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4170 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4171 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4172 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4173 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4174 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4175 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4176 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4177 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4178 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4179 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4180 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4181 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4182 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4183 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4184 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4185 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4186 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4187 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4188 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4189 | `0x1401E9B50` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4200 | `0x1402069A0` other, inline |
| 4201 | `0x1402069A0` other, inline |
| 4202 | `0x1402069A0` other, inline |
| 4203 | `0x1402069A0` other, inline |
| 4204 | `0x1402069A0` other, inline |
| 4205 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline; `0x1402069A0` other, inline |
| 4206 | `0x1402069A0` other, inline; `0x14020D1C0` other, helper |
| 4210 | `0x140207470` other, inline |
| 4211 | `0x140207470` other, inline |
| 4212 | `0x140207470` other, inline |
| 4213 | `0x140207470` other, inline |
| 4214 | `0x140207470` other, inline |
| 4220 | `0x140207E70` other, inline |
| 4221 | `0x140207E70` other, inline |
| 4222 | `0x140207E70` other, inline |
| 4223 | `0x140207E70` other, inline |
| 4224 | `0x140207E70` other, inline |
| 4230 | `0x1401E1B30` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4231 | `0x1401E1B30` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4232 | `0x1401E1B30` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4233 | `0x1401E1B30` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4234 | `0x1401E1B30` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 4240 | `0x140204A10` damage calculation, inline |
| 4241 | `0x140204A10` damage calculation, inline |
| 4242 | `0x140204A10` damage calculation, inline |
| 4243 | `0x140204A10` damage calculation, inline |
| 4244 | `0x140204A10` damage calculation, inline |
| 4245 | `0x140204A10` damage calculation, inline |
| 4246 | `0x140204A10` damage calculation, inline |
| 4247 | `0x140204A10` damage calculation, inline |
| 4248 | `0x140204A10` damage calculation, inline |
| 4249 | `0x140204A10` damage calculation, inline |
| 4250 | `0x140204A10` damage calculation, inline |
| 4251 | `0x140204A10` damage calculation, inline |
| 4252 | `0x140204A10` damage calculation, inline |
| 4253 | `0x140204A10` damage calculation, inline |
| 4254 | `0x140204A10` damage calculation, inline |
| 4255 | `0x140204A10` damage calculation, inline |
| 4256 | `0x140204A10` damage calculation, inline |
| 4257 | `0x140204A10` damage calculation, inline |
| 4258 | `0x140204A10` damage calculation, inline |
| 4259 | `0x140204A10` damage calculation, inline |
| 4260 | `0x140204A10` damage calculation, inline |
| 4261 | `0x140204A10` damage calculation, inline |
| 4262 | `0x140204A10` damage calculation, inline |
| 4263 | `0x140204A10` damage calculation, inline |
| 4264 | `0x140204A10` damage calculation, inline |
| 4265 | `0x140204A10` damage calculation, inline |
| 4266 | `0x140204A10` damage calculation, inline |
| 4267 | `0x140204A10` damage calculation, inline |
| 4268 | `0x140204A10` damage calculation, inline |
| 4269 | `0x140204A10` damage calculation, inline |
| 4270 | `0x140204A10` damage calculation, inline |
| 4271 | `0x140204A10` damage calculation, inline |
| 4272 | `0x140204A10` damage calculation, inline |
| 4273 | `0x140204A10` damage calculation, inline |
| 4274 | `0x140204A10` damage calculation, inline |
| 4275 | `0x140204A10` damage calculation, inline |
| 4276 | `0x140204A10` damage calculation, inline |
| 4277 | `0x140204A10` damage calculation, inline |
| 4278 | `0x140204A10` damage calculation, inline |
| 4279 | `0x140204A10` damage calculation, inline |
| 4292 | `0x1401E4040` other, inline |
| 4302 | `0x1401F6300` damage calculation, inline |
| 4303 | `0x140773F10` other, inline |
| 4304 | `0x1401E6CB0` other, inline |
| 4305 | `0x1401F8FD0` damage calculation, helper |
| 4306 | `0x1401F6300` damage calculation, helper; `0x14082B450` other, inline |
| 4307 | `0x1401FDBC0` damage calculation, inline; `0x14082D520` other, inline |
| 4308 | `0x140147290` damage calculation, helper; `0x1402069A0` other, helper; `0x140207470` other, helper; `0x140207C30` other, helper; `0x140207DF0` damage calculation, inline |
| 4309 | `0x1401E6950` other, inline; `0x1405299C0` other, inline |
| 4310 | `0x1401B1FD0` damage calculation, helper |
| 4311 | `0x1401AEB10` damage calculation, helper; `0x1402010C0` other, helper |
| 4320 | `0x1401E5490` other, helper; `0x140227430` command-menu predicates, helper; `0x140257660` other, helper; `0x1402605F0` other, helper; `0x140279220` other, helper; `0x14027BC80` other, helper; `0x14027FCB0` other, helper; `0x140280B30` other, helper; `0x14028BBB0` other, helper; `0x1403E1550` other, helper; `0x14043F110` other, helper; `0x140441190` other, helper; `0x140445E90` other, helper; `0x14045DD30` other, helper; `0x140465420` other, helper; `0x14046CB00` other, helper; `0x14054DB60` deployment, helper; `0x14054F830` deployment, helper; `0x1405F6FB0` other, helper; `0x1405FD140` other, helper; `0x1405FE450` other, helper; `0x140607110` carry / throw, helper; `0x140608330` carry / throw, helper; `0x14060E100` other, helper; `0x140627090` other, helper; `0x140628330` other, helper; `0x140629290` other, helper; `0x14062B330` other, helper; `0x14062D580` command-menu predicates, helper; `0x14062E4A0` command-menu predicates, helper; `0x140632AF0` carry / throw, helper; `0x1406335C0` carry / throw, helper; `0x140638540` turn order and round upkeep, helper; `0x14063AA20` turn order and round upkeep, helper; `0x14063C6F0` world map, helper; `0x14063F140` world map, helper; `0x14064D570` other, helper; `0x140669590` other, helper; `0x14066D840` other, helper; `0x140675700` other, helper; `0x140679690` other, helper; `0x14067EEB0` other, helper; `0x1406809C0` other, helper; `0x140682DD0` other, helper; `0x140687D90` other, helper; `0x140689560` other, helper; `0x14068C150` other, helper; `0x14068D6C0` other, helper; `0x140692930` other, helper; `0x14069A770` other, helper; `0x14069F390` other, helper; `0x1406A9D10` other, helper; `0x1406BC840` other, helper; `0x1406C63B0` other, helper; `0x1406CA140` other, helper; `0x1406D38C0` other, helper; `0x1406D4920` other, helper; `0x1406D6540` other, helper; `0x1406D7A50` other, helper; `0x1406DE6F0` other, helper; `0x1406EC790` other, helper; `0x1406EE890` other, helper; `0x1406F2500` other, helper; `0x1406FA4F0` other, helper; `0x140705B40` other, helper; `0x140717110` other, helper; `0x140739740` other, helper; `0x140747CF0` other, helper; `0x14074DF50` other, helper; `0x140755730` other, helper; `0x140772510` other, helper; `0x140776CE0` other, helper; `0x1407814C0` other, helper; `0x1407817C0` other, helper; `0x140782CC0` other, helper; `0x140782FD0` other, helper; `0x140783BE0` other, helper; `0x140785A80` other, helper; `0x140785DA0` other, helper; `0x1407860D0` other, helper; `0x1407A0BF0` other, helper; `0x1407A4220` other, helper; `0x1407ABC60` other, helper; `0x1407AF710` other, helper; `0x1407BC690` other, helper; `0x1407BE160` other, helper; `0x1407C42E0` other, helper; `0x1407CB790` other, helper; `0x1407D0D90` other, helper; `0x1407D8140` other, helper; `0x1407DAFB0` other, helper; `0x1407DE440` other, helper; `0x1407DE6E0` other, helper; `0x1407E2CB0` other, helper; `0x1407F5880` other, helper; `0x140801ED0` other, helper; `0x140805070` other, helper; `0x140805EE0` other, helper; `0x140809E10` other, helper; `0x1408159A0` other, helper; `0x14081D500` other, helper; `0x14081FB70` other, helper; `0x140840B00` other, helper; `0x140854D70` other, helper |
| 4321 | `0x140205CF0` other, helper |
| 4322 | `0x140205CF0` other, helper |
| 4323 | `0x140205CF0` other, helper |
| 4324 | `0x140205CF0` other, helper |
| 4500 | `0x1401EA9C0` movement / pathing, inline |
| 4501 | `0x1401F6300` damage calculation, helper |
| 4502 | `0x1401E7600` carry / throw, inline |
| 4503 | `0x14014E4C0` script commands, helper; `0x140162010` damage calculation, helper; `0x140193760` script commands, helper; `0x1401C13E0` damage calculation, helper; `0x1401C1BC0` other, helper; `0x1401E1B30` evility stat aggregation (potency, growth, status, list rebuild), helper; `0x1401E6E20` other, inline; `0x1401EB390` other, helper; `0x1401F6230` other, helper; `0x1401F8FD0` damage calculation, helper; `0x1401FECC0` damage calculation, helper; `0x140202120` other, helper; `0x140219630` unit stat aggregation, inline; `0x140219980` unit stat aggregation, helper; `0x14021BFB0` script commands, helper; `0x1402D73E0` movement / pathing, helper; `0x140544580` other, helper |
| 4504 | `0x1401E6C30` carry / throw, inline |
| 4505 | `0x140605E30` script commands, inline |
| 4506 | `0x1401E72A0` other, inline; `0x1406055A0` carry / throw, inline |
| 4507 | `0x1401E7600` carry / throw, inline |
| 4508 | `0x1401E6E20` other, inline; `0x140219630` unit stat aggregation, inline |
| 4600 | `0x14082B450` other, helper |
| 4601 | `0x1401FB2E0` damage calculation, inline; `0x1408325F0` other, inline; `0x14083A180` other, inline; `0x14083C040` other, inline; `0x14083CAE0` other, inline; `0x14083D2A0` other, helper |
| 4602 | `0x1401E1A30` other, inline; `0x1401FB2E0` damage calculation, inline; `0x14082B450` other, helper; `0x14083B620` other, inline |
| 4603 | `0x14084EB50` other, inline |
| 5000 | `0x140160720` battle model state, helper; `0x1401AE710` other, helper; `0x1401B1DB0` deployment, helper; `0x140204030` AI decisions, helper; `0x14020D1C0` other, helper; `0x14021BFB0` script commands, helper; `0x1402C8680` AI decisions, helper; `0x1402C8D50` AI decisions, helper; `0x1402C9770` AI decisions, helper; `0x1402C9A80` AI decisions, helper; `0x1402C9D80` AI decisions, helper; `0x1402CA080` AI decisions, helper; `0x1402CAE60` AI decisions, helper; `0x1402CB480` AI decisions, helper; `0x1402CBE60` AI decisions, helper; `0x1402CC3F0` AI decisions, helper; `0x1402CC940` AI decisions, helper; `0x1402CCC00` AI decisions, helper; `0x1402CD000` AI decisions, helper; `0x1402CD560` AI decisions, helper; `0x1402CDDB0` AI decisions, helper; `0x1402CE610` AI decisions, helper; `0x1402CE930` AI decisions, helper; `0x1402CEC00` AI decisions, helper; `0x1402CEFC0` AI decisions, helper; `0x1402CFE50` AI decisions, helper; `0x1402D1AF0` other, helper; `0x1402D4E60` other, helper; `0x1404A7BE0` specials / reinforcements, helper |
| 5002 | `0x140203BB0` other, inline |
| 5003 | `0x14015D240` battle record helpers, inline |
| 5004 | `0x1401FB2E0` damage calculation, helper |
| 5005 | `0x1401FB2E0` damage calculation, helper |
| 5024 | `0x140150170` damage calculation, helper; `0x140150880` other, helper; `0x140150B00` other, helper; `0x14015A1B0` battle flow, helper; `0x14015EEA0` turn order and round upkeep, helper; `0x1401613A0` command-menu predicates, helper; `0x140161790` geometry / misc, helper; `0x140162470` command-menu predicates, helper; `0x140162730` damage calculation, helper; `0x140193760` script commands, helper; `0x1401B0440` damage calculation, inline; `0x1401B0630` damage calculation, inline; `0x1401B9600` evility stat aggregation (potency, growth, status, list rebuild), inline; `0x1401B97D0` evility stat aggregation (potency, growth, status, list rebuild), inline; `0x1401D4F30` unit stat aggregation, inline; `0x1401F8FD0` damage calculation, inline; `0x140203B80` other, helper; `0x14020D1C0` other, helper; `0x1404CDF50` command-menu predicates, helper |
| 5025 | `0x1401FE0D0` damage calculation, helper |
| 5026 | `0x1401511E0` battle model state, inline; `0x140151420` carry / throw, inline; `0x1401E5AF0` turn order and round upkeep, inline; `0x1401E5F60` battle setup, inline |
| 6001 | `0x1401F0280` evility stat aggregation (potency, growth, status, list rebuild), helper; `0x14020D1C0` other, helper |
| 6002 | `0x1401F8FD0` damage calculation, helper |
| 6003 | `0x1401FB2E0` damage calculation, inline |
| 6004 | `0x1401F6300` damage calculation, helper; `0x1401F8700` damage calculation, inline; `0x14020D1C0` other, helper |
| 6005 | `0x1401F8FD0` damage calculation, helper |
| 6006 | `0x1401EADC0` other, helper |
| 6007 | `0x1401F6300` damage calculation, helper |
| 6008 | `0x1402042E0` damage calculation, helper |
| 6009 | `0x1401FB2E0` damage calculation, inline |
| 6010 | `0x1401CE1E0` command-menu predicates, inline; `0x1401F4D80` AI decisions, inline; `0x1401F4F50` other, inline |
| 6011 | `0x140203BB0` other, helper |
| 6012 | `0x1401EC5C0` command-menu predicates, inline |
| 6013 | `0x1401FB2E0` damage calculation, helper |
| 6014 | `0x1401EA9C0` movement / pathing, helper; `0x14020D1C0` other, helper |
| 6015 | `0x1401F0280` evility stat aggregation (potency, growth, status, list rebuild), helper; `0x14020D1C0` other, helper |
| 6016 | `0x1401F0280` evility stat aggregation (potency, growth, status, list rebuild), helper; `0x14020D1C0` other, helper |
| 6017 | `0x1401F8FD0` damage calculation, helper |
| 6018 | `0x1401F8FD0` damage calculation, helper |
| 6020 | `0x1401EB390` other, inline; `0x14020D1C0` other, helper |
| 6022 | `0x1401F6300` damage calculation, helper; `0x14020D1C0` other, helper |
| 6023 | `0x1402069A0` other, helper; `0x140207470` other, helper |
| 6026 | `0x140161A20` damage calculation, helper; `0x140204720` other, helper |
| 6028 | `0x140203BB0` other, helper |
| 6030 | `0x1401E3840` other, helper |
| 6032 | `0x1401EEE30` evility stat aggregation (potency, growth, status, list rebuild), inline; `0x140200D20` damage calculation, inline |
| 6034 | `0x1401C8F40` other, helper; `0x1401C9220` other, helper; `0x1401E6400` other, helper; `0x1401F4FE0` other, helper; `0x1401F50D0` other, helper |
| 6035 | `0x1401E6F20` other, inline; `0x140219630` unit stat aggregation, inline |
| 6036 | `0x1401F0280` evility stat aggregation (potency, growth, status, list rebuild), helper; `0x14020D1C0` other, helper |
| 6037 | `0x1401F0280` evility stat aggregation (potency, growth, status, list rebuild), helper; `0x14020D1C0` other, helper |
| 6039 | `0x140206480` other, inline; `0x14020D1C0` other, helper |
| 6041 | `0x140147290` damage calculation, inline; `0x1401F6300` damage calculation, helper; `0x140207C30` other, inline |
| 6042 | `0x1401E18B0` other, inline; `0x140624030` other, inline |
| 6043 | `0x1401E44D0` battle model state, inline |
| 6044 | `0x1401F6300` damage calculation, helper; `0x1401FB2E0` damage calculation, helper |
| 6045 | `0x140157830` other, inline; `0x1401605E0` carry / throw, inline |
| 6046 | `0x1401E6400` other, helper |
| 6047 | `0x1401FE0D0` damage calculation, helper; `0x14020D1C0` other, helper |
| 6048 | `0x1401F6300` damage calculation, helper; `0x14020D1C0` other, helper |
| 6049 | `0x1401F6300` damage calculation, helper; `0x14020D1C0` other, helper |
| 6051 | `0x1401E7330` carry / throw, inline |
| 6052 | `0x1401FDBC0` damage calculation, inline |
| 6054 | `0x1401F8FD0` damage calculation, helper; `0x14020D1C0` other, helper |
| 6055 | `0x140200810` damage calculation, inline |
| 6056 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline; `0x14020D1C0` other, helper |
| 6057 | `0x1401F0280` evility stat aggregation (potency, growth, status, list rebuild), helper; `0x14020D1C0` other, helper |
| 6058 | `0x1401ED6A0` turn order and round upkeep, inline |
| 6059 | `0x1401F0280` evility stat aggregation (potency, growth, status, list rebuild), helper; `0x14020D1C0` other, helper |
| 6060 | `0x1401F5410` damage calculation, helper |
| 6061 | `0x1401F0280` evility stat aggregation (potency, growth, status, list rebuild), helper; `0x14020D1C0` other, helper |
| 6062 | `0x1401C6480` other, inline; `0x140200390` damage calculation, inline |
| 6063 | `0x140206480` other, helper; `0x14020D1C0` other, helper |
| 6064 | `0x1403C40F0` other, inline |
| 6065 | `0x1401F5410` damage calculation, helper; `0x14020D1C0` other, helper |
| 6067 | `0x1401F8FD0` damage calculation, helper; `0x1401FB2E0` damage calculation, helper |
| 6068 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 6069 | `0x1401E1540` other, helper; `0x1401E1710` other, inline |
| 6070 | `0x1401F0280` evility stat aggregation (potency, growth, status, list rebuild), helper; `0x14020D1C0` other, helper |
| 6071 | `0x1401F5410` damage calculation, inline |
| 6073 | `0x1402016F0` damage calculation, inline |
| 6074 | `0x1401F2A10` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 6075 | `0x1401E1470` other, inline |
| 6077 | `0x140206480` other, helper; `0x14020D1C0` other, helper |
| 6078 | `0x1401F6300` damage calculation, helper; `0x1401FB2E0` damage calculation, helper |
| 6079 | `0x1401F6300` damage calculation, helper; `0x14020D1C0` other, helper |
| 6080 | `0x1401F8FD0` damage calculation, helper; `0x14020D1C0` other, helper |
| 6081 | `0x1401E12F0` other, inline; `0x140533B50` damage calculation, inline |
| 6082 | `0x1401F6300` damage calculation, helper; `0x1401F8700` damage calculation, inline; `0x14020D1C0` other, helper |
| 6083 | `0x1401E11D0` other, inline; `0x1402DA750` movement / pathing, inline |
| 6085 | `0x1401E90F0` other, inline; `0x1405557B0` other, inline |
| 6086 | `0x140200810` damage calculation, inline; `0x14020D1C0` other, helper |
| 6087 | `0x1401B17D0` other, inline |
| 6089 | `0x1401F6300` damage calculation, helper; `0x140201980` damage calculation, inline; `0x14020D1C0` other, helper |
| 6091 | `0x1401E44D0` battle model state, inline |
| 6095 | `0x1401E44D0` battle model state, inline |
| 6096 | `0x1401EB390` other, inline |
| 6097 | `0x1401C13E0` damage calculation, inline; `0x1401C1BC0` other, inline |
| 6098 | `0x1401F8FD0` damage calculation, helper; `0x1401FB2E0` damage calculation, helper |
| 6099 | `0x1401F6300` damage calculation, helper; `0x14020D1C0` other, helper |
| 6102 | `0x1401EE010` turn order and round upkeep, inline; `0x1401EE670` turn order and round upkeep, inline |
| 6103 | `0x1401F8FD0` damage calculation, helper |
| 6104 | `0x1401F0280` evility stat aggregation (potency, growth, status, list rebuild), helper; `0x14020D1C0` other, helper |
| 6106 | `0x1401F8FD0` damage calculation, helper; `0x1401FB2E0` damage calculation, helper |
| 6107 | `0x140157670` battle model state, inline; `0x1401E1050` other, inline; `0x140206070` other, inline; `0x14020D1C0` other, helper; `0x1406357E0` carry / throw, inline |
| 6108 | `0x1401F6300` damage calculation, helper; `0x14020D1C0` other, helper |
| 6109 | `0x1401E5490` other, helper; `0x140227430` command-menu predicates, helper; `0x140257660` other, helper; `0x1402605F0` other, helper; `0x140279220` other, helper; `0x14027BC80` other, helper; `0x14027FCB0` other, helper; `0x140280B30` other, helper; `0x14028BBB0` other, helper; `0x1403E1550` other, helper; `0x14043F110` other, helper; `0x140441190` other, helper; `0x140445E90` other, helper; `0x14045DD30` other, helper; `0x140465420` other, helper; `0x14046CB00` other, helper; `0x14054DB60` deployment, helper; `0x14054F830` deployment, helper; `0x1405F6FB0` other, helper; `0x1405FD140` other, helper; `0x1405FE450` other, helper; `0x140607110` carry / throw, helper; `0x140608330` carry / throw, helper; `0x14060E100` other, helper; `0x140627090` other, helper; `0x140628330` other, helper; `0x140629290` other, helper; `0x14062B330` other, helper; `0x14062D580` command-menu predicates, helper; `0x14062E4A0` command-menu predicates, helper; `0x140632AF0` carry / throw, helper; `0x1406335C0` carry / throw, helper; `0x140638540` turn order and round upkeep, helper; `0x14063AA20` turn order and round upkeep, helper; `0x14063C6F0` world map, helper; `0x14063F140` world map, helper; `0x14064D570` other, helper; `0x140669590` other, helper; `0x14066D840` other, helper; `0x140675700` other, helper; `0x140679690` other, helper; `0x14067EEB0` other, helper; `0x1406809C0` other, helper; `0x140682DD0` other, helper; `0x140687D90` other, helper; `0x140689560` other, helper; `0x14068C150` other, helper; `0x14068D6C0` other, helper; `0x140692930` other, helper; `0x14069A770` other, helper; `0x14069F390` other, helper; `0x1406A9D10` other, helper; `0x1406BC840` other, helper; `0x1406C63B0` other, helper; `0x1406CA140` other, helper; `0x1406D38C0` other, helper; `0x1406D4920` other, helper; `0x1406D6540` other, helper; `0x1406D7A50` other, helper; `0x1406DE6F0` other, helper; `0x1406EC790` other, helper; `0x1406EE890` other, helper; `0x1406F2500` other, helper; `0x1406FA4F0` other, helper; `0x140705B40` other, helper; `0x140717110` other, helper; `0x140739740` other, helper; `0x140747CF0` other, helper; `0x14074DF50` other, helper; `0x140755730` other, helper; `0x140772510` other, helper; `0x140776CE0` other, helper; `0x1407814C0` other, helper; `0x1407817C0` other, helper; `0x140782CC0` other, helper; `0x140782FD0` other, helper; `0x140783BE0` other, helper; `0x140785A80` other, helper; `0x140785DA0` other, helper; `0x1407860D0` other, helper; `0x1407A0BF0` other, helper; `0x1407A4220` other, helper; `0x1407ABC60` other, helper; `0x1407AF710` other, helper; `0x1407BC690` other, helper; `0x1407BE160` other, helper; `0x1407C42E0` other, helper; `0x1407CB790` other, helper; `0x1407D0D90` other, helper; `0x1407D8140` other, helper; `0x1407DAFB0` other, helper; `0x1407DE440` other, helper; `0x1407DE6E0` other, helper; `0x1407E2CB0` other, helper; `0x1407F5880` other, helper; `0x140801ED0` other, helper; `0x140805070` other, helper; `0x140805EE0` other, helper; `0x140809E10` other, helper; `0x1408159A0` other, helper; `0x14081D500` other, helper; `0x14081FB70` other, helper; `0x140840B00` other, helper; `0x140854D70` other, helper |
| 6110 | `0x1401F0280` evility stat aggregation (potency, growth, status, list rebuild), helper; `0x14020D1C0` other, helper |
| 6111 | `0x1401F0280` evility stat aggregation (potency, growth, status, list rebuild), helper; `0x14020D1C0` other, helper |
| 6112 | `0x1401E90F0` other, inline; `0x1405557B0` other, inline |
| 6113 | `0x1401F8FD0` damage calculation, helper; `0x1401FB2E0` damage calculation, helper |
| 6114 | `0x1401F5410` damage calculation, helper; `0x14020D1C0` other, helper |
| 6115 | `0x1401EC5C0` command-menu predicates, inline; `0x1401F6300` damage calculation, helper |
| 6117 | `0x1401FB2E0` damage calculation, inline |
| 6118 | `0x1401EB980` damage calculation, helper |
| 6119 | `0x1401EC0B0` other, inline |
| 6120 | `0x1401F8FD0` damage calculation, helper |
| 6121 | `0x1401F0280` evility stat aggregation (potency, growth, status, list rebuild), helper; `0x14020D1C0` other, helper |
| 6122 | `0x1401F6300` damage calculation, helper |
| 6123 | `0x14020D1C0` other, helper |
| 6125 | `0x1401E3CE0` other, helper; `0x140527060` battle flow, helper |
| 6126 | `0x1401AEB10` damage calculation, inline |
| 6128 | `0x1401F6300` damage calculation, helper; `0x14020D1C0` other, helper |
| 6129 | `0x1401EC5C0` command-menu predicates, inline; `0x1401EEE30` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 6130 | `0x1401EC5C0` command-menu predicates, inline; `0x1401EEE30` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 6131 | `0x1401EC5C0` command-menu predicates, inline |
| 6132 | `0x1401EC5C0` command-menu predicates, inline |
| 6133 | `0x1401FE0D0` damage calculation, helper |
| 6134 | `0x1401FE0D0` damage calculation, helper |
| 6135 | `0x1401FE0D0` damage calculation, helper |
| 6136 | `0x1401FE0D0` damage calculation, helper |
| 6137 | `0x1401F6300` damage calculation, helper; `0x14020D1C0` other, helper |
| 6138 | `0x1401F6300` damage calculation, helper; `0x14020D1C0` other, helper |
| 6139 | `0x140552D20` other, inline |
| 6140 | `0x1401ADA40` other, inline |
| 6142 | `0x1401FB2E0` damage calculation, helper |
| 6143 | `0x1401FB2E0` damage calculation, helper |
| 6144 | `0x1401F8FD0` damage calculation, helper |
| 6145 | `0x1401E5AF0` turn order and round upkeep, inline; `0x1401E5F60` battle setup, inline |
| 6146 | `0x1401F6300` damage calculation, helper; `0x1401F8700` damage calculation, inline; `0x14020D1C0` other, helper |
| 6147 | `0x1401F6300` damage calculation, helper; `0x1401FB2E0` damage calculation, helper |
| 6148 | `0x1401F8FD0` damage calculation, helper |
| 6200 | `0x1401E3840` other, inline |
| 6300 | `0x1401EEE30` evility stat aggregation (potency, growth, status, list rebuild), inline |
| 6301 | `0x140157670` battle model state, inline; `0x1402D0D60` other, inline; `0x140527060` battle flow, inline |
| 6302 | `0x1401EADC0` other, inline |
| 6303 | `0x1401F0280` evility stat aggregation (potency, growth, status, list rebuild), helper; `0x14020D1C0` other, helper |
| 6304 | `0x1401EEE30` evility stat aggregation (potency, growth, status, list rebuild), inline; `0x1401F5410` damage calculation, helper |
| 6305 | `0x1401E0E70` other, inline; `0x1401EB390` other, inline |
| 6306 | `0x1401F0280` evility stat aggregation (potency, growth, status, list rebuild), helper; `0x14020D1C0` other, helper |

## Sources
D5 Mod Kit development fork, evility consumer catalog (EX-01, `catalog.json`, 2026-10); counts match
[functions.md](functions.md#where-the-game-checks-for-evilities).
