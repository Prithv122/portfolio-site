# portfolio-site — B1

**Tier:** 1 ⭐ · **Category:** B — Web dev HTML/CSS · **Wave:** 1

Root rules in `../CLAUDE.md` apply, **except** the `uv`/Python conventions — this project has
zero Python. It's a static site: `public/` is the deployable HTML/CSS/JS, repo root holds docs
and npm-based CI tooling only (no runtime dependency, no build step).

## What this is

The portfolio hub — semantic HTML, CSS Grid/Flexbox, dark mode, a11y, Lighthouse 95+, deployed
to GitHub Pages. Links every shipped repo (pytemplate, file-organiser, ledger-cli,
async-crawler, logstats, filetx, and future ones as they ship).

## Stack

Vanilla HTML5 / CSS3 / JS — no framework, no bundler. npm used only for two dev-only CI gates:
`html-validate` and `@lhci/cli` (Lighthouse CI), both version-pinned in `package.json`.

## Acceptance criteria

- [x] Semantic HTML (proper landmarks, heading hierarchy)
- [x] CSS Grid/Flexbox layout, no framework
- [x] Dark mode (respects `prefers-color-scheme`, toggle overrides it)
- [x] Lighthouse Performance/Accessibility/Best Practices/SEO all ≥ 95, CI-gated — currently 100/100/100/100
- [x] Links all six shipped repos with real descriptions
- [x] Deployed and live on GitHub Pages — https://prithv122.github.io/portfolio-site/
- [ ] Ship gate passes (`/ship`) — INTERVIEW.md/RESUME_BULLET.md still template placeholders

## Project-specific notes

- `public/` is the GitHub Pages deploy root (`.github/workflows/deploy.yml`) — anything meant
  to be publicly served goes there, not at repo root.
- `npm install` before `npm run validate` / `npm run lhci` locally.
- No `.env` — nothing here reads environment variables.
