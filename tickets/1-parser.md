# Shared parser reads day-first dates month first (379 reports from #4471)

**Reports covered:** 379 of the 412 merged into #4471, counted as the desk counts them (CauseTable) · 11 tenants · 2026-04-01 → 2026-10-04

Every tenant on file writes the day before the month (en-GB, en-IE, nl-NL, de-DE, fr-FR, es-ES, it-IT, pt-PT, pl-PL, cs-CZ, da-DK). The shared parser reads every date month first, so day and month swap: "Our 7 April reminder went out on 4 July."

- Reaches every day-first tenant through app, email and form (except form on booking-v3, see #2).
- This is the problem the queued #4471 fix addresses.
- Counted by `ProblemTable` in `src/routes/kb/incidents/4471-problems/+page.svx`: `causeOf(...) === 'parser'`. 5 of these 379 are the one-day moves in #3: the desk files and counts them here, by tenant.
