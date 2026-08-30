# Search-Driven Content Refresh Prioritization

**FlyRank AI/ML Internship — Capstone Project**

A decision-support system that uses search performance, engagement, and content-freshness
signals to rank content pages for human review. It does **not** predict Google's algorithm,
does **not** claim that refreshing content causes improved rankings or traffic, and does
**not** automate content changes.

## Published paper

The deployed research paper lives in [`docs/index.html`](docs/index.html) and is intended
to be served via GitHub Pages. The live URL goes in [`submission/paper_url.txt`](submission/paper_url.txt)
once Pages is enabled on this repo (Settings → Pages → deploy from `/docs` on `main`).

## Repository structure

```
├── data/raw/
│   └── content_refresh_anonymized.csv     # anonymized FlyRank content-refresh dataset
│
├── work/
│   ├── notebooks/
│   │   ├── w04_baseline_score.ipynb       # rule-based baseline + reason codes
│   │   ├── w05_model.ipynb                # logistic regression model + baseline comparison
│   │   ├── w06_validation_audit.ipynb     # random vs. grouped split, leakage audit, error analysis
│   │   └── w07_action_playbook.ipynb      # value score, priority score, archetypes, ranked queue export
│   │
│   ├── outputs/
│   │   ├── ranked_action_queue.csv        # final ranked, explainable action queue
│   │   ├── baseline_action_score.csv      # Week-4 rule-based baseline queue
│   │   ├── validation_metrics.json        # random vs. grouped metrics, confusion matrix
│   │   ├── leakage_audit.json             # leakage audit table + honest vs. leaky F1
│   │   └── monitoring_baseline.json       # baseline stats for drift monitoring
│   │
│   └── figures/                           # Figures 1–5 used in the paper
│
├── docs/
│   └── index.html                         # deployed research paper (GitHub Pages)
│
└── submission/
    └── paper_url.txt                      # single line: the published GitHub Pages URL
```

## Headline, honestly-reported results

All numbers below come from the executed notebooks in `work/notebooks/`, not from
invented placeholders.

| Metric | Random row-level split | Client-grouped split |
|---|---|---|
| F1 | 0.683 | **0.586** |
| Accuracy | 0.613 | 0.538 |
| ROC-AUC | 0.639 | 0.546 |

The client-grouped split — which keeps every client's pages entirely in either train or
test — is treated as the more honest estimate of real-world performance. It scores lower
than the random split, and that gap is reported rather than hidden.

A deliberate leakage test (adding the label-derived `trend_pct` column back into the
feature set) inflates the grouped F1 from 0.586 to 0.850, demonstrating concretely why
`trend_direction` and `trend_pct` are excluded from the honest feature set.

## Reproducing

1. Clone this repo.
2. `pip install pandas numpy scikit-learn matplotlib`
3. Run the notebooks in order: `w04 → w05 → w06 → w07`. Each notebook reads
   `data/raw/content_refresh_anonymized.csv` (or the raw GitHub URL for the same file)
   and regenerates the outputs and figures used above.

## Scope and limits

This is a capstone prototype, not a production system. See `docs/index.html` §11–12 for
the full limitations and monitoring/retraining triggers, and §10 for the human-review
rules and no-go list — the system prioritizes pages for human review and never
automatically edits, publishes, or removes content.

Built on the FlyRank ML Internship dataset — https://flyrank.ai
