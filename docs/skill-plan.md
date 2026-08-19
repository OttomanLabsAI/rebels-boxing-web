# Plan — turn this workflow into a skill

The goal: type something like *"use site-pitch on https://theirgym.co.uk"* and get
back the same three-tab demo this repo holds — their current site, a new one built
for them, and the offer — live on a Cloudflare URL, ready to send.

---

## 1. Where the skill sits

Three skills already installed do part of this job. The new skill must **call
them, not copy them**:

| Already exists | Does | New skill's relationship |
|---|---|---|
| `cloudflare-static-site` | HTML page → repo, `public/`, 404, headers, dry-run deploy, v1.0 to main | Hand it the finished pages. It packages and ships. |
| `git-identity` | Commit author is Fid / fid_kk@proton.me | Called before the first commit. |
| `release-workflow` | Every push to main is a release; vMAJOR.MINOR; tag text in the reply | Called at push time. |

The boundary matters. `cloudflare-static-site` opens with *"the page is the
client's, not yours — keep content and design exactly as provided."* That rule is
written for a human handing over a page. The new skill is the **author** of the
pages; it finishes them, then hands them over. State this in the SKILL.md so the
two never fight: **site-pitch writes the site, cloudflare-static-site ships it.**

So the new skill owns exactly the part nothing covers yet: *link in → three
finished pages out.*

---

## 2. Skill layout

```
~/.claude/skills/site-pitch/
  SKILL.md                     the procedure — short, points at the rest
  references/
    capture.md                 how to copy their site safely and honestly
    brief.md                   what to extract from the copy, and what never to invent
    house-style.md             the design system + the typography laws
    offer.md                   the offer page structure, pricing, tone
  scripts/
    capture-site.js            headless fetch, localise assets, strip trackers
    verify-layout.js           320→1920 line-box + overflow sweep
    measure-font.js            real font metrics via npm @fontsource
  assets/
    demo-bar.html              the fenced three-tab bar
    offer-template.html        offer page skeleton, tokens as placeholders
    original-frame.html        iframe fallback page when a copy is impossible
```

**Name:** `site-pitch`. The description has to fire on how you actually talk —
*"pitch this business a site"*, *"build a demo for this link"*, *"make them a new
website"* — and on a bare link with the skill named. It should not fire on
ordinary edits to a site that already exists.

---

## 3. What happens on a run

Eight stages. Stages 2 and 6 are gates: fail there and it stops rather than
bluffing.

**0 · Preflight.** Take the link. Take price (default £500), changes fee (default
£50), and how they reply. Check the URL resolves. Set git identity.

**1 · Copy their site → tab 1.** `capture-site.js`: load in headless Chromium,
capture the rendered DOM, pull stylesheets/images/fonts local, rewrite the
references, strip analytics, tag managers, chat widgets and cookie banners,
neuter forms, add `noindex`, and put a dated snapshot strip on top. If the site
can't be captured cleanly, fall back to `original-frame.html` and **say so** —
never a screenshot pretending to be a page.

**2 · Read what you copied → the brief.** *(gate)* From the captured site pull
two things: brand (colours, typefaces, logo, tone) and facts (what they sell,
prices, hours, address, phone, socials). Write them to `brief.json` in the repo.
Everything on the new site must trace to a line in that brief. Anything missing
becomes a question for the client, not a guess. **This is the stage that decides
whether the pitch is honest** — the tale of the tape in stage 4 can only score
what was actually seen here.

**3 · Build the new site → tab 2, the landing page.** House skeleton dressed in
their brand tokens, content strictly from the brief. `house-style.md` carries the
rules this build paid for: single-word headlines capped against their own column
so they never split mid-word, headings never capped at a reading measure, card
grids on `minmax(min(300px,100%),1fr)`.

**4 · Build the offer → tab 3.** Harvest the new site's own tokens and components
so tabs 2 and 3 look the same age. Tale of the tape, two price cards, why it's
priced that way, closing band. Every left-hand row must cite something observed in
stage 2.

**5 · Wire the three tabs.** Bar at the top of all three pages. `/` is the new
site, `/original` the copy, `/offer` the offer. 301s for any path already shared.

**6 · Verify.** *(gate)* Every route and redirect. `verify-layout.js` across
320→1920 with the real fonts loaded, measuring line boxes — single-word headlines
exactly one line, nothing overflowing at any width. The copied site renders with
its own styling and no asset still points at their live host. No tracker survived.

**7 · Ship and report.** Hand to `cloudflare-static-site`, then `release-workflow`
for the tag text. Report the link, what was verified, what couldn't be, and the
list of questions for the client.

---

## 4. What gets lifted from this repo

Most of the skill already exists as working code here — it's a move, not a build:

| From | Becomes |
|---|---|
| `docs/pitch-prompt-three-tab.md` | the spine of `SKILL.md` + `references/offer.md` |
| the demo bar markup and CSS | `assets/demo-bar.html` |
| `public/offer.html` | `assets/offer-template.html`, tokens as placeholders |
| `public/original.html` | `assets/original-frame.html` (the fallback) |
| the verification harness used on this site | `scripts/verify-layout.js` |
| the npm-fontsource measuring trick | `scripts/measure-font.js` |
| the typography fixes and their comments | `references/house-style.md` |

Genuinely new: `capture-site.js` and `references/brief.md`.

---

## 5. Build order

1. **Scaffold** with `skill-creator`; move the seven items above across. Nothing
   new written yet.
2. **Write `capture-site.js`** and test it on three sites of different builds — a
   plain static one, a WordPress one, a JavaScript-heavy one. This is the riskiest
   piece and the one worth building first.
3. **Write `brief.md` and `house-style.md`** — the judgement, not the mechanics.
4. **Dry run** end to end on a real link into a scratch repo, stopping before
   deploy. Fix what breaks.
5. **Full run** with deploy, on a business you'd actually pitch.
6. **Tune the description** so it fires when you want it and stays quiet
   otherwise; `skill-creator` has eval tooling for this if it's worth the time.

Steps 1 and 2 are the bulk. A working first version is realistically one focused
session plus one round of fixing what the first real link exposes.

---

## 6. Defaults I've assumed

- **£500 / £50** carried as defaults, overridable per run.
- **One repo per client**, named `<client>-web`.
- **The new site is one page**, like this one.
- **Their old site is the only source** for the brief.

## 7. Known limits to design around

- **The first deploy is manual.** Connecting a repo to Cloudflare happens in their
  dashboard — as it did here. The skill can create and push the repo, but the
  first connection is a human step. Say so in the report rather than implying the
  site is live.
- **Capture fails sometimes** — logins, bot protection, network policy. The
  fallback and the honesty clause exist for that.
- **A sandbox may block webfonts**, which makes screenshots lie. That's why the
  verifier fetches real fonts from npm and measures line boxes instead.
- **Never mirror a site that isn't the client's**, and never let a copy reach a
  search index.
