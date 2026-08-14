# Prompt — add a sales pitch tab to a site

Paste the block below to an agent working in the site's repo, filling in the
five values at the top. It produces a two-tab demo: the site itself, and an
offer page that looks like it was cut from the same cloth.

The offer page is not designed from scratch — it is derived from the site it
sits on, so this works for any client, any palette, any typeface.

---

```
Add a two-tab sales pitch to this website. The site is the pitch; the second
tab asks for the money.

  Site file(s):     <path to the finished site, e.g. public/index.html>
  Client:           <name of the business>
  Price:            £<500> one-off for the site
  Changes:          £<50> per round of changes afterwards
  How they reply:   <reply to this message / email hello@… / call …>

TABS — exactly two, no others. Tab 1 is the site. Tab 2 is the offer. Put an
identical bar at the very top of both pages, above everything, so it reads as
chrome around the demo rather than part of the site. Each tab is a plain link,
numbered, with the current one filled in the site's accent colour and carrying
aria-current="page". No JavaScript — these are two real pages.

RULE 1 — THE OFFER PAGE MUST LOOK LIKE THE SITE.
Do not invent a design. Before writing a line of the offer page, read the
site's stylesheet and harvest, verbatim:
  - every CSS custom property (colours, tints, spacing, fonts)
  - display and body font families, weights, and how headings are treated —
    uppercase or not, letter-spacing, line-height
  - the component vocabulary: buttons, cards, chips/labels, list rows,
    accent bars, border weights, shadow offsets
  - page max-width and the padding scale
Rebuild the offer page from those parts. If the site uses hard borders and
offset shadows, so does the offer. If it is soft and shadowless, match that
instead. Someone flicking between the tabs should not be able to tell the two
pages were written at different times. Reuse the site's own font loading —
do not add a second typeface or any external dependency.

RULE 2 — WHAT GOES ON THE OFFER PAGE, in this order:
  1. A hero: short label, one big headline in the site's display face, and a
     sentence saying the whole thing is theirs on their own domain.
  2. What it does better. If the client has an existing website, score the two
     against each other in a two-column table — what a visitor cannot do today
     on the left, what this site does on the right, six rows, concrete and
     specific (prices visible without scrolling, what to expect on a first
     visit, works on a phone, answers the questions people are nervous to
     ask). If they have no existing site, make it a plain list of what it does
     for them instead. Never invent a flaw you have not seen — write only from
     what the current site actually does, or omit the row.
  3. Two price cards side by side, built from the site's own card component:
     the one-off price for the site, and the per-round price for changes
     afterwards. Spell out that the second is per round of changes and not a
     monthly fee, and that changing nothing costs nothing.
  4. One short paragraph on why it is priced that way — tie it to how the
     client sells their own thing if you can (a gym with no contracts gets a
     site with no subscription).
  5. A closing band: big headline one side, how to reply the other.

RULE 3 — TONE. Plain and confident. Short sentences. No agency words —
nothing "bespoke", "seamless", "solution", "leverage" or "journey". Write like
the client's own best copy sounds. Never promise anything the site does not
already do.

RULE 4 — TYPOGRAPHY THAT HOLDS. Big display headlines break in ugly ways, so:
  - A long single-word headline must never split mid-word. Viewport-based
    sizing (10vw and friends) keeps growing after the column it sits in stops
    at the page's max width, which is exactly when it snaps. Measure the word
    in the real display font, then also cap the size against its own column
    (container query units) so it can never outgrow it.
  - Do not cap a heading block at a reading measure — that pushes the last
    word onto its own line while half the page sits empty. Constrain prose,
    never headings.
  - Card grids need minmax(min(300px,100%),1fr), or they overhang narrow
    phones.

RULE 5 — REMOVABLE. This chrome comes off the day the client says yes. Fence
the tab bar markup and its CSS with obvious comments, keep the offer page in
its own file, and write down in the README exactly which files to delete and
which lines to strip.

RULE 6 — VERIFY BEFORE YOU CLAIM IT WORKS. Serve the pages and render them at
widths from 320px to 1920px with the real fonts loaded. Measure line boxes
rather than eyeballing: assert that single-word headlines occupy exactly one
line and that nothing overflows the viewport at any width. Report the results.
If webfonts are blocked in your environment, fetch the same families from npm
(@fontsource/<name>) and serve them locally so you are measuring the real
thing — a fallback font proves nothing.
```

---

## Notes

- Drop the price lines and it still works; it just asks for the money less
  specifically.
- For a three-tab version — their current site embedded as tab 1 — add: "Tab 1
  is their existing site at <url> in a full-height iframe with a strip above
  it naming the domain and an open-in-a-new-tab button, since some hosts
  refuse to be framed. The site becomes tab 2 and the offer tab 3."
