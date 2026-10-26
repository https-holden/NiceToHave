# NiceToHave roadmap

This directory is an Obsidian vault and it is the source of truth for planning NiceToHave.
Seeded 2026-10-26 from the ObsidianVaultSeed planning archetype. It is also just Markdown, and a
Claude Code session reads it with `cat`, `grep` and `git diff`.

**No community plugins.** Core Obsidian plus two Python scripts; Bases does what Dataview did.

## Obsidian syntax

**Load the skill before the work:** `obsidian:obsidian-markdown` before a note,
`obsidian:obsidian-bases` before a `.base` file, `obsidian:obsidian-cli` only for the running
app. They come from `kepano/obsidian-skills` (`/plugin marketplace add kepano/obsidian-skills`,
then `/plugin install obsidian@obsidian-skills`). On top of them:

- **Wikilinks inside the vault**, Markdown links for URLs only: Obsidian tracks renames and
  the script matches `[[...]]`.
- **No tags.** `type`, `kind`, `status` and `area` are the axes, and the script validates them.
- **Callouts sparingly.** `> [!note] Superseded` is the shape for a dated correction.
- **A `.base` view's `sort:` key is valid** though the skill does not list it. A new
  frontmatter field goes in the view's `order:` list too, or it shows no column.

## The note types

`Ideas/` is raw capture: one note per idea, annoyance, bug or dream, never worked from
directly. Frontmatter:

| Field | Values |
|---|---|
| `type` | `idea` |
| `status` | `Open`, `Done`, `Culled` |
| `kind` | `NOW`, `SPEC`, `POLISH`, `DREAM` |
| `area` | optional; leave blank on capture. Set it once the idea is built, to the toolkit element it landed in (that element's folder name) |
| `created` | ISO date |

`kind` is the triage axis:

- **NOW**: broken or missing in a way a user meets.
- **SPEC**: wanted, but a real decision has to be made first. Write the decision into the
  note when it is made, then retype it.
- **POLISH**: correct already, just not good yet.
- **DREAM**: someday, unscoped, keep it visible.

`Builds/` is units of work, one note per Claude Code session. The prompt that starts the
session is in the body under `## Prompt`. Frontmatter:

| Field | Values |
|---|---|
| `type` | `build` |
| `status` | `Planned`, `In progress`, `In review`, `Shipped`, `Abandoned` |
| `kind` | `Implementation`, `Fix`, `Investigation`, `Docs` |
| `order` | integer, run order across the whole vault |
| `repo_area` | which part of the repo it touches |
| `model` | which model the build is scoped for |
| `roadmap` | the milestone it serves, free text |
| `ideas` | list of wikilinks to Ideas notes, each quoted |

`Research/` holds specs, findings and how things work in this repo: one note each, `type:
research`, `area`, `created`. It is prose, not a queue, so the script does not index it. A
build cut from a spec links to it by wikilink in its first paragraph.

`Elements.md` is the hand-kept catalog of elements by name, one row each. Add a row when an
element gets its first idea note, and update its status when a rules note or code lands. It is
not generated: edit it directly.

A build is named with a short prefix and a number, so it can be said aloud and found by grep
(`NTH-07 Title`). New notes start from `_templates/`.

## The one rule that keeps it consistent

**The relation is authored in exactly one place: the `ideas:` list in a build's frontmatter.**
An idea has no `builds:` field. Its `## Builds` section, a build's `## Ideas in scope` and
every table in `Home.md`, `Builds.md` and `Ideas.md` are generated between `reindex:`
markers and overwritten on every run, so they are never hand-edited. `vault.css` hides the
markers in Live Preview, scoped away from code blocks so a prompt's comment lines still show.

Read the relation as **"appears in"**, not "closed by": a build lists an idea it discovered as
well as one it fixes. The idea's `## Resolved in` section says where it actually closed.

## After any edit, run the script

```
python3 nicetohave-roadmap/bin/reindex.py          # regenerate, then commit the result
python3 nicetohave-roadmap/bin/reindex.py --check  # writes nothing, exits 1 on drift
```

The `.base` files are for browsing in Obsidian; the generated tables are the same data as
plain text. The script prints `PROBLEM` for an unknown `status` or `kind` and for an `ideas:`
link to a missing note (how a build silently loses an item: fix it on the spot), and `WARN`
for a `CLAUDE.md` over budget (see "Context").

## Working a build

A session that starts from a build says so, and the build's scope is the scope: the items it
lists are the work, and anything else is out of scope however related and easy.

When the session ends, it updates the build note as part of the same work. Four things, and
the fourth is the one that gets skipped:

1. `## Outcome`, one line per scoped item, saying what actually shipped.
2. `## Commits`, hashes and whether they were pushed.
3. Anything deliberately not done, and why.
4. **Discoveries become new notes in `Ideas/`**, one file each, written for someone who was
   not in the session, and added to the build's `ideas:` list. A discovery that lives only in
   a transcript is lost.

Then the build goes to `Shipped`, every idea it closed goes to `Done` with a `## Resolved in`
section naming the commit, and the script runs. Discoveries are expected: "I noticed X is
fragile" is an Ideas note, not a mid-session fix. A session never reorders or widens its own
scope on what it finds.

## The Stop hook

`bin/session_check.py` (wired in `.claude/settings.json` when seeded with it) keeps a session
whose transcript names a build note from ending until that build is `Shipped` or `Abandoned`
with `## Outcome` and `## Commits` filled, and `reindex.py --check` passes. If it fires on a
build this session only read, say "did not work it" and stop again. Its docstring says why it
exists and what it never checks; run it with `--self-test` after touching it.

## Context

What a session reads without asking is spent before the work starts.

- **Every `CLAUDE.md` is a signpost.** It holds what every session needs (here: the schema,
  the one rule, the write-back) and points at the rest. History in one is a verdict plus a
  wikilink. `reindex.py` warns when any `CLAUDE.md` in the repo passes the budget set at the
  top of that script.
- **Search with the Grep tool or `rg`**, which skip what git ignores; `grep -r`, `find` and
  `ls -R` over the repo wade through stale worktrees and build output. Logs are tailed, never
  read whole.
- **Big reference data is asked of its tool**: an index first, then one entity, then one
  field. A data file that says "ask the tool" means it.

## Concurrent sessions and corrections

`Home.md`, `Ideas.md` and `Builds.md` are fully generated and the only files two sessions
collide on: take either side and rerun the script. The last live session runs it and commits.

A later session that finds an earlier outcome wrong **appends a dated correction** and leaves
the original: the wrong claim and its date are evidence.

## House rules

- No em dashes or en dashes, anywhere, including in these notes.
- Relative dates become absolute ones in a note.
- One idea per file. Two questions that could be decided independently are two notes.
