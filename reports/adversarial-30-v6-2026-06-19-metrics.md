# Evaluation Metrics — adversarial-30 v6 Baseline Run
**Date:** 2026-06-19  
**Auditor prompt:** `prompts/v6-auditor.md`  
**Input:** `test-sets/adversarial-30.md` (clean reference list, 30 entries — trap index stripped)  
**Mode:** A (reference list only)  
**Verification:** Live — Crossref API, PubMed, Cochrane Library, publisher sites (Elsevier, Wiley), Unicode inspection, DOAJ, Scopus (for H10), Web of Science (for H10)

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

**Reference list score (v6 formula):** 0 / 100 (raw −45, floored)  
Calculation: 100 − (9 × 12) − (7 × 5) − (1 × 2) = 100 − 108 − 35 − 2 = −45

**Note on score vs. v5:** The v5 raw score was −84 (D × 3 = 13 × 3 = 39 additional deduction). v6 removes the D × 3 term, producing −45. Both floor to 0. The tier assignments — not the raw scores — are the correct regression metric.

---

## Overall Detection Performance

For this analysis, "flagged" = H, E, or M tier; "cleared" = D tier.

| Metric | Value | Notes |
|---|---|---|
| **True positive rate (sensitivity)** | **16 / 16 = 100.0%** | All manipulated refs correctly flagged |
| **False positive rate** | **1 / 14 = 7.1%** | Ref 27 (M, DOI artifact) — same as v5 |
| **True negative rate (specificity)** | **13 / 14 = 92.9%** | 13 clean refs correctly cleared |
| **False negatives** | **0 / 16 = 0%** | No manipulated refs missed |
| **Precision** | **16 / 17 = 94.1%** | Flagged refs that are truly manipulated |
| **F1 score** | **32 / 33 = 97.0%** | Harmonic mean of precision and recall |

### Confusion Matrix

|  | Ground truth: Manipulated | Ground truth: Clean |
|---|---|---|
| **Auditor: Flagged (H/E/M)** | 16 (TP) | 1 (FP) |
| **Auditor: Defensible** | 0 (FN) | 13 (TN) |

Arithmetic: TP = all 16 manipulated refs flagged (3, 5, 6, 8, 11, 13, 15, 16, 17, 18, 21, 22, 24, 26, 28, 30). FP = ref 27 only (Moderate, GT=Clean; DOI retirement artifact). FN = 0. TN = 13 (all remaining Clean refs received D).

---

## Heuristic 10 — Journal Legitimacy Check (v6 New)

H10 was applied to all 30 references. **H10 fired on zero references.** This is the expected result for the adversarial-30 set.

### H10 verification detail

All cited journals in adversarial-30 are legitimate, indexed journals or grey literature:

| Journal (refs) | DOAJ | PubMed | Scopus | WoS | H10 result |
|---|---|---|---|---|---|
| JOGNN (3, 8, 10, 16, 17, 18, 21, 26, 27, 28) | ❌* | ✅ | ✅ | ✅ | Clears (subscription journal, Scopus/WoS exempt) |
| Social Science & Medicine (11, actual journal via DOI) | ❌* | ✅ | ✅ | ✅ | Clears |
| Health Affairs (6) | ❌* | ✅ | ✅ | ✅ | Clears |
| Cochrane Database (8, 9, 10, 16, 19, 26) | ✅ | ✅ | ✅ | ✅ | Clears |
| Reproductive Health (12) | ✅ | ✅ | ✅ | ✅ | Clears |
| MCN (13) | ❌* | ✅ | ✅ | ✅ | Clears |
| AJOG (14) | ❌* | ✅ | ✅ | ✅ | Clears |
| Journal of Perinatal Education (15, 30) | ❌* | ✅ | ✅ | ✅ | Clears |
| Obstetrics & Gynecology (4, 21) | ❌* | ✅ | ✅ | ✅ | Clears |
| BJOG (17) | ❌* | ✅ | ✅ | ✅ | Clears |
| Nursing for Women's Health (22) | ❌* | ✅ | ✅ | ✅ | Clears |
| MMWR (23) | N/A (grey lit) | N/A | N/A | N/A | Exempt |
| Birth / "Int. J. Advanced Birth" (24 — actual journal via DOI) | ❌* | ✅ | ✅ | ✅ | Clears |
| Clinical Obstetrics and Gynecology (20) | ❌* | ✅ | ✅ | ✅ | Clears |
| Grey lit (2, 7, 25, 29, 1, 29) | N/A | N/A | N/A | N/A | Exempt |

\* Subscription journals not indexed in DOAJ by design; v6 exemption applies: "Established subscription journals indexed in Scopus or Web of Science are not flagged even if absent from DOAJ."

**Critical note for journal-mutation references (Refs 11, 24):** The mutated journal names ("Social Science & Contemporary Medicine," "International Journal of Advanced Birth") are not indexed anywhere, as they are fabricated names. H10 verification correctly used the actual journal revealed by DOI resolution — Social Science & Medicine and Birth respectively (both indexed). This is the correct forensic approach: H10 assesses the journal's legitimacy, not a deliberate falsification of a journal name. H6 (journal mutation) captures the falsification; H10 should not fire because the underlying real journal is indexed.

---

## Heuristic 9 — Temporal Impossibility Check

H9 was applied to all 30 references. **H9 fired on zero references.** This is the expected result — adversarial-30 contains no temporal traps.

- All cited journals predate their oldest citations by wide margins (JOGNN V1=1972; oldest JOGNN citation = V42/2013 ✓)
- No manuscript submission date provided (Mode A) → sub-check (b) skipped
- All volume/issue numbers plausible for stated years (confirmed by Crossref for DOI-bearing refs)

---

## Per-Heuristic Accuracy

| Heuristic | GT Instances | Detected | Missed | Notes |
|---|---|---|---|---|
| H1: DOI dead/wrong | 3 (refs 18, 24, 28) | 3 | 0 | 100% |
| H2: Homoglyph | 3 (refs 3, 15, 30) | 3 | 0 | 100% — Unicode confirmed |
| H3: Digit-swap | 3 (refs 6, 21, 30) | 3 | 0 | 100% — Crossref confirmed: ref 6 pages 527–535; ref 21 pages 366–373 |
| H4: Author-shifting | 5 (refs 8, 16, 17, 26, 30) | 5 | 0 | 100% — all confirmed by Crossref author records |
| H5: Double-Real trap | 2 (refs 28, 30) | 2 | 0 | 100% — ref 28: DOI → Bann et al. 2025 (peer navigators) |
| H6: Journal mutation | 2 (refs 11, 24) | 2 | 0 | 100% — ref 11: "Social Science & Medicine" confirmed; ref 24: "Birth" confirmed |
| H7: Shadow paper | 4 (refs 5, 13, 18, 22) | 4 | 0 | 100% — refs 5, 13, 22 absent from PubMed and Crossref; ref 18: pop-culture authors |
| H8: Sneaked reference | 0 (Mode A) | N/A | N/A | Not applicable |
| H9: Temporal impossibility | 0 (no traps in this set) | 0/0 | 0 | H9 correctly fires on 0 refs |
| H10: Journal legitimacy | 0 (no unindexed venues) | 0/0 | 0 | H10 correctly fires on 0 refs |

**Weighted average detection rate (confirmed GT instances):** 22 / 22 = **100%** (21 from H1–H7, 0 expected for H9 and H10)

---

## Risk-Tier Calibration

| Ref | Ground Truth | v5 Tier | v6 Tier | Match (v5→v6)? |
|---|---|---|---|---|
| 3 | Elevated or High | H | H | ✓ Identical |
| 5 | High | H | H | ✓ Identical |
| 6 | Moderate or Elevated | E | E | ✓ Identical |
| 8 | Elevated | E | E | ✓ Identical |
| 11 | Elevated or High | E | E | ✓ Identical |
| 13 | High | H | H | ✓ Identical |
| 15 | Elevated or High | H | H | ✓ Identical |
| 16 | Elevated | E | E | ✓ Identical |
| 17 | Elevated (H4) | E | E | ✓ Identical |
| 18 | High | H | H | ✓ Identical |
| 21 | Moderate or Elevated | E | E | ✓ Identical |
| 22 | High | H | H | ✓ Identical |
| 24 | Elevated or High | H | H | ✓ Identical |
| 26 | Moderate or Elevated | E | E | ✓ Identical |
| 27 | Defensible | M | M | ✓ Identical (FP, documented artifact) |
| 28 | High | H | H | ✓ Identical |
| 30 | High | H | H | ✓ Identical |

**All 17 evaluated entries: tier assignments identical between v5 and v6.** Zero tier regressions.

---

## False Negatives

None. Zero manipulated refs were missed.

---

## False Positives (Over-Flagged Clean Refs)

### Ref 27 — AWHONN position statement (ground truth: Clean/Defensible)
**Citation:** AWHONN (2018). Staffing for healthy outcomes. JOGNN 47(5), 724–726. DOI 10.1016/j.jogn.2018.07.002  
**Auditor classification:** Moderate (unchanged from v5)  
**Reason flagged:** DOI currently maps to a different AWHONN statement (pages 722–723). JOGNN DOI retirement artifact.  
**Note:** Same finding as v4 and v5. H10 correctly does not fire (JOGNN is indexed). The Moderate classification is conservative and unchanged.

---

## H10 Misfire Check (Key Requirement from Issue #45)

> "Heuristic 10 should fire on NOTHING in this set because adversarial-30 contains no predatory or unindexed venues; if v6 raises an H10 flag on any reference that v5 classified Defensible, treat it as a false positive and document it."

H10 fired on **zero** references in this run. No reference that v5 classified Defensible was changed to a higher tier. No H10 false positive occurred.

**Additional note:** H10 was also checked for the journal-mutation references (Refs 11 and 24), where the mutated journal names are technically absent from all databases. The auditor correctly applied H10 to the actual journals revealed by DOI resolution (Social Science & Medicine and Birth, respectively), not to the fabricated names. This is the correct approach: H10 targets unindexed journals, not deliberately falsified journal names that already trigger H6.

---

## v6 vs. v5 Regression Verdict

| Metric | v5 Baseline | v6 Result | Verdict |
|---|---|---|---|
| Sensitivity | 100.0% | 100.0% | ✓ Holds |
| Specificity | 92.9% | 92.9% | ✓ Holds |
| False positive rate | 7.1% | 7.1% | ✓ No increase |
| False negatives | 0 | 0 | ✓ None |
| Precision | 94.1% | 94.1% | ✓ Holds |
| F1 | 97.0% | 97.0% | ✓ Holds |
| H10 false positives | N/A (H10 not in v5) | 0 | ✓ Clean — H10 fires on nothing |
| H9 false positives | 0 | 0 | ✓ Unchanged |
| Tier assignments | 9H / 7E / 1M / 13D | 9H / 7E / 1M / 13D | ✓ Identical |

**Overall verdict: v6 matches the v5 baseline exactly at the tier level.** All v5 classifications are reproduced. Heuristic 10 introduced no false positives. The v6 scoring formula change (removal of D × 3) produces a different raw score (−45 vs. −84) but both floor to 0 — the score comparison point is moot for this heavily-fabricated set.

v6 is a regression-safe upgrade: it extends detection to Heuristic 10 (journal legitimacy) without increasing the false-positive rate on the adversarial-30 set.

---

## Notes on v6-Specific Additions vs. v5

### H10 Integration
H10 was evaluated for all 30 references across all cited journals. The adversarial-30 set was designed before H10 existed and contains no unindexed-venue traps. All cited journals are legitimate indexed publications or grey literature. H10 produced zero findings — correct behavior.

### Scoring Formula Change
v6 removed the D × 3 base cost. This changes the raw score but not the floored score (both 0) for this heavily-fabricated set. The change becomes meaningful for clean or near-clean reference lists (see RUN 3: Patriksson and Ahmadinezhad runs).

### COPE Alignment (unchanged from v5)
All COPE alignment elements from v5 are preserved in v6. Recommendations map to the same flowcharts.

---

## Summary

Against the corrected trap index (14 Clean, 16 Manipulated), the v6 auditor achieved **100% sensitivity** and **92.9% specificity** on this adversarial-30 run with live web verification — matching the v5 baseline exactly. All seven applicable heuristic categories (H1–H7) achieved 100% detection. Heuristic 9 and Heuristic 10 both correctly produced zero findings (no temporal or predatory-venue traps in this set). The sole false positive remains Ref 27 (Moderate, JOGNN DOI retirement artifact). v6 is confirmed as a regression-safe upgrade.
