# Changelog

Tracks changes to the process and the daily-scan prompt. The prompt itself carries no version stamp — git history + the date on each daily post are the source of truth; this log is the human-readable summary.

## 2026-08-03 — Major-change checklist becomes one parent issue with 8 sub-issues
Adopted by the review group at the 2026-07-29 SOC 2 Change Management Review. Previously the *SOC 2 Major Change* **project** template created the 8 checklist steps as 8 loose issues in the project, where they sat undifferentiated among the team's development issues and nothing pulled them together. They are now the sub-issues of a single *SOC 2 Major Change* parent issue, created from an **issue** template.
- **`change-management-plan.md`:** §3 reorganized around issue templates (Major + Emergency-issue) with the Emergency project template listed separately; notes that an issue template can be applied at any time, so a project reclassified Major follows the same path as one that starts out Major — the Linear-API workaround for reclassified projects is gone. §4 states the parent/sub-issue structure and standardizes the step titles to title case as used in Linear. §7 spells out the Major follow-through (label the project → apply the issue template → complete the steps → record in the Authorization sub-issue). §10 and the appendix updated.
- **`scan-prompt.md`:** the 8-step health check now reads the sub-issues of the parent checklist issue rather than loose issues in the project. Identifies the parent by its sub-issues being the 8 gates, not by title (older ones vary, e.g. "SOC2 Compliance"). A `Major change` project with no such parent issue is reported as having no checklist; projects that predate the template and still hold the 8 steps as loose issues are accepted, with a note. The project query now fetches `parent` on each issue.
- No change to labels: the project-level `Major change` label remains the authoritative designation and the unit that gets counted.
- **In Linear (2026-08-03):** the *SOC 2 Major Change* issue template was created at workspace level (parent issue + 8 sub-issues), and the *SOC 2 Major Change* project template was deleted so the old shape can no longer be created. The workspace now holds one project template (*SOC 2 Emergency Change (Project)*) and two issue templates (*SOC 2 Major Change*, *SOC 2 Emergency Change (Issue)*).

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
