# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

LaTeX paper: **"Physically-Feasible Reactive Synthesis for Terrain-Adaptive Locomotion"** — a robotics research paper on quadrupedal locomotion over dynamic terrains, combining reactive synthesis with mixed-integer convex programming (MICP) for footstep planning.

The repository is synced with Overleaf (origin is an Overleaf git remote). Commits typically come from either Overleaf edits or offline local edits.

## Build Commands

The paper is compiled with standard LaTeX tooling (pdflatex + bibtex). There is no Makefile; compile manually:

```bash
# IEEE journal version (primary)
pdflatex main.tex && bibtex main && pdflatex main.tex && pdflatex main.tex

# IJRR/SAGE journal version
pdflatex main_ijrr.tex && bibtex main_ijrr && pdflatex main_ijrr.tex && pdflatex main_ijrr.tex
```

## Document Targets

- **`main.tex`** — IEEE format (`IEEEtran` class, `IEEEtran.bst` bibliography style)
- **`main_ijrr.tex`** — SAGE/IJRR format (`sagej` class, `SageH.bst` bibliography style)

Both targets share all content sections and macros.

## Repository Structure

- **`journal_tex/`** — All section content files. Both main documents `\input` the same set: abstract, introduction, related_work, preliminaries, problem_statement, offline_synthesis, online_execution, results, hardware, discussion, conclusion. `approach.tex` and `case_study.tex` also live here but are **not** currently included (case_study is commented out in both `main.tex` and `main_ijrr.tex`); treat them as legacy unless explicitly told to wire them back in.
- **`commands.tex`** — Shared LaTeX macros: reviewer comment commands (`\qm`, `\hkc`, `\ye`, `\ziyi`), calligraphic shorthands (`\cA`–`\cZ`), formal methods notation (specs, strategies, domains), and physics/math symbols.
- **`references.bib`** — Bibliography database.
- **`figure_source/`** — SVG source files for figures (exported to PDF/PNG at top level).
- **`backup/`** — Prior conference versions (ICRA 2025, IROS 2025).

## Key Conventions

- Reviewer/author comment macros are color-coded: `\ye{...}` (red), `\ziyi{...}` (blue), `\qm{...}` (cyan), `\hkc{...}` (blue). Use `\todo` for TODO markers.
- Bold vectors use `\v{x}` (mathbf) and `\vg{x}` (bm/bold Greek).
- The paper uses `algorithm2e` (not `algorithmic`) for pseudocode.
- Figures are included as PDF/PNG at the repository root; SVG sources live in `figure_source/`.
- All new content added during the IJRR revision is wrapped in `\revised{...}` (renders in blue).

## Current State: IJRR Revision

The paper is being prepared for **IJRR submission** (target: `main_ijrr.tex`). An earlier version was rejected at **T-RO Manuscript 25-1489** because the editor and three reviewers concluded the manuscript did not yet meet the bar for "new results of substantive research significance and impact beyond the previous papers" (T-RO's recently introduced rule for journal versions of conference papers). The conference paper is `zhou2025physically` (IROS 2025).

### Contribution framing
The introduction now lists **four contributions**: (1) the integrated reactive-synthesis-and-MICP framework, (2) scalable offline synthesis via the high-level manager (state-space reduction $80.9$–$90.1\%$) and symbolic repair with dynamic feasibility checks (MICP-solve reduction $71.6$–$97.6\%$), (3) the online-execution module bridging offline synthesis and real-world deployment with three new mechanisms introduced in this article, (4) comprehensive empirical validation. The three new online-execution mechanisms are **delay-aware coordination**, **kinematic-feasibility re-targeting**, and **collision-region selection** (binary $\v H_{s,w,j}$ variables plus a swing-foot clearance threshold $h_{\text{swing}}$).

### Tone rules for revision and response letter
- **Do not** mention the conference version before the last paragraph of the introduction (the conference-delta paragraph is the only place that cites `zhou2025physically`).
- **Do not** say IJRR has a different/easier policy than T-RO. Frame as "we took the technical concerns seriously and made substantial efforts."
- **Do not** over-emphasize "restoring from the conference version." Phrase additions as direct answers to reviewer concerns, not as restorations.
- **Do not** concede that the journal extensions are "engineering" or "implementation choices." Defend them as substantive (they close concrete deployment gaps unaddressed by prior synthesis-MICP work).
- Use gentle, collaborative wording in the response letter ("we appreciate this suggestion", "while a full implementation was beyond the scope of this revision"); avoid blunt phrasings like "we chose not to".
- Replace any "Minor:" subsection prefixes in the response letter with sequential reviewer-comment numbering.
- Figure captions: aim for **at most three lines** each.

### Hardware / implementation facts (use these wherever the setup is reported)
- Intel Core **i9-13900K (24 cores)**, **64 GB RAM**, **Gurobi 9**.
- Hardware MICP-solve std-devs in [`hardware.tex` Table 4](journal_tex/hardware.tex): Unstructured 1: 2.50, Unstructured 2: 0.25, Rebar 1: 2.20, Rebar 2: 5.50, Rebar 3: 4.41 s. Rebar values are exact reconstructions from quartile data; unstructured values are range-rule rough estimates.
- Pure-MIP planning region anchor: **2 s horizon ↔ 2 cells (two consecutive symbolic transitions)**, with each additional second of horizon adding half a cell.
- GR(1) complexity: per Bloem et al. 2012, GR(1) synthesis is $O(nm|\Sigma|^2)$ symbolic next-step computations (quadratic in state-space size). Since $|\Sigma| = 2^{|V|}$, this is exponential in the number of variables — the framing used in our intro and discussion. The "exponential in variables" shorthand is correct and consistent with the paper's "exponential in formula size, polynomial in controller" framing (p. 9 of the paper).

### Reviewer-response letter

- Location: `reviewer_response/` — separate document built from `reviewer_response/main.tex`, using `NSF.cls` (with `[timesnewroman]` option that triggers a benign unused-option warning; do not "fix" the class file).
- The response letter uses `xr` to pull `\ref`/`\pageref` labels from `../main_ijrr.aux` so manuscript cross-references resolve. To avoid `bibcite`-collision warnings between `xr` and the response letter's own bibliography, `\bibcite` is **temporarily neutered** around `\externaldocument{../main_ijrr}`. Do not remove this guard.
- The response letter `\input{../commands}` to access manuscript-side macros (`\skillpre`, `\envsafetynothard`, `\statevar`, etc.) and `\renewcommand`s `\todo` to take an argument. Local `\v`, `\vg`, `\R` are defined before `\input{../commands}`.
- Build sequence: build `main_ijrr.tex` first to refresh `main_ijrr.aux`; then build `reviewer_response/main.tex` so labels resolve.
- Section files in `reviewer_response/sections/`: `AuthorsMessage.tex`, `ResponseToEditors.tex`, `Reviewer4.tex`, `Reviewer5.tex`, `Reviewer7.tex` (reviewers are numbered as in the original T-RO submission).
- The architectural-rationale-for-two-level-discrete-decisions discussion lives in the response letter only (not in the manuscript), since it defends an existing design choice rather than presenting new content. The reactivity / update-frequencies discussion lives in the manuscript discussion section.

### Where major revisions live in the manuscript

- [`journal_tex/introduction.tex`](journal_tex/introduction.tex) — compressed scalability paragraph (manager + symbolic repair with the two headline numbers), motivation paragraph for the two key online-execution mechanisms, four-bullet contribution list, conference-delta paragraph (only at the end).
- [`journal_tex/preliminaries.tex`](journal_tex/preliminaries.tex) — informal forward-definition of symbolic repair placed at the first mention of the term within the GR(1) paragraph (not at the start of the subsection).
- [`journal_tex/offline_synthesis.tex`](journal_tex/offline_synthesis.tex) — Task Specification Encoding subsection now states that preconditions/postconditions are user-defined and the encoding maps them deterministically; disjunction-over-precondition-pairs clarification follows the $\syssafetynothard$ equation.
- [`journal_tex/online_execution.tex`](journal_tex/online_execution.tex) — collision-avoidance subsection rewritten with explicit two-level structure (symbolic level: obstacle class + runtime re-synthesis for large obstacles; MICP level: $\v H_{s,w,j}$ + $h_{\text{swing}}$ for in-transition clearance). Out-of-scope perception statement included.
- [`journal_tex/results.tex`](journal_tex/results.tex) — Implementation Details subsection (Gurobi 9, i9-13900K, 64 GB RAM); restored Offline Synthesis and Repair Time table; Runtime Repair Case Study subsection (Unforeseen Terrain States: 12.36 s = 0.18 s + 12.18 s; Solving Failure: 0.11 / 0.07 s); pure-MIP benchmark with 2 s ↔ 2-cell anchor and tightened figure captions.
- [`journal_tex/hardware.tex`](journal_tex/hardware.tex) — heuristic-baseline rationale trimmed (points to discussion section for stronger baselines); std-dev column added to MICP-solve-time Table 4.
- [`journal_tex/discussion.tex`](journal_tex/discussion.tex) — new subsections: Reactivity and Update Frequencies (two bottlenecks: symbolic-side scalability + MICP solve cost; both adaptable to higher frequency); Stronger Baseline Comparisons as Future Work (Winkler 2018, ANYmal-Parkour, RLOC); Extension to Bipedal and Other Platforms. Angular-momentum disclosure on the MICP dynamics linked from the dynamics equation.
