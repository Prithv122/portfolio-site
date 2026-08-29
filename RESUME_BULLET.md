# Resume Bullets — portfolio-site

Form: **action → technical specifics → measured outcome.** Numbers or it doesn't go on the resume.

---

## Bullets

- Built and deployed a portfolio site (vanilla HTML5/CSS3/JS, no framework) linking six
  shipped, CI-tested repos, achieving Lighthouse scores of 100/100/100/100 (Performance,
  Accessibility, Best Practices, SEO), enforced as a hard CI gate at ≥95 on every push, not
  just measured once.
- Built a two-stage GitHub Actions pipeline (HTML validation, then Lighthouse CI) that caught
  a real regression — a missing favicon causing a browser console error — before it reached
  production, diagnosed from the actual failing audit in the Lighthouse report rather than
  guesswork.

## Which roles this supports

- [ ] Data Scientist / ML
- [ ] AI Engineer (LLM/NLP/CV)
- [ ] Data Engineer
- [ ] Data Analyst / Python Developer

None, honestly — this is the portfolio's own hub page, not a demonstration of any of the four
target roles' technical skills (it's zero-Python, no data). Its job is to be the one link on
the resume that makes the other six projects legible, not to earn a bullet of its own. If it
comes up in an interview, it's more likely as "and here's the site you're looking at right
now" than as a rehearsed bullet.

## Keywords this project earns

Semantic HTML5, CSS Grid, CSS Flexbox, CSS custom properties, `prefers-color-scheme` /
dark mode, web accessibility (WCAG-aligned via Lighthouse's axe-core-based audit),
Lighthouse CI, GitHub Actions (multi-workflow CI/CD), GitHub Pages deployment.

---

### Bad vs good

❌ "Built a machine learning model to predict customer churn using Python."
✅ "Built a churn classifier on 240k accounts (LightGBM, 1:40 class imbalance) with isotonic calibration and cost-sensitive thresholding, lifting precision@10% from 0.31 to 0.58 over the business's existing rules baseline."

The second one is answerable in an interview. The first invites the question you can't answer.
