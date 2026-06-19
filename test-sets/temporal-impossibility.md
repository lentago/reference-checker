# Test Set — Temporal Impossibility

This reference list is designed to test **Heuristic 9 (Temporal Impossibility)** of the Forensic Reference-Integrity Auditor. All three fabricated entries contain temporal anomalies that should be caught by the auditor. One clean reference is included as a true-negative control.

**DO NOT use this reference list in any real manuscript.** It contains fabricated citations.

---

## Trap Index

This index is the answer key. It should NOT be provided to the auditor during testing — the auditor should detect these independently.

| Ref # | Trap Type | Expected Detection | Notes |
|---|---|---|---|
| 1 | Pre-existence date — reference predates journal founding | Elevated or High | *Nursing for Women's Health* was launched in 2007 (as a renamed successor to *AWHONN Lifelines*, which ran 1997–2006). A 2004 citation to *Nursing for Women's Health* is impossible under that title. |
| 2 | Impossible volume/issue — volume number cannot exist for stated year | Elevated or High | *Journal of Obstetric, Gynecologic & Neonatal Nursing* (JOGNN) began publication in 1972. Volume 1 = 1972. Volume 52 therefore corresponds to 2023. Citing Volume 61 in 2023 is impossible; the journal had not reached that volume. |
| 3 | Clean | Defensible | Real citation included as a negative control. |
| 4 | Post-submission date — reference year postdates the stated manuscript submission date | Elevated or High | The manuscript header states submission date 2024-03-10. The reference is dated 2025, which postdates submission. No ahead-of-print or preprint record can be confirmed for this specific fabricated citation. |

**Expected distribution:** 1 Clean/Defensible, 3 flagged (Elevated or High)

**Note on Heuristic 9 risk calibration:** Each of these entries is designed to trigger H9 in isolation (Elevated). If any entry also triggers another heuristic (e.g., the journal name or author list cannot be confirmed), escalate to High per the v5 risk calibration rules.

---

## Simulated Manuscript Submission Date

For testing Reference 4 (post-submission date check), prefix the reference list with the following line, which the auditor should parse in Stage 0:

```
Manuscript submitted: March 10, 2024
```

---

## Reference List

Paste the following into the auditor for testing. The trap index above and the manuscript submission date prefix (for H9 sub-check b) should NOT be included in a bare reference-list run.

---

1. Stiles, A. M., & Dowd, T. (2004). Neonatal abstinence syndrome: Assessment and management of the opioid-exposed neonate. *Nursing for Women's Health*, 8(5), 394–403. https://doi.org/10.1177/1091592304270047

2. Creanga, A. A., & Callaghan, W. M. (2023). Maternal mortality disparities in the United States by race and ethnicity. *Journal of Obstetric, Gynecologic & Neonatal Nursing*, 61(2), 144–157. https://doi.org/10.1016/j.jogn.2023.01.005

3. Petersen, E. E., Davis, N. L., Goodman, D., Cox, S., Syverson, C., Seed, K., Shapiro-Mendoza, C., Callaghan, W. M., & Barfield, W. (2019). Racial/ethnic disparities in pregnancy-related deaths — United States, 2007–2016. *Morbidity and Mortality Weekly Report*, 68(35), 762–765. https://doi.org/10.15585/mmwr.mm6835a3

4. Montoya, R. J., & Shen, L. K. (2025). Intrapartum fetal surveillance and adverse neonatal outcomes: A retrospective cohort analysis. *Journal of Obstetric, Gynecologic & Neonatal Nursing*, 54(3), 211–224. https://doi.org/10.1016/j.jogn.2025.03.009
