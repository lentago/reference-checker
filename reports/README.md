# Audit Reports

This directory contains sample output reports from the Forensic Reference-Integrity Auditor.

## Naming Convention

Reports are named by the input source and date:

- `adversarial-30-YYYY-MM-DD.html` — Audits of the adversarial test set
- `predatory-venues-YYYY-MM-DD.html` — Audits of the predatory-venues H10 test set
- `{first-author-year}-YYYY-MM-DD.html` — Audits of real article reference lists

Versioned runs include a `v<N>` marker before the date (e.g., `adversarial-30-v6-2026-06-19.html`).

## How to View

Reports are self-contained HTML files. Open them directly in any browser — no server or dependencies required.

## Reports Available

| Report | Input Source | Date | Notes |
|---|---|---|---|
| [adversarial-30-v6-2026-06-19.html](adversarial-30-v6-2026-06-19.html) | `test-sets/adversarial-30.md` (adversarial test set, Mode A) | 2026-06-19 | **v6 regression gate.** 30 refs; raw score −45 (floored to 0). 9 High, 7 Elevated, 1 Moderate, 13 Defensible. 100% sensitivity, 92.9% specificity — identical to v5. H9: 0 fires. H10: 0 fires (no predatory venues in set). v6 is regression-safe. See companion metrics file. |
| [adversarial-30-v6-2026-06-19-metrics.md](adversarial-30-v6-2026-06-19-metrics.md) | Evaluation of adversarial-30 v6 run | 2026-06-19 | Per-heuristic accuracy, TP/FP/FN analysis, H10 misfire check (0 fires), H9 misfire check (0 fires), v6 vs. v5 regression comparison, tier calibration table. |
| [predatory-venues-v6-2026-06-19.html](predatory-venues-v6-2026-06-19.html) | `test-sets/predatory-venues.md` (H10 test set, Mode A) | 2026-06-19 | **v6 H10 functional check.** 5 refs; score 78. Ref 1: Elevated (H10 only). Ref 2: Defensible (clean control — IJNP, indexed). Ref 3: High (H10+H7). Ref 4: Elevated (H10 only). Ref 5: Defensible (clean control — Lancet Global Health, indexed). H10 PASS. |
| [patriksson-2024-v6-2026-06-19.html](patriksson-2024-v6-2026-06-19.html) | `test-sets/real-articles/patriksson-2024.md` (real article, Mode A) | 2026-06-19 | **v6 scoring calibration — clean article.** 26 refs; score 96 (vs. v5 ~22). 2 Moderate (deposit errors, not integrity concerns), 24 Defensible. H10: 0 fires on all 26 journals. Confirms v6 formula correctly scores near-clean article near top of scale. |
| [ahmadinezhad-2024-v6-2026-06-19.html](ahmadinezhad-2024-v6-2026-06-19.html) | `test-sets/real-articles/ahmadinezhad-2024.md` (real article, Mode A) | 2026-06-19 | **v6 scoring calibration — article with genuine finding.** 30 refs; score 83 (vs. v5 ~5). 1 High (Ref 6 — H3+H7+H9, impossible volume/shadow paper, COPE investigation warranted), 1 Elevated (Ref 25 — H10, Journal of Human Sciences unindexed), 28 Defensible. H10 fired on 1 ref only; 0 false positives on indexed journals. |
| [adversarial-30-v5-2026-06-19.html](adversarial-30-v5-2026-06-19.html) | `test-sets/adversarial-30.md` (adversarial test set, Mode A) | 2026-06-19 | **v5 baseline run.** 30 refs; raw score −84 (floored to 0). 9 High, 7 Elevated, 1 Moderate, 13 Defensible. 100% sensitivity, 92.9% specificity vs. corrected trap index. H9 fired on 0 refs (no temporal traps in set). Matches v4 baseline exactly — regression-safe. See companion metrics file. |
| [adversarial-30-v5-2026-06-19-metrics.md](adversarial-30-v5-2026-06-19-metrics.md) | Evaluation of adversarial-30 v5 run | 2026-06-19 | Per-heuristic accuracy, TP/FP/FN analysis, tier calibration, H9 null-result analysis, v5 vs. v4 regression verdict. |
| [temporal-impossibility-v5-2026-06-19.html](temporal-impossibility-v5-2026-06-19.html) | `test-sets/temporal-impossibility.md` (H9 test set, Mode A, two runs) | 2026-06-19 | **v5 H9 functional check.** 4 refs; two runs (bare + submission-date prefix). All 3 temporal traps flagged High; CDC control Defensible. H9 all sub-checks verified: (a) pre-existence (ref 1 NWH 2004), (b) post-submission (ref 4 2025 paper), (c) impossible volume (ref 2 JOGNN V61). H9 PASS. |
| [adversarial-30-2026-06-19.html](adversarial-30-2026-06-19.html) | `test-sets/adversarial-30.md` (adversarial test set, Mode A) | 2026-06-19 | v4 baseline run. 30 refs; raw score −84 (floored to 0). 9 High, 7 Elevated, 1 Moderate, 13 Defensible. 88.2% sensitivity, 84.6% specificity vs. ground truth. See companion metrics file. |
| [adversarial-30-2026-06-19-metrics.md](adversarial-30-2026-06-19-metrics.md) | Evaluation of adversarial-30 v4 run | 2026-06-19 | Per-heuristic accuracy, TP/FP/FN analysis, tier calibration, prompt misfire notes. Corrected from original flawed-GT figures per issue #37; superseded as production baseline by v5 run above. |
| [madhukar-2026-2026-06-15.html](madhukar-2026-2026-06-15.html) | `IJNTR12040012.pdf` (real article, Mode B) | 2026-06-15 | Parkinson's LSVT BIG vs. Nordic Walking paper. 5 refs; score 54. 3 High (1 fabricated shadow paper, 2 real papers with mutated titles), all 5 uncited orphan/sneaked references. |
| [amarnath-2026-2026-06-15.html](amarnath-2026-2026-06-15.html) | `IJNTR12020003.pdf` (real article, Mode A) | 2026-06-15 | Ankle-sprain kinesiology-taping paper. 20 refs; raw score −25 (floored to 0). 7 High fabrications/manipulations interleaved with 12 genuine landmark papers — a citation-laundering signature. |
