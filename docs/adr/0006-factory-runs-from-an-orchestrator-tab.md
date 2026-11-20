# The factory runs on demand from an orchestrator tab on the user's Mac

The factory only moves while an **orchestrator** is running. It's a Claude agent the user starts on purpose (`claude --agent orchestrator`) in a herdr tab on their Mac. When it starts, it reads GitHub and moves every piece of work forward through `factory/run`: build, checks, review, rework, QA. When nothing is left to move, it says so and stops. Closing the tab stops the factory too. Nothing runs in the cloud or in the background without the user knowing.

The front desk no longer starts runs. When the user says go, it adds `ready-for-agent` to the approved tickets and tells the user to start the orchestrator.

## How the user tests a change

When a change passes QA, the orchestrator starts that version of the app on the Mac, on its own port and with its own database, and gives the user the link and the test steps. Then it waits: herdr marks the tab as blocked, so the user sees it needs them. The user answers in that tab:

- **"Looks good"**: the orchestrator merges the change, stops the test copy, and runs the retro on the merged change. Any factory improvement the retro suggests arrives as its own change for the user to approve.
- **"Problem: …"**: the orchestrator helps the user put the problem into words, posts it as a comment on the change, and sends the change back for rework.

While the user tests, the orchestrator keeps building and checking the next ticket.

## One build at a time

Only one ticket is built at a time (`FACTORY_MAX_BUILDS=1`). This avoids builds fighting over the database and ports, and keeps cost steady. Before review, QA or testing, the orchestrator brings the change up to date with the latest approved code. If that fails because both changed the same lines, the change goes back to rework.

## Which model each worker uses

The strongest model (Opus) goes only where a mistake costs the most: the build lead's plan, the security reviewer, and the retro. Everyone else uses Sonnet: the builders, the spec, standards and test reviewers, QA, and the orchestrator. This holds on any Claude plan. There is no separate cheap mode.

## The starting app is built in one setup session

The factory's builders, QA agent and checks depend on the app's tools, so they can't exist before the app does. One hands-on setup session, outside the factory, builds the empty starting app (ADRs 0001–0005) and writes, all to match this ADR:

- the builders, the QA agent and the orchestrator,
- the real checks,
- `CODING_STANDARDS.md`,
- the "Running the app" notes in `docs/agents/factory.md`.

The user tests that starting app once, and every change after it goes through the factory.

## Considered Options

- **A background service that checks GitHub every few minutes**: rejected because the user wants runs only when they ask for them.
- **The build lead builds the starting app itself in a special first ticket**: rejected because that run would skip the builders and the usual checks, and its mistakes would carry into every later ticket.
- **An online preview for each change**: rejected because it needs a server and costs money, while a test copy on the Mac is free.
- **Two builds at once**: rejected for now because it needs a separate database per build and more conflict handling. Revisit if the line feels slow.

## Consequences

- Passwords and secret keys the app needs (the database password, Payload's secret) live in a local file outside the project folder. Each run copies them in, and they never reach GitHub.
- A change marked `factory:stuck` stops the line for that ticket only. The orchestrator tells the user, and the front desk helps decide what to do: reword the ticket, split it, or give the factory a hint.
- "Looks good" is final. Merging is a deploy (ADR 0001), so after launch an approved change reaches readers within minutes. There is no publish button and no test copy on the server.
- If herdr doesn't show the tab as waiting while the orchestrator is busy with the next ticket, the orchestrator also sends a Mac notification when a change is ready to test.
