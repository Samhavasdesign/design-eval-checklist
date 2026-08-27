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

### AI mode

AI mode is the only part that calls out to a model. It runs through a Vercel
serverless function at `api/evaluate.js`, which calls `claude-opus-5` with
adaptive thinking and a JSON-schema structured output using the official
[Anthropic SDK](https://github.com/anthropics/anthropic-sdk-typescript).

**The API key lives only in Vercel's environment.** It is never in this repo,
never sent to the browser, and never visible to anyone using the site. Every
run bills the account behind that key, which is why the endpoint is passcode
gated.

Screenshots are downscaled to a 1400px long edge in the browser before upload —
it keeps the request under Vercel's 4.5 MB body cap and avoids paying for
resolution the model discards. Up to 10 screens per run.

Requirements a static screenshot cannot settle — responsive behaviour, working
forms, link destinations — are reported as **"can't be judged from a
screenshot"** rather than guessed at, and don't drag the score down.

AI mode does not work against a plain static server (`python3 -m http.server`);
the other two tools do. Use `vercel dev` to exercise it locally.

#### Required environment variables

Set both in Vercel (Settings → Environment Variables, or `vercel env add`):

| Variable | Purpose |
|---|---|
| `ANTHROPIC_API_KEY` | Your Anthropic API key. Billed per evaluation. |
| `APP_PASSCODE` | Shared passcode users must enter before the endpoint will answer. |

The function **fails closed**: if either variable is missing it refuses every
request rather than running unprotected.

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
| `index.html` | The whole front end — markup, styles, and logic |
| `api/evaluate.js` | Serverless function: holds the API key, gates on the passcode, calls Claude |
| `vercel.json` | Hosting config (clean URLs, 60s function timeout) |
| `package.json` | Declares the Anthropic SDK for the function |
