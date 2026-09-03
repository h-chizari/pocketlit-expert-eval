# PocketLit Expert Evaluation

A static webpage for human-expert evaluation of PocketLit's financial-literacy
responses, using the same five criteria as the automated LLM-judge pipeline
(factual accuracy, completeness, consumer clarity, hallucination check,
consumer safety — each 1–5). Model identity and RAG configuration are hidden
from raters (blind review).

**Live page:** enable GitHub Pages on this repo (Settings → Pages → Deploy
from branch `main` / root) and share the resulting URL.

## Assigning items to raters

The full pool is 1,488 items (`data/items.json`). Split it across raters with
URL query params — 1-indexed, inclusive:

```
https://<your-pages-url>/?start=1&count=200      # rater A: items 1-200
https://<your-pages-url>/?start=201&count=200     # rater B: items 201-400
...
```

Omit the params to assign the full set to one rater (not recommended for a
single person — 1,488 items is a large workload; split across your invited
experts instead).

Each rater enters their name/email once; progress autosaves to their
browser's local storage and resumes automatically if they return to the same
link on the same device/browser. They can also download a partial export at
any time and re-import it later (or on another device) via "Load it" on the
start screen.

## Collecting results back

There is no backend — nothing is transmitted automatically. Each rater clicks
**"Download my ratings (.json)"** when done (or partway through) and emails
you the file. Once you have the files, merge them with the private model/config
mapping using `scripts/merge_results.py` (kept in the private PocketLit repo,
**not** in this public repo, since it reveals which model/RAG-config produced
each response) to reconstruct de-anonymized results for analysis.

## Repo contents

- `index.html` — the rating webpage (vanilla HTML/JS, no build step)
- `data/items.json` — blinded item pool: `id`, `query`, `context` (retrieved
  documents, same 5-doc/500-char format as the GPT-judge prompt, `null` for
  no-RAG items), `response`. No model, RAG-config, or ground-truth fields —
  intentionally, to keep raters blind.

## Regenerating the dataset

If the underlying experiment data changes, regenerate `data/items.json` from
the private PocketLit repo's `experiment_data/quality/` using the build
script kept there (not committed here, since it also emits the private
model/config mapping).
