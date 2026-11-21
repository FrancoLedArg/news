# Build on Payload 3 stable, not the Payload 4 beta

Supersedes ADR 0002. We build on the latest stable Payload 3 release (3.94.1 when this was written), with Next.js 16.4 and Node 24, which that release supports. A paying client's site shouldn't run on beta software, and a v3-to-v4 upgrade after launch is a known, planned job, while beta bugs are not. We pin the exact version and update only on purpose, re-running the tests each time.

## Consequences

- Use Payload 3 docs and APIs. For example, image sizes are declared as `imageSizes` on the upload collection (ADR 0005).
- Moving to Payload 4 once it is stable becomes its own planned change, following its migration guide.
