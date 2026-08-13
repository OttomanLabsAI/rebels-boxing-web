# Rebels Boxing Gym — website

Static site for [Rebels Boxing Gym](https://www.instagram.com/rebelsboxinggym/), Vauxhall SE11, hosted on Cloudflare.

Everything inside `public/` **is** the website — plain HTML, no build step. `public/index.html` is the live page, `public/404.html` is shown for addresses that don't exist, and `public/_headers` adds a few security headers.

## How deploys work

**Every push to `main` publishes the site.** The GitHub Action in `.github/workflows/deploy.yml` uploads `public/` to Cloudflare, which serves it as a Worker named `rebels-boxing-web` at:

    https://rebels-boxing-web.<your-subdomain>.workers.dev

## One-time setup

The deploy needs two secrets so GitHub is allowed to talk to your Cloudflare account:

1. **Account ID** — log in at [dash.cloudflare.com](https://dash.cloudflare.com) and open **Workers & Pages**. Your Account ID is shown in the right-hand sidebar.
2. **API token** — go to [dash.cloudflare.com/profile/api-tokens](https://dash.cloudflare.com/profile/api-tokens) → **Create Token** → use the **Edit Cloudflare Workers** template → **Create Token**, then copy it (it's shown once).
3. **Add both to GitHub** — in this repo: **Settings → Secrets and variables → Actions → New repository secret**:
   - `CLOUDFLARE_ACCOUNT_ID` — the ID from step 1
   - `CLOUDFLARE_API_TOKEN` — the token from step 2
4. Push to `main` — or run **Deploy to Cloudflare** manually from the **Actions** tab. The first deploy creates the worker on your account; after that every push to `main` updates the live site automatically.

## Custom domain

In the Cloudflare dashboard: **Workers & Pages → rebels-boxing-web → Settings → Domains & Routes → Add → Custom domain**, then enter the domain (e.g. `rebelsboxinggym.com`). If the domain is on your Cloudflare account, DNS and the certificate are handled for you.

## Working locally

- Preview the site: `npx wrangler dev`, then open <http://localhost:8787>
- Deploy by hand (normally unnecessary): `npx wrangler login`, then `npx wrangler deploy`

## Adding pages

Drop more HTML files into `public/` — `public/coaches.html` is served at `/coaches`. Anything else in there (images, CSS, favicons) is served at its own path.
