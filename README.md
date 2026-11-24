# Structure Follows Uncertainty: Cyclic State Estimation and Control under Partial Observation

Paper source only (code lives separately).

- `main.tex` — TMLR submission (set as the main document on Overleaf).
- `arxiv.tex` — arXiv preprint; same `sections/`, `tables/`, `figures/`, `references.bib`, different preamble.
- `sections/abstract.tex` — shared abstract.
- `notes/` — revision log (`CHANGES.md`) and internal review (`REVIEW_ARS.md`); not compiled.

Build: pdflatex → bibtex → pdflatex ×2 (Overleaf default), or `tectonic -r 3 main.tex`.
