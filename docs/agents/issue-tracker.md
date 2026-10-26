# Issue tracker: the roadmap vault

Issues, tickets and specs for this repo live in `nicetohave-roadmap/`, an Obsidian vault tracked
in git. It is the only place that says what is next. Never create a GitHub or GitLab issue, and
do not use `.scratch/`.

Read `nicetohave-roadmap/CLAUDE.md` before writing a note, and load the `obsidian:obsidian-markdown`
skill first. Start new notes from `nicetohave-roadmap/_templates/`. After any edit, run
`python3 nicetohave-roadmap/bin/reindex.py` and commit what it rewrote.

## What maps to what

| Skill vocabulary | In this repo |
| ---------------- | ------------ |
| An issue | A note in `nicetohave-roadmap/Ideas/`, one per idea, bug or dream. `type: idea`, `status: Open`, `kind` one of `NOW`/`SPEC`/`POLISH`/`DREAM`, `area`, `created`. Plain title as file name. |
| A ticket | A note in `nicetohave-roadmap/Builds/`, one per session of work, named `NTH-<n> <title>.md`. `type: build`, `status: Planned`, `kind`, a unique integer `order`, `repo_area`, and `ideas:` listing the Ideas notes it covers. The session's prompt goes under `## Prompt`. |
| A spec | A note in `nicetohave-roadmap/Research/` (`type: research`, `area`, `created`): a document builds start from. The builds cut from it link to it by wikilink. |
| A comment | Dated prose appended to the note. A correction to an Outcome is appended, never edited in. |
| Closing | An idea goes to `Done` with a `## Resolved in` section, or to `Culled` with the reason. A build goes to `Shipped` (or `In review`, `Abandoned`) with `## Outcome` and `## Commits`. |

The relation between ideas and builds is authored in exactly one place: the `ideas:` list in
the build's frontmatter. Everything between `<!-- reindex:... -->` markers is generated.

## Operations

- **Create an issue**: write `nicetohave-roadmap/Ideas/<Plain title>.md` from `_templates/idea.md`.
  No colon, slash, pipe, hash or square bracket in the name. No em or en dashes anywhere.
- **Read an issue or ticket**: `cat` the file. Read the vault with `cat`, `grep` and `git diff`.
- **List open issues**: `grep -l "^status: Open" nicetohave-roadmap/Ideas/*.md`, or read the
  generated table in `nicetohave-roadmap/Ideas.md`.
- **List tickets in run order**: the queue in `nicetohave-roadmap/Home.md`.
- **Link a ticket to its issues**: add the idea to the build's `ideas:` list, as a quoted
  wikilink, then run the script.
- **Change state**: edit `status` (and `kind` for an idea) in frontmatter, then run the script.
  Allowed values are in `nicetohave-roadmap/CLAUDE.md`; the script refuses anything else.
- **Order the queue**: set `order` on the build. Lowest runs first; no two builds share one.

## When a skill says "publish to the issue tracker"

A finding, bug or idea becomes an Ideas note. A unit of work becomes a Build note with its
`ideas:` list filled in. A spec becomes a Research note. Then run the script.

## When a skill says "fetch the relevant ticket"

`cat` the Build note the person named by id (`ls nicetohave-roadmap/Builds/ | grep "^NTH-07 "`),
including its `## Prompt`. The note's scope is the scope.

## Wayfinding operations

Used by `/wayfinder`. The **map** is a Research note; each **child** ticket is a Build note.

- **Map**: `nicetohave-roadmap/Research/<Effort> map.md` holding Notes / Decisions-so-far / Fog.
- **Child ticket**: a Build note, `kind: Investigation` for `research`/`prototype`/`grilling`
  questions and `kind: Implementation` for a `task`, linking the map by wikilink in its first
  paragraph.
- **Blocking**: `order`. A ticket's blockers get lower numbers, and a `Blocked by:` line near
  the top names them by wikilink. A ticket is unblocked when every blocker is `Shipped`.
- **Frontier**: the first `Planned` build in `Home.md` whose blockers are all `Shipped`.
- **Claim**: set `status: In progress` and run the script, before any work.
- **Resolve**: answer under `## Outcome`, set `status: Shipped`, run the script, then append a
  pointer (gist plus wikilink) to the map's Decisions-so-far.
