# Contributing

Changes to this repo follow the same change-management controls it describes — the process is, fittingly, change-managed.

## How to make a change
1. Branch from `main`.
2. Open a pull request describing what's changing and why.
3. Get **1 approval from someone other than the author** (the #soc2-change-review group has write access and can review).
4. Merge to `main`.

Only Cam Crow (`cjcrow`) can bypass the review requirement, for genuine emergencies.

## Why this matters
`scan-prompt.md` is **live** — Claude Tag fetches it from `main` each morning, so merging a change to it changes the next day's scan. A reviewed merge is the deploy mechanism; treat `main` as production.

When you change `scan-prompt.md`, add a `CHANGELOG.md` entry so any daily post can be traced (by date) back to the version of the prompt that produced it.
