# Design Evaluation Checklist

A single-page tool for evaluating designs (and AI-generated prototypes) across four
dimensions: **Aesthetic & visual appeal**, **Functionality**, **Usability**, and
**Prompt adherence**.

It's a self-contained `index.html` — no build step, no dependencies. All CSS and JS
are inline; the only external requests are Google Fonts.

## The three tools

1. **Decode a prompt** — paste a prompt (or OCR one from a screenshot) and it
   extracts explicit requirements, implicit ones, and flags worth weighing.
2. **Evaluate a design** — the manual checklist: rate concepts Strong/Weak
   across four categories, get a 1–7 score and a prose write-up.
3. **AI mode** — paste the prompt, upload the screens it produced, and Claude
   judges prompt adherence against them.

### AI mode and your API key

AI mode is the only part that calls out to a model. It uses the official
[Anthropic TypeScript SDK](https://github.com/anthropics/anthropic-sdk-typescript)
loaded from a CDN, running in the browser with `dangerouslyAllowBrowser`, and
calls `claude-opus-5` with adaptive thinking and a JSON-schema structured
output.

**Your key never touches this repo or any server of ours.** It is stored in
`localStorage` in your own browser and sent only to `api.anthropic.com`. Each
run bills your own Anthropic account (a few cents, driven mostly by how many
screens you upload). Clear the key field and press Save to forget it.

Screenshots are downscaled to a 1568px long edge in the browser before being
sent, which keeps requests well under the API's size cap and avoids paying for
resolution the model discards.

Requirements a static screenshot cannot settle — responsive behaviour, working
forms, link destinations — are reported as **"can't be judged from a
screenshot"** rather than guessed at, and don't drag the score down.

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
