# Interview Prep — portfolio-site

**Five questions, five answers.** An unanswered question means this project is not shipped.

If you can't answer one, you don't understand that part of your own project yet — go back and understand it. This file is the difference between a portfolio that survives a technical screen and one that collapses in it.

---

### Q1. Walk me through the architecture in 90 seconds.

_A:_ It's a static site — one `index.html`, one stylesheet, one small JS file, no framework
and no build step. `public/` is the deploy root; the repo root holds docs and CI tooling only,
so nothing but the actual site is publicly served. Two GitHub Actions workflows run on every
push to `main`: `ci.yml` runs `html-validate` for structural correctness, then Lighthouse CI
against the built `public/` directory, asserting Performance/Accessibility/Best
Practices/SEO all ≥95 — a failing score fails the build, it's not just a number I glance at.
`deploy.yml` runs `actions/deploy-pages` to publish to GitHub Pages. Content-wise: a header
with a dark-mode toggle, a CSS Grid of six project cards (one per shipped repo, each with a
real one-line pitch, tech tags, and measured stats pulled from that repo's own README), and a
footer with contact links.

### Q2. Why did you choose ___ over ___?

_A:_ Vanilla HTML/CSS/JS over a static-site generator or framework. The catalog entry for this
project explicitly asks for CSS Grid/Flexbox depth, not framework reliance — the whole point is
proving I can build a real, accessible, responsive layout without Tailwind or React doing the
work for me. A generator would also add a build step and a dependency tree to a six-card page
that doesn't need one. The tradeoff I accepted: adding project #7 means hand-editing HTML, not
just appending to a data file — documented in the README's "what I'd change at 100× scale"
section, because at real scale (hundreds of projects) that tradeoff stops being worth it.

### Q3. What's the weakest part of this, and what would break first under load?

_A:_ Not "load" in the traditional sense — it's static and served by GitHub Pages' CDN, so
traffic isn't the failure mode. The weakest part is maintainability: every new shipped project
means manually writing a new `<article>` block with the pitch, tags, and stats by hand. That's
fine at 6 projects, tedious at 20, and actively error-prone at 47 — the exact point where a
copy-paste mistake would let a stale metric sit in a card unnoticed. If I were scaling this,
the cards would be generated from a single JSON/YAML source of truth instead.

### Q4. How do you know it works? What did you measure, and against what baseline?

_A:_ Lighthouse CI, asserted in `lighthouserc.json` at ≥0.95 on all four categories, actually
measuring **100/100/100/100** — I pulled the real numbers from the CI-generated report rather
than eyeballing a local run. That measurement caught a real bug: the first full-content run
scored Best Practices 0.96, and instead of guessing why, I pulled the failing audit
(`errors-in-console`) from the report and found a genuine browser console 404 — a missing
favicon. Fixed it, re-ran, confirmed 1.0 from the same kind of report download. I also manually
verified in a real browser (not just read the CSS) that the dark-mode toggle actually flips
state, the mobile layout holds at 375px, and all seven outbound links resolve to the URLs I
intended.

### Q5. What's a design decision here you'd defend under pushback?

_A:_ Theming with CSS custom properties plus a `prefers-color-scheme` media query as the
default, with JavaScript only writing a `data-theme` override to `localStorage` when a visitor
explicitly picks something different from their system setting. The alternative — JS deciding
and applying the theme on every load — is more "obviously correct" to a lot of people, but it
means the page has no correct appearance until JS runs, which is worse for the common case (a
visitor with no stored preference, i.e. everyone on their first visit) to make the uncommon
case (an explicit override) marginally simpler to reason about. The one real cost I accepted:
a visitor who explicitly chose the opposite of their system theme can see a brief flash of the
system theme before JS applies their override, since there's no build step to inline a
blocking script. For a personal portfolio page, that's a fair trade.

---

## 30-second pitch

Six other repos are shipped, tested, and CI-green, but a resume linking to six separate GitHub
URLs asks the reader to do all the work of tying them together. I built a single hub page —
vanilla HTML/CSS/JS, no framework — with real pitches and real measured numbers pulled from
each repo, dark mode that respects system preference by default, and a CI pipeline that
actually gates on Lighthouse scores rather than just reporting them. It's live on GitHub
Pages at 100/100/100/100 across Performance, Accessibility, Best Practices, and SEO — and
that pipeline already caught one real bug (a missing favicon) before it shipped.
