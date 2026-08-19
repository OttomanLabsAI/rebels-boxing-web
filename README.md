# Rebels Boxing Gym — website

Static site for [Rebels Boxing Gym](https://www.instagram.com/rebelsboxinggym/), Vauxhall SE11, hosted on Cloudflare.

Everything inside `public/` **is** the website — plain HTML, no build step. `public/index.html` is the live page, `public/404.html` is shown for addresses that don't exist, and `public/_headers` adds a few security headers.

## The demo tabs (pitch mode)

While this is being pitched to the gym, the site wears a black three-tab bar. The bar reads left to right as the pitch — what you have, what you could have, what it costs — but **the demo opens on the new design**, so nobody following the link lands on the old site first:

| Tab | URL | What it shows |
|---|---|---|
| **1 · The original** | `/original` | Their current site (rebelsboxinggym.com), embedded live with an open-in-new-tab fallback |
| **2 · New site** | `/` (landing) | The new site, built and written for Rebels |
| **3 · The offer** | `/offer` | The tale of the tape (old vs new), then the sell: **£500** one-off for the site, **£50** per round of changes after |

**When the gym says yes**, strip the pitch chrome before pointing their domain at it: delete `public/original.html`, `public/offer.html` and `public/_redirects`, then in `public/index.html` remove the demo-bar markup at the top of `<body>` plus the demo-bar CSS block at the bottom of the stylesheet (both are fenced with `══ DEMO BAR ══` comments — the CSS comment lists the two small offsets to restore).

## Pitching another site

`docs/pitch-prompt.md` and `docs/pitch-prompt-three-tab.md` are the reusable prompts that build this demo for any client — two tabs (site + offer) or three (their current site copied in, the new site, the offer).

`docs/skill-plan.md` plans the next step: folding that method into a skill that takes a link and returns the finished three-tab demo.

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
