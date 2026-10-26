# NiceToHave

Holden's public toolkit of reusable web app pieces: features, flows and UI elements that worked
in one app and should be droppable into the next (a Telegram feedback/bug flow, an
add-to-home-screen prompt). Mostly for Holden, open source so others can use it. The apps are
web for now and may go iOS later, so keep an element's logic separable from its UI where that is
cheap.

**Secrets never get committed.** The repo is public. Tokens, chat IDs and API keys are read from
environment variables; each element documents the ones it needs in a committed `.env.example`,
and `.env` stays git-ignored. Strip app-specific names and keys when porting code in from
another repo.

## Working from the roadmap vault

Planning lives in `nicetohave-roadmap/`, an Obsidian vault tracked in this repo. `Ideas/` is
raw capture, one file per idea. `Builds/` is units of work handed to a Claude Code session, one
file per session, the prompt in the note body. Start at `nicetohave-roadmap/Home.md`;
`nicetohave-roadmap/CLAUDE.md` is the contract and should be read before editing anything there.

When a session starts from a build it says so, and the build's scope is the scope. When it ends
it writes back: `## Outcome`, `## Commits`, what was not done, and every discovery as its own
file in `nicetohave-roadmap/Ideas/`. Then `python3 nicetohave-roadmap/bin/reindex.py`, and commit.

## Context

Search with the Grep tool or `rg`, never `grep -r`, `find` or `ls -R` over the repo; tail logs,
never read them whole. Every `CLAUDE.md` is a signpost under a character budget. Details: the
"Context" section of `nicetohave-roadmap/CLAUDE.md`.

## Agent skills

### Issue tracker

Work is tracked in the roadmap vault, `nicetohave-roadmap/`: an Ideas note is an issue, a Build
note is a ticket, a spec is a Research note. Never GitHub Issues. See
`docs/agents/issue-tracker.md`.

### Triage labels

The five triage roles map onto the vault's `status` and `kind` vocabulary, not label strings.
See `docs/agents/triage-labels.md`.

### Domain docs

Single-context: `GLOSSARY.md` and `docs/adr/` at the repo root. See `docs/agents/domain.md`.
