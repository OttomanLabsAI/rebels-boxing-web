# Rebels Boxing Gym — website

Static site for [Rebels Boxing Gym](https://www.instagram.com/rebelsboxinggym/), Vauxhall SE11, hosted on Cloudflare.

Everything inside `public/` **is** the website — plain HTML, no build step. `public/index.html` is the live page, `public/404.html` is shown for addresses that don't exist, and `public/_headers` adds a few security headers.

## How deploys work

The repository is connected to Cloudflare through the dashboard's Git integration. On **every push to `main`**, Cloudflare clones the repo, runs `npx wrangler deploy`, and publishes `public/` — the live site updates on its own moments later at:

    https://rebels-boxing-web.<your-subdomain>.workers.dev

Pushes to other branches get a preview build instead of touching the live site. No GitHub secrets or API tokens are involved — the connection lives entirely on the Cloudflare side (**Workers & Pages → your worker → Settings → Build**).

One thing to keep in sync: the worker you connected in the dashboard should be named `rebels-boxing-web`, the same as the `name` field in `wrangler.jsonc`. If you gave it a different name when connecting, either rename it in the dashboard or change `wrangler.jsonc` to match.

## Reconnecting (if it's ever needed again)

Cloudflare dashboard → **Workers & Pages → Create → Workers → Import a repository**, pick this repo, set the production branch to `main`, and leave the deploy command as `npx wrangler deploy`. That's the whole integration.

## Custom domain

In the Cloudflare dashboard: **Workers & Pages → rebels-boxing-web → Settings → Domains & Routes → Add → Custom domain**, then enter the domain (e.g. `rebelsboxinggym.com`). If the domain is on your Cloudflare account, DNS and the certificate are handled for you.

## Working locally

- Preview the site: `npx wrangler dev`, then open <http://localhost:8787>
- Deploy by hand (normally unnecessary): `npx wrangler login`, then `npx wrangler deploy`

## Adding pages

Drop more HTML files into `public/` — `public/coaches.html` is served at `/coaches`. Anything else in there (images, CSS, favicons) is served at its own path.
