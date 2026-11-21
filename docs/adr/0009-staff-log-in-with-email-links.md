# Staff log in with a link sent by email, not a password

Staff log in by typing their email address. The app emails them a login link and a 6-digit code, valid for a few minutes and usable once. Opening the link, or typing the code on the login page, logs them in. There are no passwords to guess, leak or reuse, and it works on any computer, including the shared newsroom computers. It is the same kind of login GitHub and Anthropic offer.

Payload 3 has no built-in email login, so this uses Better Auth's email login through a community Payload plugin. The setup work picks the plugin after checking that it works with our pinned Payload version.

## Considered Options

- **Payload's built-in email and password**: rejected because a password alone is the weakest point of the site.
- **Passkeys (fingerprint or face)**: rejected. Logging in on a computer with a phone needs Bluetooth on both, and the newsroom computers are shared, so a passkey saved on the computer would work for anyone using it.
- **USB security keys**: the safest option, but each one costs money and has to be handed out and replaced when lost.
- **A code by SMS or WhatsApp**: rejected because each message costs money and a phone number can be taken over (SIM swapping).

## Consequences

- Each staff member's email inbox becomes the key to the admin, so staff should protect their email account with two-step login.
- The app needs a service that sends email, and the newspaper's domain needs email records in Cloudflare so the messages don't land in spam.
- On a shared computer, the next person could find the previous one still logged in. Sessions end after a working day, and the admin has a visible log-out button.
- A staff member who loses access to their email gets it changed to a new address by another staff member, or by the developer, who also holds a staff account.
- The login screen is the most sensitive part of the site and depends on a third-party plugin, so plugin updates are reviewed like any other dependency, and security review covers every change to login.
