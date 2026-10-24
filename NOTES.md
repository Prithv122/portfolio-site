# Build Notes — portfolio-site

Working notes: what broke, what you tried, why you chose X over Y.
Not for recruiters — for you, six months from now, in an interview.

Keep it rough. Rough is the point.

---

## Log

### 2026-08-29 — scaffold
- **Tried:** starting from the standard `_template/` (uv + pyproject.toml + pytest), per the
  usual scaffolding flow.
- **Broke:** nothing broke, but it was the wrong template — this project has zero Python. It's
  a static HTML/CSS/JS site deployed to GitHub Pages.
- **Fixed by:** discarded the copied template, hand-built a static-site scaffold instead:
  `public/` holds the deployable site (`index.html`, `css/`, `js/`), repo root holds docs +
  tooling config. npm used only for two dev-only CI gates (`html-validate`, `@lhci/cli`) —
  no runtime dependency, no build step, no framework.
- **Learned:** the portfolio's root `GUIDELINES.md` conventions ("uv everywhere") were written with
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

### 2026-10-24 — refresh: six cards → every shipped project (35), grouped by role
- **Why:** the page still listed only the six Wave-1 repos while ~30 more had shipped. Adding
  only the newest few would have left a visible hole, so this was one pass over all of them.
- **Source list:** `CATALOG.md` rows marked shipped, cross-checked against
  `gh repo list` for visibility. #29 websocket-live was published earlier the same day, so it
  is in. #37 and #39 were folded into #22 and #38; #38, #40 and #42 onward are not shipped.
- **Numbers:** every stats line was copied from that repo's README §5 as it stands on its
  public `main` (`git show origin/main:README.md`), not from the local working tree or from
  `PROGRESS.md`. Two catches:
  - `pytemplate`'s old card said "98% coverage"; its README says 100% of branches exercised
    and gives no line-coverage figure. Card now quotes the README.
  - `elt-dbt-dagster`'s README says 32/32 dbt tests on `main`, but its unmerged
    `semantic-layer` branch found the real count was 26 (28 after its fix). The card uses
    numbers both versions state identically (7,310 rows; Chennai 66 → 80 heat days) so it
    stays true whether or not that branch is merged.
- **Grouping:** four sections in the order the resume's role list reads. The fourth
  (analytics & Python development, 16 cards) is the biggest; inside it the analytics cards come
  first, then APIs and web, then the small Python tools.
- **Still hand-written HTML.** INTERVIEW Q3 predicted this gets error-prone near 47 cards. At
  35 the no-build rule still won; the price was checking each card against its README by hand.
- **Local Lighthouse on Windows broke:** `chrome-launcher` 1.2.1 throws `EPERM` deleting its
  temp profile right after the audit finishes (the folder is gone a moment later — a file-lock
  race on this host, not the page). Same with the sandbox off and with Chrome's crash reporter
  disabled. CI was replayed in a Linux `node:22-bookworm` container instead, which is closer
  to the hosted Ubuntu runner anyway.
- **CI replay (hosted Actions unavailable until about 2026-11-02):** fresh `git clone` of the
  commit inside the container, `npm ci` per job, then `ci.yml`'s steps in order. On
  `7481786`: `npm run validate` clean; `npm run lhci` passed every assertion, **100 / 100 /
  100 / 100 on all three runs** (Lighthouse 12.6.1, HeadlessChrome 152, 35 cards). Differences
  from the hosted runner: Debian 12 instead of Ubuntu; the container needed `apt-get update`
  plus `npx puppeteer browsers install chrome --install-deps` for Chrome's system libraries,
  and `--shm-size=2g` (Docker's default 64 MB `/dev/shm` crashes the Chrome tab); reports were
  written to disk (`LHCI_UPLOAD__TARGET=filesystem`) instead of uploaded to temporary public
  storage, so nothing about the unpublished page left the machine.

---

## Rejected approaches

| Approach | Why rejected |
|---|---|
| Reuse `_template/` as-is | Zero Python code in this project; pyproject.toml/pytest would be dead weight. |
| pa11y-ci for accessibility, separate from Lighthouse | Lighthouse's own Accessibility category (Chrome's axe-core rules) already gives a CI-gated a11y score; a second tool would duplicate it without adding coverage. |
| Deploy from repo root | Would publish README.md/NOTES.md/package.json alongside the site. Moved deployable assets into `public/` so only the site ships. |

## Open questions

- [ ] Resume/CV PDF link in the footer — deferred, user will add later.
- [ ] `INTERVIEW.md` / `RESUME_BULLET.md` still need answers before shipping.
