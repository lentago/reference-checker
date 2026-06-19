# Real-Article Test Runs

This directory contains reference lists extracted from real published articles used to validate that the auditor correctly classifies legitimate references as Defensible without over-flagging.

Two kinds of articles are tracked:

- **Clean corpus** — genuinely clean articles from reputable journals, used to measure false-positive rate. A clean article should produce near 100% Defensible classification. Numerical scores will be low for large reference lists due to the D × 3 base cost (see [Scoring note](#scoring-note) below).
- **Dirty corpus** — articles with known fabricated or manipulated references, used to confirm detection.

## Clean Corpus (False-Positive Calibration)

| Article | Journal | Year | References | Auditor | % Defensible | Score | Report |
|---|---|---|---|---|---|---|---|
| Ahmadinezhad et al. | BMC Pregnancy Childbirth | 2024 | 30 | v5 | 83% (25/30 D) | 5 | [ahmadinezhad-2024-2026-06-19.html](../../reports/ahmadinezhad-2024-2026-06-19.html) |
| Patriksson et al. | BMC Pregnancy Childbirth | 2024 | 26 | v5 | 89% (23/26 D) | 22 | [patriksson-2024-2026-06-19.html](../../reports/patriksson-2024-2026-06-19.html) |

Provenance: Reference arrays retrieved from the Crossref API (`api.crossref.org/works/`) on 2026-06-19 for each article's DOI. See individual `.md` files for per-article provenance notes and any Crossref deposit artifacts.

**Calibration interpretation:**
- *Ahmadinezhad*: The single High flag (ref 6, Nayeri et al. 2007) is a genuine finding from the original article's reference list — the cited volume (5) is temporally impossible for 2007 in the Iranian Journal of Pediatrics (~Vol. 17 expected), and no database record exists for the article (H3 + H7 + H9). This is a calibration success: the auditor correctly identified a problematic citation that apparently passed peer review. It is not a false positive. All other 29 references verified clean.
- *Patriksson*: The single Elevated flag (ref 25, Ungerleider & Ungerleider 2019) is a known Crossref deposit artifact (deposit attached the DOI of ref 26 to ref 25); the paper was independently verified as existing in Ann Thorac Surg 108(4):978–986. No fabrication detected. The two Moderate flags (refs 3, 12) are a deposit stub DOI and a non-biomedical journal note respectively. All other 23 references fully verified.

## Dirty Corpus (Detection Validation)

| Article | Journal | Year | References | Score | Report |
|---|---|---|---|---|---|
| Amarnath & Sharma | IJNTR | 2026 | 20 | 0 (raw −25) | [amarnath-2026-2026-06-15.html](../../reports/amarnath-2026-2026-06-15.html) |
| Madhukar & Bano | IJNTR | 2026 | 5 | 54 | [madhukar-2026-2026-06-15.html](../../reports/madhukar-2026-2026-06-15.html) |

Source PDFs: `IJNTR12020003.pdf` (Amarnath) and `IJNTR12040012.pdf` (Madhukar) are committed in this directory. Both were published in a low-visibility open-access journal and contain fabricated citations; they are detection tests, not false-positive calibration.

## Pending

These articles were planned for testing but have no committed reference-list files or audit reports yet.

| Article | Journal | Year | Status |
|---|---|---|---|
| Hawkins et al. | JOGNN | 2025 | Pending — no committed artifacts |
| Dziadkowiec et al. | JOGNN | 2025 | Pending — no committed artifacts |
| Cardon & Karr | MCN | 2026 | Pending — no committed artifacts |
| Phan, Bethune, & Lathrop | NWH | 2026 | Pending — no committed artifacts |

## Adding a Test Article

1. Extract the reference list from an authoritative machine-readable source: the Crossref reference array at `api.crossref.org/works/{DOI}`, or a PubMed Central open-access full-text record.
2. Save as `[first-author-year].md`. Include a header with the article title, journal, year, DOI, and provenance source. Follow the numbered-list format of `test-sets/adversarial-30.md`.
3. Run the **v5** auditor (`prompts/v5-auditor.md`) against the reference list in Mode A.
4. Commit the HTML report as `reports/[first-author-year]-YYYY-MM-DD.html`.
5. Record the % Defensible and score in the applicable table above, and link the report.
6. If any E or H flags appear, determine whether they are false positives or genuine findings, and document the calibration interpretation.

## Scoring note

The scoring formula is `Score = 100 − (H×12) − (E×5) − (M×2) − (D×3)`. The D × 3 penalty means that numerical scores for clean articles are low even when all references are Defensible — a 30-reference clean list scores only 10 (100 − 90). The meaningful editorial signal for clean-corpus articles is the **% Defensible**, not the numerical score. The original 75–90 range in earlier versions of this README reflected small reference lists (≤8 refs) and does not apply to typical published articles with 20+ references.
