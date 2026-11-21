# Keeping game text out of a public mod repository

Mod data often needs a game's names and descriptions: to label a converted skill, to say which source evility a new one
replaces, to build a pack's name. Copying those strings into the repository publishes game text, which this wiki's rule 1
(and most projects' licences) forbid. This page describes a technique: **store a hash of the text, and resolve it from the
user's own game copy at build time.** It is the method the D5 Mod Kit development fork adopted in November 2026 for its
importers.

**Status:** method used by this project. Everything is **Verified offline** (resolved and compared against the literal
files, and tests that fail on a regression); the effect on a user's finished pack is the same text the pack always had.

## The reference forms

A text field holds a reference instead of a string:

```json
"name": {"@text": "<SHA-256 of the exact final string>"}
"label": {"@authored": "wording the project wrote itself"}
```

- **`@text`** is the SHA-256 of the **exact string the pack will receive**, not of a normalised form. The build looks the
  hash up in a pool of every string of the user's own tables (the prepared source folder, see
  [Building packs from your own game copies](building-packs-from-your-own-game-copies.md)) and writes the string it finds.
- **`@authored`** is the project's own wording (review labels, notes). It stays as text, but a check confirms it is not a
  game string.
- Anything that **builds** a string around game text has its own form: a description with its numbers filled in (a template
  hash plus arguments, with options for the percent sign and a trailing full stop), a fixed prefix or suffix plus a
  looked-up text, a list of segments mixing project wording and references, a skeleton with marks, and a "parts" form
  for a project sentence that quotes a game name. Each resolves at use.
- A pool can index **spelling variants** of each string (a Unicode-normalised form, and a plain-ASCII fold of punctuation
  such as the minus sign a game used in a name), so a reference resolves across the source edition's punctuation.
- Keep one reference per field rather than per language when a source writes the same string into every language column.

## Rules that keep it honest

1. **A build that cannot resolve a reference refuses.** It never writes a placeholder, and its error names the JSON path
   (which names the pack or skill), the digest and how to supply the missing source.
2. **Resolve at use**, in the code that needs the string. Tools with no build context find the pools themselves (the
   installed kit's pristine backups, else a clean game copy named by a setting) and refuse, naming what is missing.
3. **Convert with a tool that proves nothing changed.** The migration tool rewrites a file only when the resolved document
   equals the old literal one; it can run as a dry check, and it is idempotent.
4. **Generators emit references.** A generator that writes a literal again is caught by a test; the test fails when any
   name, title, description or text field of the listed files is a bare string, when a reference is malformed, and when an
   unresolved hash stops naming its field. Tests use a stub pool (every hash answers a placeholder) so they run without
   game files.
5. **Convert prose only where it quotes.** Project wording is converted only where it quotes a description (a template, or
   a game string of 25 characters or more), never for a bare name. A note that quotes a description carries the quote as an
   inline marker that the writer turns into segments.
6. **Verify against the old files.** A verifier resolves every reference from the real pool and compares the result with
   the literal file at an older revision.
7. **Keys are not text.** A reference list is never a dictionary key (a key would hold the string again).

## Auditing a repository

How the need was found, so another project can repeat it:

1. Scan every tracked JSON file for string values under name, title, description and text keys, and for Japanese
   characters; ignore values that are already references and schema column names. Check the hits by hand. Counts from a
   scan like this are approximate; code files need a spot check, since strings in Python literals are easy to miss.
2. Count by file and game: names only, descriptions as well, or text in several language columns.
3. Separate **tracked in a repository** from **published**: compare the local copies of the remote's references with
   the remote's own list, and check whether the commits that added the text are reachable from the public branches.
   Private storage is not publication, but anything built from that branch would ship the text.
4. Take a baseline: an upstream project may already ship some names as labels. Say what it ships before judging what is new.

## Limits

- **Hashing is forward-only.** History still holds the old text. Removing it needs a history rewrite, which changes every
  commit hash that other notes and worktrees refer to, and a forced push to every copy; only worth it if a private
  repository becomes public.
- **A hash of a short string is guessable** by anyone who owns the game (they can hash every string and compare). The point is
  to **avoid distributing** the text, not to hide it from game owners.
- A project's own translations of a game's text (a provisional English name for a Japanese one) are the project's wording
  and stay as `@authored`, but the **source** text beside them is the reference.
- The build is only as portable as the source folder: a missing prepared file stops the build with a message, not a
  half-written pack.

## Sources

The D5 Mod Kit development fork (a fork of eraser4521's [D5 Mod Kit](https://github.com/eraser4521/D5ModKit), MIT; the text-reference method is a fork addition, not upstream work): `docs/TEXT_REFS.md`
(the forms, the pools per game, the checks). This project's per-game conversions and a repository audit, 2026-11-20.
