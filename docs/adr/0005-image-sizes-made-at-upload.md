# Image sizes are made by Payload at upload, not by Next.js's image optimizer

When staff upload an image, Payload uses sharp to create a fixed set of WebP sizes: card, large cover and a 1200×630 size for link previews. Pages serve those files directly, and Cloudflare caches them. Next.js's own image optimizer is turned off. That way the CPU cost is paid once per upload, instead of unpredictably on the VPS each time a new size is requested.
