# Base44 Dev Environment

## Project Overview
This is a VuePress 1.x documentation site for RikkaApps (https://github.com/RikkaApps/websites).
It contains multiple VuePress sub-sites: `www` (main landing page), `appops`, `shizuku`, and `storage_redirect`.
There is also a `webhooks/` directory with a Node.js Cloudflare cache-purge webhook service — not needed for the dev preview.

## Running the Dev Server
- `docker compose -f docker-compose.base44.yml up -d` starts the `www` VuePress dev server on port 3000.
- Uses `node:16` base image (VuePress 1.x / webpack 4 requires Node 16 or lower; Node 17+ breaks webpack 4's OpenSSL usage).
- No lock file exists; `npm install` runs on container startup (dependencies stored in a named volume `www_node_modules`).
- Dev command: `npx vuepress dev www --host 0.0.0.0 --port 3000`
- Live reload is enabled — edits to `www/` markdown and theme files appear in the preview automatically.

## No External Credentials Needed
The dev preview requires no secrets. The `webhooks/` service needs Cloudflare credentials (`cf_email`, `cf_key`, `cf_zone_id`) and a `config.json`, but it is not part of the dev preview.

## Other Sub-Sites
To preview a different sub-site, change the dev command in the compose file:
- `appops`: `npx vuepress dev appops --host 0.0.0.0 --port 3000`
- `shizuku`: `npx vuepress dev shizuku --host 0.0.0.0 --port 3000`
- `storage_redirect`: `npx vuepress dev storage_redirect --host 0.0.0.0 --port 3000`

## Verification
- `curl -s -o /dev/null -w "%{http_code}" http://localhost:3000/` should return `200`.
- The page is a VuePress SPA — the initial HTML loads `/assets/js/app.js` which renders the home page client-side.
