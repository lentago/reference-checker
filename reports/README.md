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
| [adversarial-30-v5-2026-06-19.html](adversarial-30-v5-2026-06-19.html) | `test-sets/adversarial-30.md` (adversarial test set, Mode A) | 2026-06-19 | **v5 baseline run.** 30 refs; raw score −84 (floored to 0). 9 High, 7 Elevated, 1 Moderate, 13 Defensible. 100% sensitivity, 92.9% specificity vs. corrected trap index. H9 fired on 0 refs (no temporal traps in set). Matches v4 baseline exactly — regression-safe. See companion metrics file. |
| [adversarial-30-v5-2026-06-19-metrics.md](adversarial-30-v5-2026-06-19-metrics.md) | Evaluation of adversarial-30 v5 run | 2026-06-19 | Per-heuristic accuracy, TP/FP/FN analysis, tier calibration, H9 null-result analysis, v5 vs. v4 regression verdict. |
| [temporal-impossibility-v5-2026-06-19.html](temporal-impossibility-v5-2026-06-19.html) | `test-sets/temporal-impossibility.md` (H9 test set, Mode A, two runs) | 2026-06-19 | **v5 H9 functional check.** 4 refs; two runs (bare + submission-date prefix). All 3 temporal traps flagged High; CDC control Defensible. H9 all sub-checks verified: (a) pre-existence (ref 1 NWH 2004), (b) post-submission (ref 4 2025 paper), (c) impossible volume (ref 2 JOGNN V61). H9 PASS. |
| [adversarial-30-2026-06-19.html](adversarial-30-2026-06-19.html) | `test-sets/adversarial-30.md` (adversarial test set, Mode A) | 2026-06-19 | v4 baseline run. 30 refs; raw score −84 (floored to 0). 9 High, 7 Elevated, 1 Moderate, 13 Defensible. 88.2% sensitivity, 84.6% specificity vs. ground truth. See companion metrics file. |
| [adversarial-30-2026-06-19-metrics.md](adversarial-30-2026-06-19-metrics.md) | Evaluation of adversarial-30 v4 run | 2026-06-19 | Per-heuristic accuracy, TP/FP/FN analysis, tier calibration, prompt misfire notes. Corrected from original flawed-GT figures per issue #37; superseded as production baseline by v5 run above. |
| [madhukar-2026-2026-06-15.html](madhukar-2026-2026-06-15.html) | `IJNTR12040012.pdf` (real article, Mode B) | 2026-06-15 | Parkinson's LSVT BIG vs. Nordic Walking paper. 5 refs; score 54. 3 High (1 fabricated shadow paper, 2 real papers with mutated titles), all 5 uncited orphan/sneaked references. |
| [amarnath-2026-2026-06-15.html](amarnath-2026-2026-06-15.html) | `IJNTR12020003.pdf` (real article, Mode A) | 2026-06-15 | Ankle-sprain kinesiology-taping paper. 20 refs; raw score −25 (floored to 0). 7 High fabrications/manipulations interleaved with 12 genuine landmark papers — a citation-laundering signature. |
