# Vacatia SOC 2 Change-Management Plan

**Status:** Canonical source of truth. Supersedes the v16 Google Doc reviewed in #proj-soc2_change_management (kept as the historical approved snapshot). Changes are made by pull request — see CONTRIBUTING.md.

**Observation windows:** The initial SOC 2 Type 2 observation window runs July 31 – October 31, 2026. After a brief remediation period, a 12-month observation window begins. **This is the permanent way of working, not a one-time exercise.** Type 2 auditors verify controls were followed *consistently* across a window, so every in-scope production change must be traceable from start to deploy.

## 1. Scope
- **In-scope systems:** AWS, VDC (Vacatia.com), DVC, Snowflake.
- **In-scope teams/areas:** DevOps, Data, Platform, Vacatia.com, DVC. (Vacatia.com is tracked as its own line; the Platform team builds it.)
- **Out of scope:** Salesforce, Berkley, Resort Sites. **Club Vacatia** is out of scope until it launches to the public; once launched, it is in scope and follows this process.
- **DVC carve-out:** DVC is pre-production; dev/staging is out of scope. Only changes promoted to DVC production are in scope. A higher major-change rate is expected and acceptable for an early-stage product.
- **Snowflake:** dbt code changes are in scope (they flow through pull requests). Non-PR Snowflake changes made directly in the UI (new users, sources) are out of scope.

## 2. Classification — Minor, Major, Emergency
Each Linear project is one change record.
- **Minor (default):** pre-approved, no sign-off. No label = Minor; apply `Minor change` to affirmatively mark a reviewed minor change.
- **Major:** new infrastructure, new PII data flows, access-control changes, new third-party integrations, or significant/architectural scope. Requires a named business approver before production deploy and follows the 8-step checklist (§4).
- **Emergency:** break-glass only. Named approver before work begins (verbal OK accepted, logged with name + time); post-mortem within 5 business days.

Classification is driven by the risk criteria above. Many changes are clearly minor or clearly major; where a change falls in a grey area, the review group of engineering and security leaders applies and documents its judgment, recording the decision and its rationale in the relevant Linear project. Because a SOC 2 Type 2 audit requires evidence that the control operated in practice, the team proactively ensures in-scope major changes are processed during the observation window — including deliberate practice runs across teams.

## 3. Linear setup
**Label group `SOC 2 Change Management`** on projects and issues:
- Project labels: `Minor change`, `Major change`, `Emergency change`
- Issue labels: `Minor change`, `Major change`, `Emergency change`. A major change is normally designated at the project level, and a standalone major change that started life as a single issue becomes a project. The issue-level `Major change` label covers the other case: a long-running project that is not itself one major change but contains a discrete major change within it — possibly several over its life. Label that issue rather than the project, and associate it with the *SOC 2 Major Change* parent issue covering that change, so it is clear which checklist applies to which change.

**Issue templates:**
- *SOC 2 Major Change* — one parent issue whose sub-issues are the 8 checklist steps (§4). Applied inside the project that carries the `Major change` label.
- *SOC 2 Emergency Change (Issue)* — one issue with Approval + Post-mortem sub-issues, for routine break-glass handled as a single issue (e.g., overnight data build failures). Lighter than a project; the issue-level `Emergency change` label keeps these enumerable. For routine cases, approval is pre-granted under standing authorization — the first responder fixes immediately and writes the post-mortem, without waiting for sign-off.

**Project template:** *SOC 2 Emergency Change (Project)* (Approval + Post-mortem).

A project template applies only at project creation, but an issue template can be applied at any time. So a project reclassified Major after it started follows exactly the same steps as one that starts out Major — apply the label, apply the issue template — with no API scripting and no second path to document.

## 4. Major-change checklist (8 steps)
The 8 steps are the sub-issues of one *SOC 2 Major Change* parent issue in the project. They stay grouped under that parent rather than sitting loose among the team's development issues: the compliance record is legible on its own, the project board stays readable, and each step can still be assigned, estimated, and blocked like any other issue.

1. **R&D / Scoping** — initial investigation; authorization follows once scope is clear
2. **Impact Assessment** — scope, affected systems, users, data flows; flag any new PII
3. **Authorization** — a named business approver confirms in writing, after R&D and before production deploy, that the change is needed and that what is being built is the right answer to that need. This is a business and product validation, not a compliance sign-off and not a restatement of the Major classification. The approver must sit **outside the engineering team making the change** — a product manager or business leader. Engineering review and another engineering team's sign-off do not satisfy this gate; self-approval fails separation of duties.
4. **Communication** — notify stakeholders of the change, timeline, any downtime
5. **Documentation** — architecture notes, spec, or updated runbook attached
6. **Pre-prod Testing** — QA sign-off in non-prod before deploy (separate tester preferred; self-testing acceptable for small teams as a documented exception, provided separate-person code review was done)
7. **Rollback Plan** — documented rollback procedure before deploy
8. **Post-Implementation Testing** — verify in production after deploy

## 5. GitHub controls
Branch protection on production branches, managed in Terraform across in-scope repos:
- Pull request required; no direct commits
- 1 approval; reviewer ≠ author; self-approval blocked
- CI status checks must pass
- Push to prod restricted to designated deployers
- Bypass limited to designated leads; every bypass logged in the GitHub audit log

**Segregation of duties:** code review by someone other than the author is a hard requirement, no exception. QA testing by a separate person is best practice; self-testing is an acceptable documented exception for small teams, provided code review was done.

## 6. The AI review (two-tier scan)
A read-only Claude agent (Claude Tag) scans in-scope Linear work and posts to #soc2-change-review. It reports factual status (counts, per-team coverage, timing), tracks designated major changes, and surfaces candidates. It makes no Linear changes — it reports and creates accountability through visibility. Its prompt lives in `scan-prompt.md` and is fetched from this repo's `main` on each run, so a reviewed merge to `main` is how the review behavior changes.

The scan runs in two tiers:
- **Light scan (Mon, Wed, Thu, Fri):** an exception-based tripwire. Most days it posts a one-line "nothing needs attention" heartbeat; it speaks up only for the few things that can't wait for the weekly pre-read — in-scope work that shipped without being triaged, an undecided candidate about to ship, a new high-confidence Major, or a break-glass event.
- **Weekly pre-read (Tue):** the comprehensive digest, posted the day before the Wednesday review meeting. It is the meeting's pre-read and agenda: TL;DR and big-picture status up top, with candidates, 8-step health checks, emergencies, per-team coverage, and open process questions in the thread.

To support the un-triaged tripwire, the scan also inspects recently-shipped (Done) in-scope items. This is not to reopen routine work — no label still means Minor by default — but to catch a change that shipped without being triaged when it plausibly warranted a Major review.

## 7. The decision loop
Classifications are decided at the **weekly SOC 2 Change Management Review** (Wednesdays), the standing forum where the review group of engineering and security leaders works through the Tuesday pre-read. Each candidate resolves to one of three outcomes:
- **Major** → the owning team's engineering manager runs the process in Linear: make sure the change is a project and apply the `Major change` project label, add the *SOC 2 Major Change* issue template to create the parent issue and its 8 sub-issues, complete the steps, and record who/when/why in the Authorization sub-issue. Where the major change is one discrete piece of a longer-running project rather than the whole project, apply the `Major change` label to that issue instead, and associate it with the *SOC 2 Major Change* parent issue covering it. The next scan moves it to "in process" and health-checks it.
- **Minor** → recorded as the group's decision. The next scan stops surfacing it.
- **No decision yet** → stays in "awaiting decision," aging, until resolved.

**Recording decisions:** after each review, the meeting notes (Gemini notes) with the group's calls are posted to #soc2-change-review — the visible, dated record the scan reads to know what's resolved. The owning team's engineering manager, or their responsible engineer under the EM's oversight, then applies the corresponding label in Linear. Note what that label is and is not: it marks the outcome, not the reasoning, and it does not record who decided. The classification decision and its rationale live only in the meeting notes today — closing that gap is an open proposal to record them as decision comments on the affected issues and projects. The Authorization sub-issue is a separate gate: it captures a business approver's validation that a change already classified Major is needed and correctly scoped, and it exists only for changes that reached that classification. The scan reports and keeps items visible until that Linear follow-through is done; it never makes Linear changes itself.

Between meetings, an engineering manager may confirm or set a scope label on an assignee's item as needed — a manual judgment call, not an automated step.

## 8. Roles
- **Cam Crow** owns the change-management framework and process.
- **Engineering managers** own execution for their teams — running the major-change process and the Linear paperwork (directly, or via their responsible engineer under their oversight).
- **The review group** (engineering + security leads) decides classifications at the weekly SOC 2 Change Management Review (Wednesdays); decisions are recorded in the meeting notes posted to #soc2-change-review and made durable in Linear by the owning team.
- **Fractional CISO (Chris Williams)** advises on audit sufficiency.

## 9. Operating the control over time
- Each in-scope team runs an end-to-end **practice** major change before 7/31 (builds the muscle and evidences the control).
- Changes known to deploy early in a window **start their process ahead of time** so the deploy lands inside the window.
- Because Linear completion timing is imperfect and work slips, teams keep several candidates in progress so the control is consistently exercised.
- This continues into the 12-month observation window — the process is permanent.

## 10. Audit evidence
| Evidence | How it's produced |
|---|---|
| Approval of production changes | Linear Authorization sub-issue + GitHub PR reviewer record |
| Separate dev/test/prod environments | GitHub branch protection |
| Rollback capability | GitHub revert PRs |
| List of all changes in a window | Linear project list |
| Who can deploy to prod | GitHub push restrictions + bypass log |
| Classification applied with documented judgment | Linear Authorization sub-issues recording the rationale for each Major change |

## Appendix — what changed since the approved v16
- Emergency changes may be handled as a single Linear issue (Approval + Post-mortem sub-issues), not only a project — proportionate for routine break-glass like overnight data build failures.
- Added explicit `Minor change` labels (project + issue); no label still defaults to Minor.
- The AI classification is implemented via Claude Tag reading this repo's prompt (rather than the originally-sketched scheduled research); the post format and decision loop are specified here.
- The scan became a two-tier cadence — an exception-based light scan (Mon/Wed/Thu/Fri) plus a comprehensive Tuesday pre-read — feeding a standing weekly review meeting (Wednesdays). The light scan also inspects recently-shipped items to catch changes that shipped without triage.
- The 8 major-change steps are now sub-issues of a single *SOC 2 Major Change* parent issue, created from an issue template, rather than 8 issues created by a project template. The review group adopted this on 2026-07-29 to keep the compliance steps grouped and the project board readable.
