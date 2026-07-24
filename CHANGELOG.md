# Changelog

Tracks changes to the process and the daily-scan prompt. The prompt itself carries no version stamp — git history + the date on each daily post are the source of truth; this log is the human-readable summary.

## 2026-07-24 — Two-tier scan + weekly review meeting
Introduces a standing weekly SOC 2 Change Management Review (Wednesdays) as the decision forum, and splits the scan into two tiers to feed it.
- **`scan-prompt.md`:** split the single daily scan into two modes.
  - *LIGHT* (Mon/Wed/Thu/Fri) — an exception-based tripwire that posts a one-line heartbeat on quiet days and alerts only on shipped-un-triaged items, undecided imminent ships, new high-confidence Majors, or break-glass events.
  - *PREREAD* (Tue) — the comprehensive weekly digest and agenda for the Wednesday review, with a lean top-level post (TL;DR + big picture) and the detail in-thread. Adds §5 coverage-vs-target and §6 open-process-questions.
  - Added a bounded recently-shipped (Done) lookback to power the un-triaged check. Absence of a label alone never triggers it — no label still means Minor by default; it fires only when a shipped item was a prior open candidate or plausibly warranted a Major review.
  - State logic now treats the posted weekly meeting notes and the EM/engineer Linear update as the authoritative "resolved" signal.
- **`change-management-plan.md`:** §6 rewritten as the two-tier scan; §7 rewritten around the weekly review meeting and the notes-to-channel + Linear-by-EM decision-recording loop; §8 and the appendix updated.
- **`README.md`:** updated the "how the review works" section for the two-tier cadence and the weekly meeting.

## 2026-06-29 — Initial release
Establishes this repo as the canonical home for the change-management process and the daily review prompt.
- **`scan-prompt.md`:** read-only, state-aware daily scan. Posts a TL;DR plus three sections — big-picture status, in-process major changes with an 8-step health check, and candidates awaiting decision (with queue age). Forward-looking (excludes Done/Canceled). Written for a non-expert, cross-team audience. No targets in the output; the agent reports facts only. No @-mentions (humans pull people in).
- **`change-management-plan.md`:** migrated and reorganized from the v16 Google Doc, incorporating the emergency-change issue template, the `Minor change` labels, the permanent multi-window framing, and the AI operating model.

### Prompt lineage before this repo (reference)
- 6/26: first scheduled scan — flag-only, dedupe via channel history.
- 6/29: richer per-flag format (project, team, status, label, people, labeled reason); "write for a non-expert, cross-team audience" principle; then state-aware three-section post with a TL;DR, factual (no-target) reporting, and the read-only model — the version captured here.
