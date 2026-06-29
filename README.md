# SOC 2 Change Management

The canonical home for Vacatia's SOC 2 change-management process and the daily AI review agent.

## What's here
- **`change-management-plan.md`** — the process: scope, classification, Linear setup, GitHub controls, the daily review, the decision loop, roles, and how the control operates over time. Source of truth (supersedes the v16 Google Doc).
- **`scan-prompt.md`** — the prompt the daily review agent runs. **This is live:** Claude Tag fetches it from `main` each morning, so a merged change here changes the scan.
- **`CONTRIBUTING.md`** — how to propose changes.
- **`CHANGELOG.md`** — version history.

## How the daily review works
Each weekday at 9:00am Mountain, a read-only Claude agent (Claude Tag) reads `scan-prompt.md` from `main`, scans in-scope Linear work, and posts to **#soc2-change-review**: a TL;DR, where we stand (counts, per-team coverage, timing), in-process major changes with a health check, and candidates awaiting a decision. It changes nothing in Linear — engineering managers run the actual process; this reports and keeps it visible.

## Changing the process or the prompt
Open a PR. One approval (from someone other than the author) merges it to `main`. A merge to `main` is a deploy — it updates the documented process and, for `scan-prompt.md`, the live agent behavior. This repo is itself run under the change-management controls it describes.
