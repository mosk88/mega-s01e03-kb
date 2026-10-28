# booking-v3 date hint says MM/DD/YYYY to day-first tenants (33 reports from #4471)

**Reports covered:** 33 of the 412 merged into #4471 · 3 tenants (alder.physio, copper.studio, pale.harbour) · 2026-04-17 → 2026-09-22

booking-v3 prints `MM/DD/YYYY` under its date field, but reads the date the tenant's way, day first. People type what the hint says, so their dates come out flipped. Only form reports from tenants on booking-v3.

- Looks the same as #1 in the body (day and month swapped), but the parser fix will not close it: the fix is the form's hint.
- Intake tagged these by browser locale (`hint` only for en-US browsers): 18 tagged `hint`, 15 tagged `parser`. The tenant decides the cause, not the browser.
- Not tracked anywhere before this ticket.
