# SOC 2 Change Management

The canonical home for Vacatia's SOC 2 change-management process and the AI review agent.

## What's here
- **`change-management-plan.md`** — the process: scope, classification, Linear setup, GitHub controls, the AI review, the decision loop, roles, and how the control operates over time. Source of truth (supersedes the v16 Google Doc).
- **`scan-prompt.md`** — the prompt the review agent runs. **This is live:** Claude Tag fetches it from `main` on each run, so a merged change here changes the scan.
- **`CONTRIBUTING.md`** — how to propose changes.
- **`CHANGELOG.md`** — version history.

## How the review works
A read-only Claude agent (Claude Tag) reads `scan-prompt.md` from `main`, scans in-scope Linear work, and posts to **#soc2-change-review**. It changes nothing in Linear — engineering managers run the actual process; this reports and keeps it visible. The scan runs in two tiers:

- **Light scan — Mon, Wed, Thu, Fri (~9:00am Mountain):** an exception-based tripwire. On a quiet day it posts a single "nothing needs attention" line. It alerts only when something can't wait for the weekly pre-read: in-scope work that shipped without being triaged, an undecided candidate about to ship, a new high-confidence Major, or a break-glass event.
- **Weekly pre-read — Tue (~9:00am Mountain):** the comprehensive digest, posted the day before the Wednesday review. TL;DR and big-picture status up top; candidates, 8-step health checks, emergencies, per-team coverage, and open process questions in the thread. This is the pre-read and agenda for the meeting.

Classifications are decided at the **weekly SOC 2 Change Management Review (Wednesdays)**. After each review the meeting notes with the group's decisions are posted to #soc2-change-review, and the owning team's engineering manager (or their engineer, under the EM's oversight) makes the decision durable in Linear.

## Changing the process or the prompt
Open a PR. One approval (from someone other than the author) merges it to `main`. A merge to `main` is a deploy — it updates the documented process and, for `scan-prompt.md`, the live agent behavior. This repo is itself run under the change-management controls it describes.
