# Next.js + Payload + PostgreSQL, self-hosted on a DonWeb VPS behind Cloudflare

The site is one Next.js app with Payload CMS inside it. Payload provides the staff admin area, staff login, image uploads and the formatted-text editor, and we build the public site ourselves. Data lives in PostgreSQL. Everything runs with Docker Compose (the app, PostgreSQL and Caddy) on a single DonWeb VPS with 2 vCPU and 8 GB of RAM. Cloudflare's free plan sits in front, managing DNS and caching images and static files. Uploaded images are stored on the VPS disk.

We host it ourselves instead of on Vercel or a managed cloud because the client's main condition is low running cost. The client is fine paying us a maintenance fee, so the work a managed host would otherwise do (backups, updates, server upkeep) is ours. Images stay on the VPS disk for the same reason, and Cloudflare keeps that cheap by serving cached copies.

## Considered Options

- **WordPress**: the usual choice for local news portals. Rejected because the look depends on themes and plugins, and the goal is a site that looks clearly better than the local competition.
- **Fully custom Next.js without a CMS**: rejected because we'd have to build the admin area, login, uploads and the editor ourselves before any news could be published.
- **Caddy's automatic certificates behind Cloudflare**: rejected because they need a custom Caddy build with Cloudflare's DNS plugin and an API token kept on the server. A Cloudflare Origin Certificate loaded from a file is simpler.

## Consequences

- The database and images exist only on one server, so backups are our responsibility and must be in place before launch. Each night, restic sends a `pg_dump` and the images folder to Cloudflare R2, keeping 7 daily, 4 weekly and 6 monthly copies, with a restore tested before launch.
- The app is built on GitHub Actions, not on the VPS. Each push to `main` runs the tests, builds an x86 image and pushes it to GitHub's container registry. The workflow then connects to the VPS over SSH and runs `docker compose pull && docker compose up -d`. A restart causes a few seconds of downtime, which is accepted for now.
- The site is reachable only through Cloudflare. Caddy uses a Cloudflare Origin Certificate with SSL mode "Full (strict)", and the firewall accepts web traffic only from Cloudflare's IP ranges.
- Staff login is Payload's built-in auth (sessions, HTTP-only cookies with `secure` set in production, lockout after failed attempts). There is no separate auth library, because readers never log in.
- Errors go to Sentry's free plan. An external uptime check watches the Home page and the nightly backup, and Docker logs are rotated.
- The server is set up by a script kept in the repo: SSH keys only, a firewall, `unattended-upgrades`, fail2ban and a deploy user that can only run Docker Compose.
- Everything except Cloudflare and the SSH deploy can be tested on a laptop with the same Docker Compose setup, so the VPS is only rented close to launch.
