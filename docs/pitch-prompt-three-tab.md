# Prompt — three-tab pitch, with the client's current site copied in

The three-tab version of `pitch-prompt.md`. Tab 1 is the client's existing
website, captured as a local copy rather than framed — an iframe is at the
mercy of their host, and plenty of hosts refuse to be embedded, leaving a
blank rectangle exactly where the comparison should be.

The demo opens on the new design. Tab 1 is there to be clicked back to, not
to be the first thing a client sees.

---

```
Build a three-tab sales pitch around this website. Tab 1 is what the client
has today, tab 2 is what I have built for them, tab 3 asks for the money.

  New site:         <path to the finished site, e.g. public/index.html>
  Their site now:   <https://existing-site.com>
  Client:           <name of the business>
  Price:            £<500> one-off for the site
  Changes:          £<50> per round of changes afterwards
  How they reply:   <reply to this message / email hello@… / call …>

ROUTING — the demo launches on the new design. The new site is the landing
page at /, the copy of their current site is at /original, the offer is at
/offer. The bar still reads 1 · The original, 2 · New site, 3 · The offer, so
the story runs left to right, but nobody arriving at the link lands on the old
site first. If any of these pages previously lived at another path, leave a
301 behind so links already sent out keep working.

TABS — an identical bar at the very top of all three pages, above everything,
so it reads as chrome around the demo rather than part of any of the sites.
Each tab is a plain numbered link, the current one filled in the new site's
accent colour and carrying aria-current="page". No JavaScript.

RULE 1 — COPY THEIR SITE, DO NOT FRAME IT.
Capture <their site> as a self-contained static copy served from /original:
  - Fetch the page. If it needs JavaScript to render, load it in a headless
    browser and capture the rendered DOM instead of the raw source.
  - Pull down the assets it needs — stylesheets, images, fonts — into a folder
    beside it and rewrite the references to point at the local copies, so the
    page still stands up if their host goes down or blocks hotlinking.
  - Strip anything that reaches out or collects: analytics, tag managers,
    chat widgets, tracking pixels, cookie banners. Neuter forms so a stray
    click cannot post to their live site. Leave the design untouched.
  - Add <meta name="robots" content="noindex"> — a second copy of a client's
    site must never turn up in a search result.
  - Put a strip above it naming the domain, saying it is a snapshot, giving
    the date it was captured, and linking to the real site in a new tab.
Only ever mirror the site belonging to the client you are pitching to, and
only to show it back to them. If the fetch fails or the site cannot be
captured cleanly, say so plainly and fall back to an iframe with an
open-in-a-new-tab button — do not fake it with a screenshot pretending to be
a page, and do not claim the copy works when you have not loaded it.

RULE 2 — THE OFFER PAGE MUST LOOK LIKE THE NEW SITE.
Do not invent a design, and do not borrow anything from the old one. Read the
new site's stylesheet and harvest, verbatim: every CSS custom property; the
display and body font families, weights, and heading treatment; the component
vocabulary — buttons, cards, chips, list rows, accent bars, border weights,
shadow offsets; the page max-width and padding scale. Rebuild the offer page
from those parts, so tabs 2 and 3 are indistinguishable in age and authorship.
Reuse the new site's own font loading — no second typeface, no external
dependency.

RULE 3 — WHAT GOES ON THE OFFER PAGE, in this order:
  1. A hero: short label, one big headline in the display face, and a sentence
     saying the whole thing is theirs on their own domain.
  2. Tale of the tape — a two-column table scoring the two sites against each
     other. What a visitor cannot do on the site under tab 1 goes left, what
     the new one does goes right. Six rows, concrete and specific: prices
     visible without scrolling, what to expect on a first visit, works on a
     phone, answers the questions people are nervous to ask. Because you have
     now read their real site, every row on the left must be something you
     actually saw there. If a row is not true, cut it — an overstated flaw is
     the fastest way to lose the room.
  3. Two price cards built from the new site's own card component: the one-off
     price for the site, and the per-round price for changes afterwards. Say
     plainly that the second is per round of changes and not a monthly fee,
     and that changing nothing costs nothing.
  4. One short paragraph on why it is priced that way, tied to how the client
     sells their own thing where you can.
  5. A closing band: big headline one side, how to reply the other.

RULE 4 — TONE. Plain and confident. Short sentences. No agency words —
nothing "bespoke", "seamless", "solution", "leverage" or "journey". Never
sneer at the old site; let the comparison do that work quietly.

RULE 5 — TYPOGRAPHY THAT HOLDS.
  - A long single-word headline must never split mid-word. Viewport-based
    sizing keeps growing after the column it sits in stops at the page's max
    width, which is exactly when it snaps. Measure the word in the real
    display font, then also cap the size against its own column (container
    query units) so it can never outgrow it.
  - Never cap a heading block at a reading measure — that pushes the last word
    onto its own line while half the page sits empty. Constrain prose only.
  - Card grids need minmax(min(300px,100%),1fr) or they overhang narrow
    phones.

RULE 6 — REMOVABLE. All of this comes off the day the client says yes. Fence
the tab bar markup and CSS with obvious comments, keep the copied site and the
offer in their own files, and write down in the README exactly what to delete
and which lines to strip so the new site is left alone at the root.

RULE 7 — VERIFY BEFORE YOU CLAIM IT WORKS. Serve the site and check every
route, including the redirects. Render all three tabs at widths from 320px to
1920px with the real fonts loaded, measuring line boxes rather than eyeballing
screenshots: single-word headlines must occupy exactly one line and nothing
may overflow the viewport at any width. Confirm the copied site renders with
its own styling and that no asset still points at their live host. Report what
you checked and anything you could not.
```

---

## Notes

- For the two-tab version — no copy of their current site, just the new one
  and the offer — use `pitch-prompt.md` instead.
- Fetching someone's site can be blocked by network policy, by Cloudflare, or
  by a login wall. The prompt tells the agent to fall back to an iframe and
  say so; that honesty matters more than the tab looking finished.
