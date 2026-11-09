# Cached pages refreshed on save, with ads picked in the browser

Public pages (Home, Category, Post) are cached by Next.js and refreshed on demand. Payload save and delete hooks on posts, categories and ads tell Next.js which pages to rebuild. This keeps a small VPS fast when a post spreads on WhatsApp, because most visits never touch PostgreSQL.

Because the cached HTML is the same for every reader, the server can't pick the random ad. Instead, each page carries all the active ads for each ad slot, and a small browser script picks one at random on each page load. Readers see the same result as a server-side pick.

## Consequences

- A save hook that fails leaves stale pages, so hook errors must reach Sentry.
- Cloudflare caches only images and static files, never HTML, so we never have to clear Cloudflare's cache when content changes.
