# CULPA Review Classifier

Zero-shot classification of Columbia course reviews along dimensions students actually care about — workload, difficulty, grading fairness, lecture quality — using natural-language labels instead of a trained classifier.

> **Status: work in progress.** Exploration notebook runs; full-corpus classification and evaluation are not finished yet. See [Roadmap](#roadmap).

## Why

CULPA reviews carry a 1–5 numeric rating and a free-text body. The number tells you whether someone liked the class; the text tells you *why*, but nobody has aggregated that. A student deciding between two sections wants "heavy reading, generous grader" — not a 3.8.

Zero-shot classification suits this well: the interesting labels aren't known in advance and there's no labeled training set, but the numeric ratings give a partial ground truth to validate against.

## Approach

1. Parse and clean the review corpus (**28,363 reviews** with non-empty text)
2. Classify each review against candidate label sets using zero-shot NLI models
3. Validate predicted sentiment against the existing numeric rating — this is the evaluation signal
4. Aggregate to per-course and per-instructor profiles

Models compared:

| Model | Notes |
|---|---|
| `facebook/bart-large-mnli` | Standard zero-shot baseline, slow |
| `knowledgator/comprehend_it-base` | Much smaller, competitive on NLI-style classification |

## Data

`data/review_sample.csv` — a random 200-review sample, provided so the notebook runs out of the box.

The full corpus is **not** committed. CULPA reviews are pseudonymous but name individual instructors, and redistributing the full scrape isn't something I want to do without checking their terms. The pipeline works on any CSV with `review` and `rating` columns.

## Running it

```bash
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
jupyter lab notebooks/01_zero_shot_exploration.ipynb
```

First run downloads model weights (~1.6GB for BART-MNLI).

## Roadmap

- [ ] Batch classification over the full corpus with checkpointing
- [ ] Validation: predicted sentiment vs. numeric rating (agreement, confusion matrix)
- [ ] Label-set ablation — which candidate labels are actually separable?
- [ ] Compare the two models on cost/latency/agreement
- [ ] Per-instructor aggregate profiles
- [ ] Streamlit demo: paste a review, see the dimension breakdown

## License

MIT — see [LICENSE](LICENSE).
