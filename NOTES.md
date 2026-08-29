# Build Notes — portfolio-site

Working notes: what broke, what you tried, why you chose X over Y.
Not for recruiters — for you, six months from now, in an interview.

Keep it rough. Rough is the point.

---

## Log

### 2026-08-29 — scaffold
- **Tried:** starting from the standard `_template/` (uv + pyproject.toml + pytest), per the
  usual `/new-project` flow.
- **Broke:** nothing broke, but it was the wrong template — this project has zero Python. It's
  a static HTML/CSS/JS site deployed to GitHub Pages.
- **Fixed by:** discarded the copied template, hand-built a static-site scaffold instead:
  `public/` holds the deployable site (`index.html`, `css/`, `js/`), repo root holds docs +
  tooling config. npm used only for two dev-only CI gates (`html-validate`, `@lhci/cli`) —
  no runtime dependency, no build step, no framework.
- **Learned:** the portfolio's root `CLAUDE.md` conventions ("uv everywhere") were written with
  the Python-heavy waves in mind; category B (web dev) projects need a different scaffold.
  Worth remembering for B2/B3/B4 too.

---

## Rejected approaches

| Approach | Why rejected |
|---|---|
| Reuse `_template/` as-is | Zero Python code in this project; pyproject.toml/pytest would be dead weight. |
| pa11y-ci for accessibility, separate from Lighthouse | Lighthouse's own Accessibility category (Chrome's axe-core rules) already gives a CI-gated a11y score; a second tool would duplicate it without adding coverage. |
| Deploy from repo root | Would publish README.md/NOTES.md/package.json alongside the site. Moved deployable assets into `public/` so only the site ships. |

## Open questions

- [ ] Final visual design/layout — not started yet, this session only scaffolded.
