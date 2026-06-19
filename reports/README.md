# Audit Reports

This directory contains sample output reports from the Forensic Reference-Integrity Auditor.

## Naming Convention

Reports are named by the input source and date:

- `adversarial-30-YYYY-MM-DD.html` — Audits of the adversarial test set
- `{first-author-year}-YYYY-MM-DD.html` — Audits of real article reference lists

## How to View

Reports are self-contained HTML files. Open them directly in any browser — no server or dependencies required.

## Reports Available

| Report | Input Source | Date | Notes |
|---|---|---|---|
| [adversarial-30-2026-06-19.html](adversarial-30-2026-06-19.html) | `test-sets/adversarial-30.md` (adversarial test set, Mode A) | 2026-06-19 | v4 baseline run. 30 refs; raw score −84 (floored to 0). 9 High, 7 Elevated, 1 Moderate, 13 Defensible. 88.2% sensitivity, 84.6% specificity vs. ground truth. See companion metrics file. |
| [adversarial-30-2026-06-19-metrics.md](adversarial-30-2026-06-19-metrics.md) | Evaluation of adversarial-30 run | 2026-06-19 | Per-heuristic accuracy, TP/FP/FN analysis, tier calibration, prompt misfire notes. |
| [madhukar-2026-2026-06-15.html](madhukar-2026-2026-06-15.html) | `IJNTR12040012.pdf` (real article, Mode B) | 2026-06-15 | Parkinson's LSVT BIG vs. Nordic Walking paper. 5 refs; score 54. 3 High (1 fabricated shadow paper, 2 real papers with mutated titles), all 5 uncited orphan/sneaked references. |
| [amarnath-2026-2026-06-15.html](amarnath-2026-2026-06-15.html) | `IJNTR12020003.pdf` (real article, Mode A) | 2026-06-15 | Ankle-sprain kinesiology-taping paper. 20 refs; raw score −25 (floored to 0). 7 High fabrications/manipulations interleaved with 12 genuine landmark papers — a citation-laundering signature. |
