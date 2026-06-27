# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **Slidev** presentation project for an OHBM 2026 oral talk by Sina Mansour L. (NUS / University of Melbourne). The talk is scheduled for 17 June 2026, 15:45, Room Agora, Lifespan Development session, and runs for 10 minutes plus Q&A.

**Title**: Mapping Organizational Gradients of Cortical Growth Using Spectral Normative Models

The talk presents three Thickness Growth Gradients (TGG1–3) recovered via Spectral Normative Modelling applied to 78,405 lifespan scans (ages 5–95). The companion poster lives at https://sina-mansour.github.io/OHBM_2026/poster/.

`plan.md` holds the original slide-by-slide structural plan. **`slides.md` is now fully built out** and has diverged from `plan.md` in wording and ordering (e.g. a Disclosures slide, an SNM-training slide, and a Take-home-messages slide were added). Treat `slides.md` as the live source of truth; `plan.md` is the original intent / narrative reference.

## Development Commands

```bash
pnpm dev          # start dev server with hot reload (opens browser)
pnpm build        # build static site to dist/
pnpm run deploy   # build with base /OHBM_2026_oral/ and push dist/ to gh-pages (NOT `pnpm deploy` — reserved)
pnpm export --with-clicks --output ohbm_oral.pdf   # PDF; --with-clicks captures every reveal
```

After most edits, verify with: `pnpm build 2>&1 | grep -iE "built in|error"`. The two `INVALID_ANNOTATION` warnings from `@vueuse/core` under Rolldown are harmless.

## Deployment

- GitHub repo: https://github.com/sina-mansour/OHBM_2026_oral (git push is done by the user, not from here).
- `pnpm run deploy` builds with `--base /OHBM_2026_oral/`, writes `dist/.nojekyll`, and publishes `dist/` to the `gh-pages` branch via the `gh-pages` package. Pages serves at `https://sina-mansour.github.io/OHBM_2026_oral/`.
- **Known mismatch**: the QR code + captions on the Disclosures and Thank-You slides point to `sina-mansour.github.io/OHBM_2026/oral/`, but deployment URL is `…/OHBM_2026_oral/`. Resolve via a redirect on the main OHBM_2026 site, or repoint the QR (regenerate `assets/qr_oral.png` with the npm `qrcode` package).
- **PDF export cannot run in this sandbox** (headless Chrome is blocked from creating its control socket); the user runs it on their own machine. `playwright-chromium` is in devDependencies but its browser binary must be fetched there (`pnpm exec playwright install chromium`).

## Project Structure

```
.
├── CLAUDE.md           # this file
├── plan.md             # original slide-by-slide plan (narrative reference)
├── slides.md           # all slide content — the live source of truth
├── package.json        # pnpm; @slidev/cli, vue, theme, @vueuse/motion; dev: qrcode, gh-pages, playwright-chromium
├── theme/              # ejected @slidev/theme-seriph, customised (primary #1ab3cc, light mode)
├── components/         # custom Vue components (see below)
├── assets/             # webp/png figures referenced by slides.md (committed)
├── public/             # header.webp (title bg), favicon.ico
└── tmp/                # scratch: raw source PNGs before webp conversion — gitignored, do not commit
```

## Components (in `components/`)

- `myFooter.vue` — teal striped header/footer bars, title chip, slide counter, and the OHBM wordmark (`LOGO.png`) bottom-left. Dropped on every content slide.
- `SlideImage.vue` — the workhorse for positioned figures. Props: `cx/cy/w/h` (centre + size as % of slide), `x0/x1/y0/y1` (CSS crop fractions), `cropStates`/`containerStates` (per-click overrides keyed by absolute click number). Used for the column-by-column TGG reveals on the gradient slides.
- `SlideText.vue` — positioned text with `appear-at`/`disappear-at` click binding. NOTE: it animates on appear; for a truly static element use a plain positioned `<div>` instead.
- `BulletItem.vue` + `BulletArrow.vue` — animated bullet list with a sliding arrow tracker (`v-click`, `hide-arrow-at`).

## Slidev Architecture

**slides.md** is the single file where all slide content lives. Slides are separated by `---`. The frontmatter of the first slide sets global config (theme, title, transitions, katex, mdc).

**Animation pattern**: Slides use `v-click` for step-by-step reveals. `v-motion` is available for enter/leave transitions when needed. KaTeX and MDC syntax should be enabled globally.

**Carbon icons** (e.g. `<carbon:brain />`, `<carbon:chart-line />`) are available without any extra import.

### Gotchas (learned the hard way)

- **Markdown breaks raw HTML blocks** if lines are indented ≥4 spaces (parsed as code) or contain blank lines inside the block (terminates it, leaks literal HTML onto the slide). Keep multi-element HTML blocks flat (column 0) with no blank lines between tags.
- **`v-motion` overrides the element's `transform`.** Inline `transform: translate(-50%,-50%)` centring gets clobbered when `v-motion` is also on the element. Fix: put the centring `transform` on a plain wrapper `div` (no `v-motion`) and keep `v-motion` on an inner child.
- **Match container aspect to image aspect** when using `SlideImage` (it uses `object-fit: cover`, so a mismatched container crops the image). Compute `w`/`h` so `(w*980)/(h*552)` ≈ the image's width/height ratio. The canvas is 980×552; UnoCSS `rem` = 16px.
- **`v-click="0"` / `appear-at="0"`** shows immediately (no click needed).

### Figure / asset workflow

- Source images are dropped in `tmp/` (raw PNGs, often huge), converted to `assets/*.webp` with a small PIL script, then referenced from `slides.md`. `tmp/` is gitignored.
- **Use lossless webp (`lossless=True`) for text-heavy figures** (heatmaps, plots, labelled panels) — lossy webp blurs fine text. Use lossy `quality≈90` only for photographic brain renders.
- Trim baked-in whitespace from logos before laying them out (flatten onto white, crop to non-white bbox). Normalize logos by **height**, not width; balance visual weight per-logo (wide wordmarks shorter, square marks taller). Cambridge logo was rasterised from SVG via `inkscape`.
- The big multi-panel figure `assets/tgg_components.webp` is shown column-by-column via `SlideImage` `cropStates`/`containerStates`; column x-boundaries ≈ 0.209 / 0.395 / 0.597 / 0.80 / 1.0.

## Final Deliverables

The final OHBM submission is a **PowerPoint with embedded per-slide videos**, generated by a Playwright-based recording pipeline that captures each Slidev slide (with its animations) as an MP4 and assembles them into a PPTX. A PDF fallback is also produced via `slidev export --with-clicks`. The web version is hosted alongside the OHBM_2026 MkDocs site.

## Reference Template

A previous talk's Slidev project can be used as a reference for theme, animation patterns, and component setup:

- Location: `/mnt/local_storage/Research/Original_Work/Talks/2026/BGD_Lab/slides/bgd_lab_2026`
- The content is for a different (related) talk, but the visual style, layouts, `v-click` and `v-motion` usage, custom theme overrides, and footer/header components can be carried over.

When in doubt about visual conventions, look there first rather than improvising.

## Working Style

- Incremental, one-step-at-a-time; ask before making sweeping changes
- British English in prose ("modelling", "organisation", "behaviour", "colour")
- No em-dashes or en-dashes in prose content
- Push back on choices that won't render well or harm the narrative