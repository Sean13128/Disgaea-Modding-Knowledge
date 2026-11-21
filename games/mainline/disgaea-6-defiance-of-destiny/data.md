# Disgaea 6 — Data Tables

Stored in `data/data.dat` (FAFULLFS). **Not documented yet**, and there's little public groundwork:

- Makai Kit's public `databases-d6` crate defines only **two** record types, `characlass` (`id`, `name`,
  `editor_name`) and `gameflag`, plus a six-language string type (`jp`, `en`, `fr`, `zh_cn`, `zh_cht`, `kr`).
- Its public D6 loader doesn't repack tables at all (see [modifiable.md](modifiable.md)).

Makai Kit decodes D6's two tables with the same serializer as D7's, so D6 tables are presumably stored the same way
(count, then size-prefixed records with length-prefixed strings: see [D7 data.md](../disgaea-7-vows-of-the-virtueless/data.md)).
So D6 tables still need to be mapped. Disgaea 7's tables ([D7 data.md](../disgaea-7-vows-of-the-virtueless/data.md))
are the closest guide: same engine family, and D7 reuses D6 assets.

## Open questions
- The full table list and fields.
- Whether D6's evilities use the same data-driven effect system as D7's ([D7 data](../disgaea-7-vows-of-the-virtueless/data.md)).

## Sources
[Makai Kit](https://github.com/HybridEidolon/makaikit) source, last commit 2023-11-01 (`databases-d6`, `modloader-d6`). **Verified offline** (source read).
