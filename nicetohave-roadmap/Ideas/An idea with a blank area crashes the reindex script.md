---
type: idea
status: Open
kind: NOW
area: roadmap
created: 2026-10-26
---

# An idea with a blank area crashes the reindex script

Found 26 Oct 2026 while writing the first idea. The idea template and the vault contract both say to leave `area` blank on capture (`area: ""`), but the frontmatter reader turns an empty value into a list (`fm[key] = val if val else []`), and the area grouping then fails with "cannot use 'list' as a set element". So the first idea written from the template stops `bin/reindex.py`. Either an empty value should read as an empty string, or the contract should give a default area for capture. Until then, an idea gets a placeholder area.

## Notes

<!-- reindex:builds -->
<!-- /reindex:builds -->
