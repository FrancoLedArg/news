---
status: superseded by ADR-0008
---

# Build on the Payload 4 beta

We start on the Payload 4 beta (with Next.js 16.4 and Node 24), even though Payload 3 is the current stable release. Nothing is built yet, so starting on v4 avoids a v3-to-v4 migration right after launch. We pin an exact beta version and only move to newer betas on purpose, re-running the tests each time. Some Payload docs and examples still describe v3. Where they differ, v4's migration guide is the reference: for example, image sizes are declared as `variants` on `sharpTransformer`, not as `imageSizes` on the collection.
