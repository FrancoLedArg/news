# The factory

news is built by a software factory. The user brings ideas through the **front desk** and tests the finished app. **Runs** write, check and review all the code. Each run is one task on a fresh git worktree, started by `factory/run` on the user's Mac. A run's only memory of earlier runs is GitHub: tickets, PRs, comments and commits.

## Stations

| Station | Started by | Agent | Ends with |
|---|---|---|---|
| Dispatch | `factory/run dispatch` | none (script) | A Build run for every ticket labelled `ready-for-agent` with no open blockers, up to `FACTORY_MAX_BUILDS` (default 2) |
| Build | Dispatch, or `factory/run build <issue>` | `build-lead` → `<area>-builder` | A draft PR that closes the ticket, labelled `factory:review` |
| Checks | Every Build, Rework and Review run | none (`factory/checks.sh`) | Green, or a fail comment and `factory:rework` |
| Review | `factory:review` | `review-lead` → `spec-reviewer`, `standards-reviewer`, `test-reviewer`, `security-reviewer` | Pass: `factory:qa`. Fail: one findings comment and `factory:rework` |
| Rework | `factory:rework` | `build-lead` → `<area>-builder` | Blocking findings fixed and pushed, back to `factory:review` |
| QA | `factory:qa` | `qa` | Pass: PR marked ready, labelled `ready-for-acceptance`. Fail: `factory:rework` |
| Acceptance | `ready-for-acceptance` | the user | Merge, or a PR comment that becomes a Rework |
| Retro | `factory/run retro <pr>` after a merge | `retro` | A PR with changes to the factory itself, labelled `ready-for-acceptance` |

The `FACTORY_MAX_FAILS`th (default 3) failed review or check on one PR labels it `factory:stuck` and the factory stops touching it until the user steps in.

## Labels

- Tickets: `ready-for-agent` → `factory:building`. A spec issue carries `spec` and is never built.
- PRs: `factory:review` → `factory:qa` → `ready-for-acceptance`, with `factory:rework` and `factory:stuck` on failure.
- `area:frontend`, `area:backend`, `area:platform` on a ticket pick the builders. One ticket can carry several.

## Commands

Run from the repo root on the user's Mac:

```
factory/run status              # what is where
factory/run dispatch            # start builds for ready tickets
factory/run build <issue>       # one Build run
factory/run review <pr>         # one Review run
factory/run rework <pr>         # one Rework run
factory/run qa <pr>             # one QA run
factory/run retro <pr>          # one Retro run on a merged PR
factory/run tick                # one pass over every queue
factory/run watch [minutes]     # tick forever
```

Worktrees, results and run logs (`stream-json`) live in `~/.factory/news/`.

## Run contract

Every run's prompt names a **result file**. The agent's last act is writing that JSON file. `factory/run` reads it and does every GitHub write: push, PR, labels, comments. Runs only read GitHub; `factory/claude-settings.json` blocks pushes, merges and GitHub edits from inside a run.

| Agent | Result file |
|---|---|
| `build-lead` | `{"status": "done" \| "blocked", "summary": "markdown", "reason": "markdown, when blocked"}` |
| `review-lead` | `{"verdict": "pass" \| "fail", "report": "markdown"}` |
| `qa` | `{"verdict": "pass" \| "fail", "report": "markdown", "how_to_test": "markdown"}` |
| `retro` | `{"status": "done" \| "nothing", "summary": "markdown"}` |

Factory comments carry the marker `<!-- factory:fail -->` on failure, so the next Rework finds its findings and the fail count stays countable.

## Crew

| Agent | Status |
|---|---|
| `front-desk`, `build-lead`, `review-lead`, the four reviewers, `retro` | Ready |
| `frontend-builder`, `backend-builder`, `platform-builder`, `qa` | Written once the app is scaffolded (stack: ADRs 0001–0005) |

## Running the app

Filled in when the app is scaffolded: the commands to install, start and test the app, and the URL it serves on. `qa` and the builders read this section.
