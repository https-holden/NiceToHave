# Triage Labels

The skills speak in terms of five canonical triage roles. This repo has no label strings:
the roles map onto the vault's `status` and `kind` fields (`nicetohave-roadmap/CLAUDE.md`).

| Label in mattpocock/skills | In this repo | Meaning |
| -------------------------- | ------------ | ------- |
| `needs-triage` | An Ideas note with `status: Open` that no build's `ideas:` list names | Owner has to evaluate it |
| `needs-info` | An Ideas note with `kind: SPEC` | A decision has to be written into the note before anyone can build it |
| `ready-for-agent` | A Build note with `status: Planned` | Fully specified; its `## Prompt` starts a session |
| `ready-for-human` | A Build note step marked as the owner's (a dashboard, a login, a real device), or `status: In review` | Needs the owner; the agent says what to check and waits |
| `wontfix` | An Ideas note with `status: Culled` and the reason in the body | Decided against; the note stays so the next person finds the answer |

Applying a role means editing frontmatter, then `python3 nicetohave-roadmap/bin/reindex.py`.
Never create a GitHub or GitLab label.
