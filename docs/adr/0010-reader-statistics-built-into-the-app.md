# Reader statistics are built into the app

The site counts reader visits itself and shows the numbers on a statistics page inside the staff admin. Because pages are cached and identical for everyone (ADR 0003), a small browser script reports each visit to the app. The app keeps running totals per post, per day, per source (Facebook, WhatsApp, Google, direct…) and per device (phone or computer), not one row per visit, so a viral post adds little to the database. It stores no IP addresses and sets no cookies, so the site needs no cookie banner.

The owner sees the statistics as a staff member, behind the same email login (ADR 0009). The first version covers what a newsroom owner usually asks for: visits over time, most-read posts, where readers come from and phone versus computer. Anything more arrives as new tickets.

## Considered Options

- **Umami, self-hosted**: a ready dashboard with more reports, but it has its own password login (fingerprint login would need Cloudflare Access and Google on top), runs as one more program on the server, and can't easily feed site features like a "most read" box.
- **PostHog**: rejected because the data lives with them and a news site could outgrow the free tier.

## Consequences

- We build and maintain every report, and each new one is a ticket.
- Bots are filtered by simple rules and unique visitors are not counted, so the numbers are good for comparing posts and days, not exact head counts.
- The owner holds a normal staff account, so he can also write and edit posts. A view-only account type would need new work if that ever matters.
