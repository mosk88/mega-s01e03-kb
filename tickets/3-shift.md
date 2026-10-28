# Dates move by one day: time zone dropped or applied twice (5 reports from #4471)

**Reports covered:** 5 of the 412 merged into #4471 · 5 tenants · 2026-04-02 → 2026-10-04 · R-41202, R-41209, R-41236, R-41344, R-41620

These are not flipped dates:
- "Invite says Tuesday 12 May. Calendar says Monday 11 May."
- "Tuesday 6 October showed as Monday."
- "Client in Lisbon got Tuesday. Client in Toronto got Monday. Same meeting."
- "The invite arrived before I made the booking."
- "Every booking in June is one day off. Only June."

The desk read them as a one-day move (`desk-notes.json`) but filed them under the parser by tenant. The parser fix will not close them.
