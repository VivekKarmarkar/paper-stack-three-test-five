# paper-stack-three-test-five

End-to-end test of **Stack 3** (the user-curated research paper pipeline) — walks a tree of hand-organized PDFs, enriches each via OpenAlex, and produces a publication-quality bibliography PDF.

## Overview

Stack 3 trusts the source: every PDF placed by the user in `exploration/` is treated as a real paper, no heuristic filtering, no verify/validate gates. The pipeline recursively scans the tree, extracts a best-effort title per PDF, resolves each against OpenAlex for rich metadata (authors with affiliations, venue, year, citations, abstract, topics), dedupes by DOI with address-union semantics, and emits both a markdown bibliography and a compiled LaTeX/PDF rendition.

## The exploration tree

The curated PDF tree is organized by methodological proximity to the target research idea:

- `Exact Idea/` — papers attacking the same problem (Iowa, Rose-Hulman, Texas A&M groups; PhD thesis; unique-author papers)
- `Similar Idea/` — adjacent formulations
- `Mathematical Foundations/` — forward/inverse-problem theory
- `Machine Learning Approaches/` — `Traditional/` (deep learning) and `SciML/` (neural operators)
- `Broader Relevance/` — context papers

## Pipeline outputs

- `identified_papers_user_tree_candidates.md` — Stage 2 candidate list (15 PDFs, title + absolute path per line)
- `identified_papers_user.md` — Stage 3 enriched bibliography (14 resolved via OpenAlex, 1 unresolved entry included on user-curation trust)
- `bibliography_exploration_stack.tex` / `.pdf` — LaTeX-rendered bibliography-only document

## Reproducing

Run the two-stage Stack 3 pipeline from the repo root:

```bash
# Stage 2 — tree discover
python3 ~/.claude/skills/identify-papers-user-tree/helpers/extract_tree_candidates.py exploration

# Stage 3 — OpenAlex enrichment + dedup
python3 ~/.claude/skills/identify-papers-user/helpers/enrich_user_candidates.py identified_papers_user_tree_candidates.md
```

Then build the PDF from the markdown (any LaTeX renderer over the `identified_papers_user.md` source).

## Tech stack

- Python 3 with the bundled `paper-metadata` resolver against the OpenAlex REST API
- `pdfinfo` (poppler-utils) for title extraction
- `pdflatex` for the bibliography PDF
