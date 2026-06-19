# Evaluation Metrics — adversarial-30 v5 Baseline Run
**Date:** 2026-06-19  
**Auditor prompt:** `prompts/v5-auditor.md`  
**Input:** `test-sets/adversarial-30.md` (clean reference list, 30 entries)  
**Mode:** A (reference list only)  
**Verification:** Live — Crossref API, PubMed, NLM Catalog, Cochrane Library, publisher sites  

---

## Ground-Truth Distribution (corrected trap index from issue #37)

| Category | Count | Refs |
|---|---|---|
| Clean (expected Defensible) | 14 | 1, 2, 4, 7, 9, 10, 12, 14, 19, 20, 23, 25, 27, 29 |
| Manipulated/Fabricated (expected flagged) | 16 | 3, 5, 6, 8, 11, 13, 15, 16, 17, 18, 21, 22, 24, 26, 28, 30 |

---

## Auditor Classifications

| Tier | Count | Refs |
|---|---|---|
| High (H) | 9 | 3, 5, 13, 15, 18, 22, 24, 28, 30 |
| Elevated (E) | 7 | 6, 8, 11, 16, 17, 21, 26 |
| Moderate (M) | 1 | 27 |
| Defensible (D) | 13 | 1, 2, 4, 7, 9, 10, 12, 14, 19, 20, 23, 25, 29 |

**Reference list score:** 0 / 100 (raw −84, floored)  
Calculation: 100 − (9 × 12) − (7 × 5) − (1 × 2) − (13 × 3) = −84

---

## Overall Detection Performance

For this analysis, "flagged" = H, E, or M tier; "cleared" = D tier.

| Metric | Value | Notes |
|---|---|---|
| **True positive rate (sensitivity)** | **16 / 16 = 100.0%** | All manipulated refs correctly flagged |
| **False positive rate** | **1 / 14 = 7.1%** | Clean refs incorrectly flagged |
| **True negative rate (specificity)** | **13 / 14 = 92.9%** | Clean refs correctly cleared |
| **False negatives** | **0 / 16 = 0%** | No manipulated refs missed |
| **Precision** | **16 / 17 = 94.1%** | Flagged refs that are truly manipulated |
| **F1 score** | **32 / 33 = 97.0%** | Harmonic mean of precision and recall |

### Confusion Matrix

|  | Ground truth: Manipulated | Ground truth: Clean |
|---|---|---|
| **Auditor: Flagged (H/E/M)** | 16 (TP) | 1 (FP) |
| **Auditor: Defensible** | 0 (FN) | 13 (TN) |

Arithmetic: TP = all 16 manipulated refs flagged (refs 3, 5, 6, 8, 11, 13, 15, 16, 17, 18, 21, 22, 24, 26, 28, 30). FP = ref 27 only (Moderate, GT=Clean; DOI retirement artifact). FN = 0. TN = 13 (all remaining Clean refs received D).

---

## Heuristic 9 — Temporal Impossibility Analysis

H9 was applied to all 30 references in this run. **H9 fired on zero references.** This is the expected result: the adversarial-30 test set contains no temporal traps.

### H9 sub-check (a) — Pre-existence date

All journal titles cited with dates plausibly postdate those journals' founding:

| Journals checked | Founded | Oldest v5 citation | Result |
|---|---|---|---|
| JOGNN (refs 3, 8, 10, 16, 17, 18, 21, 26, 27, 28) | 1972 | V42 (2013) | ✓ Plausible |
| Nursing for Women's Health (ref 22) | 2007 (renamed from AWHONN Lifelines) | V28 (2024) | ✓ Plausible |
| Reproductive Health (ref 12) | 2004 | V16 (2019) | ✓ Plausible |
| Social Science & Medicine (ref 11) | 1967 | V238 (2019) | ✓ Confirmed |
| Obstetrics & Gynecology (refs 4, 21) | 1953 | V133 (2019) | ✓ Confirmed |
| BJOG (ref 17) | ~1894 | V125 (2018) | ✓ Confirmed |
| AJOG (ref 14) | ~1920 | V210 (2014) | ✓ Confirmed |

### H9 sub-check (b) — Post-submission date

No manuscript submission date was provided in Mode A (bare reference list). This sub-check was skipped for all 30 references, as specified by the v5 prompt.

### H9 sub-check (c) — Impossible volume/issue

Volume numbers cross-checked against journal publication histories:

| Ref | Citation | Volume | Year | Plausible? |
|---|---|---|---|---|
| 3, 8, 10, 16, 17, 18, 21, 26, 27, 28 | Various JOGNN | V42–V54 | 2013–2025 | ✓ (JOGNN V1=1972; V52=2023) |
| 22 | NWH V28(2) | V28 | 2024 | ✓ (V11=2007 as NWH; V28=2024) |
| 6 | Health Affairs V32(3) | V32 | 2013 | ✓ (HA founded 1981; V32=2013 confirmed by Crossref) |
| 11 | Social Science & Medicine V238 | V238 | 2019 | ✓ (SSM published at high volume cadence; V238=2019 confirmed) |
| 4, 21 | O&G V133, V130 | V133, V130 | 2019, 2017 | ✓ (O&G publishes ~2 volumes/year; both confirmed by Crossref) |
| 14 | AJOG V210(3) | V210 | 2014 | ✓ (Confirmed by Crossref DOI resolution) |
| 17 | BJOG V125(8) | V125 | 2018 | ✓ (Confirmed; 2018 = V125 per Wiley) |

No impossible volume/issue combinations found in adversarial-30.

---

## False Negatives

None. Zero manipulated refs were missed.

---

## False Positives (Over-Flagged Clean Refs)

### Ref 27 — AWHONN position statement (ground truth: Clean/Defensible)
**Citation:** AWHONN (2018). Staffing for healthy outcomes. JOGNN 47(5), 724–726. DOI 10.1016/j.jogn.2018.07.002  
**Auditor classification:** Moderate  
**Reason flagged:** Crossref shows DOI 10.1016/j.jogn.2018.07.002 currently mapped to a retired/adjacent AWHONN statement (pages 722–723), not "Staffing for Healthy Outcomes" (pages 724–726). JOGNN DOI retirement artifact.  
**Note:** Same finding as v4 run. Conservative Moderate call; an editor would quickly confirm via AWHONN archive. See test-set footnote [b].

---

## Per-Heuristic Accuracy

| Heuristic | GT Instances | Detected | Missed | Notes |
|---|---|---|---|---|
| H1: DOI dead/unresolvable | 2 (refs 18, 24) | 2 | 0 | 100% |
| H2: Homoglyph | 3 (refs 3, 15, 30) | 3 | 0 | 100% — Unicode confirmed |
| H3: Digit-swap | 3 (refs 6, 21, 30) | 3 | 0 | 100% — Crossref confirmed: ref 6 pages 527–535; ref 21 pages 366–373 |
| H4: Author-shifting | 5 (refs 8, 16, 17, 26, 30) | 5 | 0 | 100% — all confirmed by Crossref author records |
| H5: Double-Real trap | 2 (refs 28, 30) | 2 | 0 | 100% — ref 28: Crossref resolves to Bann et al. 2025 (peer navigators) |
| H6: Journal mutation | 2 (refs 11, 24) | 2 | 0 | 100% — ref 11: "Social Science & Medicine" confirmed; ref 24: no journal "International Journal of Advanced Birth" |
| H7: Shadow paper | 4 (refs 5, 13, 18, 22) | 4 | 0 | 100% — refs 5, 13, 22 absent from PubMed and Crossref; ref 18: pop-culture author names |
| H8: Sneaked reference | 0 (Mode A) | N/A | N/A | Not applicable |
| H9: Temporal impossibility | 0 (no traps in this set) | 0/0 | 0 | H9 correctly fired on 0 refs (no temporal traps present) |

**Weighted average detection rate (confirmed GT instances):** 21 / 21 = **100%**

---

## Risk-Tier Calibration

| Ref | Ground Truth | Auditor Tier | Match? |
|---|---|---|---|
| 3 | Elevated or High | H | ✓ |
| 5 | High | H | ✓ |
| 6 | Moderate or Elevated | E | ✓ |
| 8 | Elevated | E | ✓ |
| 11 | Elevated or High | E | ✓ |
| 13 | High | H | ✓ |
| 15 | Elevated or High | H | ✓ |
| 16 | Elevated | E | ✓ |
| 17 | Elevated (H4) | E | ✓ |
| 18 | High | H | ✓ |
| 21 | Moderate or Elevated | E | ✓ |
| 22 | High | H | ✓ |
| 24 | Elevated or High | H | ✓ |
| 26 | Moderate or Elevated | E | ✓ |
| 27 | Defensible | M | ✗ (FP, conservative) |
| 28 | High | H | ✓ |
| 30 | High | H | ✓ |

**Tier calibration accuracy (correct tier or within one tier):** 16 / 17 evaluated = **94.1%**

---

## Regression Verdict: v5 vs v4 Baseline

| Metric | v4 Baseline | v5 Result | Verdict |
|---|---|---|---|
| Sensitivity | 100.0% | 100.0% | ✓ Holds |
| Specificity | 92.9% | 92.9% | ✓ Holds |
| False positive rate | 7.1% | 7.1% | ✓ No increase |
| False negatives | 0 | 0 | ✓ None |
| Precision | 94.1% | 94.1% | ✓ Holds |
| F1 | 97.0% | 97.0% | ✓ Holds |
| H9 false positives | N/A (H9 not in v4) | 0 | ✓ Clean — H9 correctly fires on nothing |

**Overall verdict: v5 matches the v4 baseline exactly.** All v4 classifications are reproduced. Heuristic 9 introduced no false positives. The one existing false positive (ref 27, Moderate) is unchanged and is a documented DOI-retirement artifact, not a prompt misfire.

v5 is a regression-safe upgrade: it extends detection to Heuristic 9 (temporal impossibility) without increasing the false-positive rate on the adversarial-30 set.

---

## H9 Misfire Check (Key Requirement from Issue #40)

> "Heuristic 9 should fire on NOTHING in this set because adversarial-30 contains no temporal traps; if v5 flags any reference that v4 classified Defensible, treat that as a Heuristic 9 false positive and document it."

H9 fired on **zero** references in this run. No reference that v4 classified Defensible was changed to a higher tier. No H9 false positive occurred.

---

## Notes on v5-Specific Additions

### H9 Integration
H9 was evaluated for all 30 references across all three sub-checks. The adversarial-30 set was designed before H9 existed; none of its entries were engineered to probe temporal impossibility. The sub-check that v5 most exposed to false-positive risk is sub-check (c) (impossible volume/issue), since the test set cites several journals with large volume numbers. Live Crossref API lookups confirmed all cited volume/issue combinations are historically plausible.

### COPE Alignment (v5 addition)
v5 adds explicit COPE flowchart recommendations for each H- and E-tier finding. These are included in the Section 3 ranked suspicion index of the HTML report and the Section 6 appendix COPE table.

---

## Summary

Against the corrected trap index (14 Clean, 16 Manipulated), the v5 auditor achieved **100% sensitivity** and **92.9% specificity** on this adversarial-30 run with live web verification — matching the v4 baseline exactly. All seven applicable heuristic categories (H1–H7) achieved 100% detection. Heuristic 9 correctly produced zero findings (no temporal traps in this set). The sole false positive remains ref 27 (Moderate, JOGNN DOI retirement artifact). v5 is confirmed as a safe production upgrade.
