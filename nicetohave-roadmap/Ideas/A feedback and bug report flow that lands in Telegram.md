---
type: idea
status: Open
kind: NOW
area: ""
created: 2026-10-26
---

# A feedback and bug report flow that lands in Telegram

The owner's ask of 26 Oct 2026, the reason this repo exists: the feedback and bug flow just got working in Speaker Tape, sending reports to Telegram, and every app should be able to reuse it. The Speaker Tape code is around `apps/api/src/telegram.ts`, `apps/api/src/alerts.ts` and `apps/api/src/routes/reports.ts`. The bot token and chat id go in `.env`, never in the repo. No rules note yet: the next step is one like [[Notice card and the Add to Home Screen tip]], written from that code.

## Notes

<!-- reindex:builds -->
<!-- /reindex:builds -->
