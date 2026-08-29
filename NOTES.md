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

### 2026-08-29 — build
- **Tried:** opening `public/index.html` directly via `file://` in the Browser pane to check
  the layout after writing it.
- **Broke:** rendered with zero CSS/JS — plain unstyled serif text. The Browser pane treats a
  local file outside the project folder as a "static snapshot" and doesn't load its linked
  resources.
- **Fixed by:** `npx serve public` and testing against `http://localhost` instead. That's when
  the dark-mode toggle, grid layout, and all styling actually rendered and could be verified
  (not just assumed from reading the CSS).
- **Learned:** first full-content CI run scored Best Practices 0.96, not 1.0. Pulled the actual
  Lighthouse report JSON instead of guessing why — the `errors-in-console` audit (weight 1) was
  failing on a real browser console error: `favicon.ico` 404. Added `public/favicon.svg` (inline
  SVG, no external asset) and re-ran; confirmed via the same report download that all four
  categories hit 1.0. The general lesson: when a Lighthouse score isn't 1.0, find the specific
  failing audit in the report before writing an explanation anywhere — the first guess (some
  best-practices audit not fully applying to a page with no forms) would have been wrong.

### 2026-08-29 — post-ship: fix Chrome dependency, not just document it
- **Tried:** documenting in the README that `npm run lhci` needs a system Chrome/Edge/Chromium
  installed, since `chrome-launcher` only searches the host for one — treated it as an accepted
  limitation and shipped.
- **Broke:** nothing broke exactly, but it's a real gap — a clean clone on a browser-less
  machine (a fresh CI container image, a minimal VM) would fail that step with no clear error.
  Documenting a gap isn't the same as closing it, and the ship gate's own "clean-clone check"
  item is supposed to mean the documented steps actually work, not just that their limits are
  written down.
- **Fixed by:** added `puppeteer` as a pinned devDependency — it downloads its own Chromium
  build during `npm install` — and wrote `scripts/run-lhci.js` to resolve
  `puppeteer.executablePath()` (async in puppeteer v23+, so this couldn't be a plain
  `lighthouserc.json` value) and set `CHROME_PATH` before invoking `lhci autorun`.
  `chrome-launcher` honors `CHROME_PATH` as an override ahead of its own host search.
- **Verified, not assumed:** confirmed the fix actually changed behavior, not just added code —
  the Lighthouse report's own `userAgent` string read `HeadlessChrome/152.0.0.0`, the exact
  version puppeteer downloaded, proving it used the bundled binary rather than falling through
  to whatever system Chrome happens to be on this machine.
- **Learned:** "document the limitation" and "fix the limitation" read similar in a PR but
  aren't the same thing, and the ship gate's clean-clone check is specifically there to catch
  the difference. Worth carrying forward: when a gate item surfaces an implicit dependency,
  the default response should be "can this be removed" before "how do I explain it."

---

## Rejected approaches

| Approach | Why rejected |
|---|---|
| Reuse `_template/` as-is | Zero Python code in this project; pyproject.toml/pytest would be dead weight. |
| pa11y-ci for accessibility, separate from Lighthouse | Lighthouse's own Accessibility category (Chrome's axe-core rules) already gives a CI-gated a11y score; a second tool would duplicate it without adding coverage. |
| Deploy from repo root | Would publish README.md/NOTES.md/package.json alongside the site. Moved deployable assets into `public/` so only the site ships. |

## Open questions

- [ ] Resume/CV PDF link in the footer — deferred, user will add later.
- [ ] `INTERVIEW.md` / `RESUME_BULLET.md` still need answers before `/ship`.
