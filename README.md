# Meetink — 15-minute ship

A fictional marketplace that pairs writers with visual artists to make comics, graphic
novels and manga. One static HTML file, no build step, deployed on GitHub Pages.

---

## The 15 minutes

| Time | What you do |
|---|---|
| 0–2 | Make the repo, drop `index.html` in, commit |
| 2–4 | Turn on GitHub Pages, get the live URL |
| 4–12 | Iterate with Claude Code — one change per prompt, commit after each |
| 12–15 | Check it on your phone, push, done |

### Minute 0–2 — repo

```bash
mkdir meetink && cd meetink
# put index.html here
git init
git add .
git commit -m "Ship Meetink landing page"
gh repo create meetink --public --source=. --push
```

No `gh` CLI? Create the repo on github.com, then:

```bash
git remote add origin https://github.com/th3-cr0w/meetink.git
git branch -M main
git push -u origin main
```

### Minute 2–4 — deploy

Repo → **Settings → Pages → Source: Deploy from a branch → `main` / `(root)` → Save**.

Live in ~60 seconds at `https://th3-cr0w.github.io/meetink/`.

That's the whole deploy story for a static site. Every push to `main` redeploys.

### Minute 4–12 — iterate with Claude Code

```bash
claude
```

Then work one change at a time. Prompts that actually work:

- `Read index.html and explain the structure to me in 10 bullets — what each section does and how the "bind" animation works.`
- `The hero headline is too big on a 375px viewport. Fix it and tell me which CSS property you changed and why.`
- `Add a fourth roster card for a letterer. Match the existing card markup exactly — don't invent new classes.`
- `Split the inline CSS into styles.css and link it. Don't change any rules.`
- `Add Open Graph tags so the link previews properly in Slack and WhatsApp.`

Two habits worth building now:

1. **Plan mode first** on anything bigger than a tweak — hit `Shift+Tab` twice, let it
   propose a plan, approve it, then let it write. Cheaper than undoing a bad edit.
2. **Commit after every working change.** `git commit -am "Fix hero on mobile"`.
   Your undo button is `git`, not Ctrl+Z.

A `CLAUDE.md` at the repo root keeps it on rails across sessions:

```md
# Meetink
Static site. One file: index.html. No build step, no framework, no npm.
Colours and fonts live in CSS custom properties under :root — always reuse those.
Panels are 3px solid borders with a 6px hard shadow. Never add border-radius.
Keep it working at 375px wide. Keep :focus-visible styles intact.
```

---

## What's in the file, and why

**Structure** — semantic HTML: `header > nav`, `main` with `section` per band, `footer`.
Search engines and screen readers both read that outline.

**Design tokens** — every colour, font and border is a CSS custom property in `:root`.
Change `--pink` once, the whole site changes. This is how real design systems start.

**Layout** — CSS Grid for the two-panel hero and every card row.
`repeat(auto-fit, minmax(260px, 1fr))` gives you responsive columns with no media query:
the browser fits as many 260px-minimum columns as it can and stretches them.

**The signature interaction** — the hero is a script panel and an art panel separated by a
wide *gutter* (the real comics term for the space between panels, where the reader's
imagination does the joining). CSS transitions the grid column's width from ~104px to 14px
and un-rotates both panels. An `IntersectionObserver` fires it once when the panels scroll
into view; the button toggles it manually. That's ~15 lines of JS.

**Type** — Bricolage Grotesque (display), Archivo (body), Courier Prime (the script panel,
because comic scripts are genuinely written in Courier).

**Riso misregistration** — the pink ghost behind the headline is one `text-shadow`. It
imitates a two-ink print where the plates don't line up perfectly.

**Accessibility floor** — visible `:focus-visible` outlines, `aria-pressed` on the toggle,
`aria-label` on the SVG, `prefers-reduced-motion` respected.

---

## Where to take it next

- Split into `styles.css` + `script.js` — the natural first refactor
- Add a real form with [Formspree](https://formspree.io) (no backend needed)
- Buy a domain, point a CNAME at Pages
- Rebuild the same page in Astro or Next.js and feel the difference in what a build step buys you
