# Vacatia SOC 2 Change-Management Plan

**Status:** Canonical source of truth. Supersedes the v16 Google Doc reviewed in #proj-soc2_change_management (kept as the historical approved snapshot). Changes are made by pull request — see CONTRIBUTING.md.

**Observation windows:** The initial SOC 2 Type 2 observation window runs July 31 – October 31, 2026. After a brief remediation period, a 12-month observation window begins. **This is the permanent way of working, not a one-time exercise.** Type 2 auditors verify controls were followed *consistently* across a window, so every in-scope production change must be traceable from start to deploy.

## 1. Scope
- **In-scope systems:** AWS, VDC (Vacatia.com), DVC, Snowflake.
- **In-scope teams/areas:** DevOps, Data, Platform, Vacatia.com, DVC. (Vacatia.com is tracked as its own line; the Platform team builds it.)
- **Out of scope:** Salesforce, Berkley, Resort Sites, Club Vacatia.
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
- Issue labels: `Minor change`, `Emergency change` (Major is project-only — convert a major issue into a project)

**Project templates:** *SOC 2 Major Change* (auto-creates the 8 checklist issues); *SOC 2 Emergency Change (Project)* (Approval + Post-mortem).

**Issue template:** *SOC 2 Emergency Change (Issue)* — one issue with Approval + Post-mortem sub-issues, for routine break-glass handled as a single issue (e.g., overnight data build failures). Lighter than a project; the issue-level `Emergency change` label keeps these enumerable. For routine cases, approval is pre-granted under standing authorization — the first responder fixes immediately and writes the post-mortem, without waiting for sign-off.

Templates apply only at project creation. When an existing project is reclassified Major, the required issues are created in it via the Linear API.

## 4. Major-change checklist (8 steps)
1. **R&D / scoping** — initial investigation; authorization follows once scope is clear
2. **Impact assessment** — scope, affected systems, users, data flows; flag any new PII
3. **Authorization** — named business approver confirms in writing after R&D, before production deploy
4. **Communication** — notify stakeholders of the change, timeline, any downtime
5. **Documentation** — architecture notes, spec, or updated runbook attached
6. **Pre-prod testing** — QA sign-off in non-prod before deploy (separate tester preferred; self-testing acceptable for small teams as a documented exception, provided separate-person code review was done)
7. **Rollback plan** — documented rollback procedure before deploy
8. **Post-implementation testing** — verify in production after deploy

## 5. GitHub controls
Branch protection on production branches, managed in Terraform across in-scope repos:
- Pull request required; no direct commits
- 1 approval; reviewer ≠ author; self-approval blocked
- CI status checks must pass
- Push to prod restricted to designated deployers
- Bypass limited to designated leads; every bypass logged in the GitHub audit log

**Segregation of duties:** code review by someone other than the author is a hard requirement, no exception. QA testing by a separate person is best practice; self-testing is an acceptable documented exception for small teams, provided code review was done.

## 6. The daily AI review
A read-only Claude agent (Claude Tag) scans in-scope Linear work each weekday at 9:00am Mountain and posts to #soc2-change-review. It reports factual status (counts, per-team coverage, timing), tracks designated major changes, and surfaces candidates. It makes no Linear changes — it reports and creates accountability through visibility. Its prompt lives in `scan-prompt.md` and is fetched from this repo's `main` each morning, so a reviewed merge to `main` is how the review behavior changes. The post leads with a TL;DR, then has three parts: big-picture status, in-process majors with an 8-step health check, and candidates awaiting decision (aged so stalled ones surface).

## 7. The decision loop
The review group discusses each candidate in-channel; one of three outcomes:
- **Major** → the owning team's engineering manager runs the process in Linear (convert to a project with the Major template, complete the 8 steps, record who/when/why in the Authorization issue). The next scan moves it to "in process" and health-checks it.
- **Minor** → recorded (in-channel and/or via `Minor change`). The next scan stops surfacing it.
- **No decision yet** → stays in "awaiting decision," aging, until resolved.

The review channel reports; it does not make Linear changes. The paperwork is done separately by the owning team, Claude-assisted if desired. The durable decision record lives in the Linear Authorization issue, stated as risk-based rationale.

## 8. Roles
- **Cam Crow** owns the change-management framework and process.
- **Engineering managers** own execution for their teams — running the major-change process and Linear paperwork.
- **The review group** (engineering + security leads in #soc2-change-review) decides classifications.
- **Fractional CISO (Chris Williams)** advises on audit sufficiency.

## 9. Operating the control over time
- Each in-scope team runs an end-to-end **practice** major change before 7/31 (builds the muscle and evidences the control).
- Changes known to deploy early in a window **start their process ahead of time** so the deploy lands inside the window.
- Because Linear completion timing is imperfect and work slips, teams keep several candidates in progress so the control is consistently exercised.
- This continues into the 12-month observation window — the process is permanent.

## 10. Audit evidence
| Evidence | How it's produced |
|---|---|
| Approval of production changes | Linear Authorization issue + GitHub PR reviewer record |
| Separate dev/test/prod environments | GitHub branch protection |
| Rollback capability | GitHub revert PRs |
| List of all changes in a window | Linear project list |
| Who can deploy to prod | GitHub push restrictions + bypass log |
| Classification applied with documented judgment | Linear Authorization issues recording the rationale for each Major change |

## Appendix — what changed since the approved v16
- Emergency changes may be handled as a single Linear issue (Approval + Post-mortem sub-issues), not only a project — proportionate for routine break-glass like overnight data build failures.
- Added explicit `Minor change` labels (project + issue); no label still defaults to Minor.
- The AI classification is implemented via Claude Tag reading this repo's prompt (rather than the originally-sketched scheduled research); the daily-post format and decision loop are specified here.
