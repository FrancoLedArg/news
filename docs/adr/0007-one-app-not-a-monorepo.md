# One app with two areas, not a monorepo

The public site and the staff admin area live in one Next.js app, in two route groups: `(frontend)` for readers and `(payload)` for staff. They build, deploy and run together on the one VPS (ADR 0001). The public pages read content through Payload's Local API in the same process, and Payload's save hooks refresh cached pages directly (ADR 0003).

## Considered Options

- **A monorepo with separate apps for the public site and the admin**: rejected. On one 2 vCPU / 8 GB server, two apps share the same CPU anyway, so the isolation buys little. Meanwhile they double the builds, deploys and containers, the public site has to fetch over HTTP instead of the Local API, and cache refresh needs a cross-app webhook that can fail silently.

## Consequences

- Revisit if the publication grows into several sites sharing one admin, the public site moves to a different host, or a mobile app needs the same content. Keeping the two route groups apart means a later split is contained, not a rewrite.
