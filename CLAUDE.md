# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Static **academic project page** for the paper *"Physically-Feasible Reactive Synthesis for Terrain-Adaptive Locomotion"* (quadrupedal locomotion via reactive synthesis + MICP). The site is a single-page HTML site built from the [Academic Project Page Template](https://github.com/eliahuhorwitz/Academic-project-page-template) (itself adapted from Nerfies) and is hosted on GitHub Pages (`.nojekyll` present; remote `synthesis-micp/synthesis-micp.github.io`-style repo).

## Architecture

There is no build step, no package manager, no test suite, and no server-side code. The entire site is one HTML file plus static assets:

- [index.html](index.html) — the entire page. Content is organized as `<section>` blocks (teaser video, abstract, approach summary, scenario videos, optional poster/BibTeX). Sections that aren't currently used are kept as commented-out HTML so they can be re-enabled by uncommenting (don't delete commented blocks unless asked).
- [static/css/](static/css/) — vendored Bulma + bulma-carousel + bulma-slider + FontAwesome, plus [index.css](static/css/index.css) for page-specific overrides.
- [static/js/](static/js/) — vendored Bulma carousel/slider plugins, FontAwesome, and [index.js](static/js/index.js) for carousel/slider initialization.
- [static/images/](static/images/), [static/videos/](static/videos/), [static/pdfs/](static/pdfs/) — all media. Videos are autoplay/muted/loop and inlined directly (no YouTube embeds in the current build).
- `static/images/cartoon_dog.png` is the favicon (referenced from index.html; do not rename without updating the `<link rel="icon">`).

## Editing the page

- All copy, video paths, and link targets live in [index.html](index.html). Edit it directly — there's no templating layer.
- Width/layout tweaks on the video blocks are done via inline `style="width: ..."` on the `<video>` or its wrapping `<div>` (recent commits in `git log` show this is the established pattern). Prefer inline styles for one-off layout changes rather than expanding `index.css`.
- The page is currently in **anonymous-submission mode**: author block says "Anonymous Author(s)", the PDF link points to `static/pdfs/paper_anonymous.pdf`, and the GitHub button is a placeholder ("Code (coming soon)"). Leave these anonymous unless explicitly told the paper is de-anonymized.
- Social-media meta tags (`og:`, `twitter:`) at the top of `index.html` still contain template placeholders like `URL OF THE WEBSITE` and `your_banner_image.png` for some fields — fix only the specific ones the user asks about.

## Local preview

No dev server is configured. To preview locally:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

(Opening `index.html` directly with `file://` also works but some browsers block autoplay/video MIME sniffing under `file://`.)

## Deployment

Pushing to `master` on the `origin` remote publishes the page via GitHub Pages — there is no separate build/deploy step. Commit messages in this repo are short and lowercase ("updated the pdf", "changed the width of some videos"); match that style.

## `source/` directory (not in git)

The local `source/` folder contains the **LaTeX manuscript** for the paper and is **excluded from this repo** (it's not tracked — see `git ls-files`). It has its own [CLAUDE.md](source/CLAUDE.md) describing the LaTeX build, IEEE/IJRR targets, and the in-progress IJRR revision. If the user asks about paper content, contributions, or the response letter, work inside `source/` and treat that directory as a separate project; do not commit changes there from this repo.

When the user asks to "update the PDF on the site", the workflow is: build the manuscript in `source/` (per its CLAUDE.md), then copy the resulting PDF over `static/pdfs/paper_anonymous.pdf` and commit *here*.
