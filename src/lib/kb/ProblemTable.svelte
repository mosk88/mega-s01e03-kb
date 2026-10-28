<script lang="ts">
  import reports from '#lib/data/reports-4471.json';
  import notes from '#lib/data/desk-notes.json';
  import { causeOf, uniqueById, type Report } from '#lib/kb/causes';
  import { tenantOf } from '#lib/kb/locales';

  // One row per problem merged into the ticket, with the issue that now tracks it.
  // Counted the desk's way (CauseTable): a report the desk read as a one-day move (desk-notes.json) stays counted
  // under the cause it is filed by, and also counts as a shift.
  let { ticket, issues }: { ticket: number; issues: Record<'parser' | 'hint' | 'shift', string> } = $props();

  type Problem = keyof typeof issues;
  const WHAT: Record<Problem, string> = {
    parser: 'shared parser reads day-first dates month first',
    hint: 'booking-v3 form hints MM/DD/YYYY to day-first tenants',
    shift: 'date moves by one day (time zone)',
  };

  const merged = $derived(uniqueById(reports as Report[]).filter((r) => r.mergedInto === ticket));
  const inProblem = (r: Report, p: Problem | 'unplaced'): boolean =>
    p === 'shift' ? notes.oneDayMove.includes(r.id) : (causeOf(r, tenantOf(r)) ?? 'unplaced') === p;
  const rows = $derived(
    (['parser', 'hint', 'shift', 'unplaced'] as const)
      .map((p) => {
        const rs = merged.filter((r) => inProblem(r, p));
        const days = rs.map((r) => r.receivedAt.slice(0, 10)).sort();
        return { p, n: rs.length, tenants: new Set(rs.map((r) => r.tenant)).size, first: days[0], last: days[days.length - 1] };
      })
      .filter((row) => row.n > 0),
  );
</script>

<p class="sum"><strong>{merged.length}</strong> reports merged into #{ticket} · <strong>{rows.length}</strong> problems</p>

<table>
  <thead><tr><th>problem</th><th>reports</th><th>tenants</th><th>seen</th><th>ticket</th></tr></thead>
  <tbody>
    {#each rows as row (row.p)}
      <tr>
        <td><code>{row.p}</code><br /><span class="what">{row.p === 'unplaced' ? 'no tenant on file' : WHAT[row.p]}</span></td>
        <td>{row.n}</td>
        <td>{row.tenants}</td>
        <td>{row.first} → {row.last}</td>
        <td>{#if row.p !== 'unplaced'}<a href={issues[row.p]}>{issues[row.p].replace(/^.*\/issues\//, 'issue #')}</a>{:else}none{/if}</td>
      </tr>
    {/each}
  </tbody>
</table>

<p class="what">The {merged.filter((r) => notes.oneDayMove.includes(r.id)).length} <code>shift</code> reports are also counted under the cause they are filed by, as the desk counts them.</p>

<style>
  .sum { font-size: 1.1rem; }
  .what { color: var(--muted); font-size: 0.85em; }
</style>
