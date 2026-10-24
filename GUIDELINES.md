# portfolio-site — B1

**Tier:** 1 ⭐ · **Category:** B — Web dev HTML/CSS · **Wave:** 1

Root rules in `../GUIDELINES.md` apply, **except** the `uv`/Python conventions — this project has
zero Python. It's a static site: `public/` is the deployable HTML/CSS/JS, repo root holds docs
and npm-based CI tooling only (no runtime dependency, no build step).

## What this is

The portfolio hub — semantic HTML, CSS Grid/Flexbox, dark mode, a11y, Lighthouse 95+, deployed
to GitHub Pages. Links every shipped repo, one card each, grouped into the four target roles
(data science & ML, AI engineering, data engineering, analytics & Python development).

## Stack

Vanilla HTML5 / CSS3 / JS — no framework, no bundler. npm used only for two dev-only CI gates:
`html-validate` and `@lhci/cli` (Lighthouse CI), both version-pinned in `package.json`.

## Acceptance criteria

- [x] Semantic HTML (proper landmarks, heading hierarchy)
- [x] CSS Grid/Flexbox layout, no framework
- [x] Dark mode (respects `prefers-color-scheme`, toggle overrides it)
- [x] Lighthouse Performance/Accessibility/Best Practices/SEO all ≥ 95, CI-gated — currently 100/100/100/100
- [x] Links every shipped repo with real descriptions — 35 cards in four role groups as of 2026-10-24
- [x] Deployed and live on GitHub Pages — https://prithv122.github.io/portfolio-site/
- [ ] Ship gate passes — INTERVIEW.md/RESUME_BULLET.md still template placeholders

## Project-specific notes

- `public/` is the GitHub Pages deploy root (`.github/workflows/deploy.yml`) — anything meant
  to be publicly served goes there, not at repo root.
- `npm install` before `npm run validate` / `npm run lhci` locally.
- No `.env` — nothing here reads environment variables.
- **Card numbers come from the linked repo's README §5 (Results), on its public `main`.** No
  number goes on a card that the README does not state. When a project ships, add its card to
  the matching role group in the same pass, and re-check any card whose README changed.
