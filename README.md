# Design Evaluation Checklist

A single-page tool for evaluating designs (and AI-generated prototypes) across four
dimensions: **Aesthetic & visual appeal**, **Functionality**, **Usability**, and
**Prompt adherence**.

It's a self-contained `index.html` — no build step, no dependencies. All CSS and JS
are inline; the only external requests are Google Fonts.

## What it does

- **Rating checklist** — each dimension has a set of concepts you rate Strong / Weak,
  with an optional note per concept. It computes a 1–7 score per dimension and
  generates a prose write-up (issues first, then strengths, prefixed by your
  one-line overall impression).
- **Gate overrides** — some dimensions can be short-circuited (e.g. "prototype failed
  to render" → N/A; "prompt was illegible" → auto score of 7).
- **Prompt analyzer** — paste an AI prompt and it extracts explicit requirements
  (bullet detection + must/should/want tiering) and implicit ones (role, audience,
  platform, tone).

## Local development

It's just a static file. Open it directly, or serve it:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

Edit `index.html` and refresh.

## Deployment

Hosted on **Vercel**, deployed automatically from the `main` branch.

- Production: pushes to `main` deploy to production.
- Pull requests get preview deployments.

`vercel.json` holds the static-hosting config (clean URLs, no trailing slash).

## Structure

| File | Purpose |
|------|---------|
| `index.html` | The entire app — markup, styles, and logic |
| `vercel.json` | Vercel static hosting config |
