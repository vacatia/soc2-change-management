# Changelog

Tracks changes to the process and the daily-scan prompt. The prompt itself carries no version stamp — git history + the date on each daily post are the source of truth; this log is the human-readable summary.

## 2026-06-29 — Initial release
Establishes this repo as the canonical home for the change-management process and the daily review prompt.
- **`scan-prompt.md`:** read-only, state-aware daily scan. Posts a TL;DR plus three sections — big-picture status, in-process major changes with an 8-step health check, and candidates awaiting decision (with queue age). Forward-looking (excludes Done/Canceled). Written for a non-expert, cross-team audience. No targets in the output; the agent reports facts only. No @-mentions (humans pull people in).
- **`change-management-plan.md`:** migrated and reorganized from the v16 Google Doc, incorporating the emergency-change issue template, the `Minor change` labels, the permanent multi-window framing, and the AI operating model.

### Prompt lineage before this repo (reference)
- 6/26: first scheduled scan — flag-only, dedupe via channel history.
- 6/29: richer per-flag format (project, team, status, label, people, labeled reason); "write for a non-expert, cross-team audience" principle; then state-aware three-section post with a TL;DR, factual (no-target) reporting, and the read-only model — the version captured here.
