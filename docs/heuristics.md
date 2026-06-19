# Forensic Heuristics — Detailed Documentation

This document describes each forensic heuristic used by the Reference-Integrity Auditor, including what it detects, how it works, known limitations, and examples from testing.

All heuristics are applied per-reference during the verification pass. A single reference can trigger multiple heuristics simultaneously — and frequently does in adversarial test sets.

---

## 1. DOI Resolution

**What it catches:** Dead DOIs, DOIs that resolve to a different paper than described, fabricated DOI patterns that follow the `10.xxxx/` convention but point nowhere.

**How it works:** The auditor resolves each DOI against Crossref. If the DOI is present, three outcomes are possible:
- **Resolves correctly** — The Crossref metadata matches the supplied citation. No flag.
- **Resolves to wrong paper** — The DOI is real but belongs to a different publication. This is a strong fabrication signal (see Heuristic 5, Double-Real Trap).
- **Does not resolve** — The DOI returns no Crossref record. Could indicate a fabricated DOI, a typo, or a DOI from a registry Crossref doesn't index (DataCite, etc.).

**Limitations:**
- Some legitimate DOIs are registered through DataCite (datasets, code) rather than Crossref.
- Very recently published papers may have DOIs that haven't propagated to Crossref yet.
- Book chapter DOIs sometimes resolve to the parent volume rather than the specific chapter.

**Grey-literature note:** Government reports, organizational guidelines, and web resources typically lack DOIs. The absence of a DOI is not itself a flag — only the presence of a DOI that fails to resolve triggers this heuristic.

---

## 2. Homoglyph Detection

**What it catches:** Unicode character substitutions designed to defeat automated string matching. The most common pattern is Cyrillic-to-Latin substitution (Cyrillic `а` for Latin `a`, Cyrillic `о` for Latin `o`, etc.), but the heuristic also covers other confusable character sets.

**How it works:** The auditor computes a suspicious-character ratio for each reference's title, author names, and journal name. Characters are checked against Unicode confusables tables. A reference with a ratio above threshold is flagged.

**Why it matters:** Homoglyph substitution is a paper mill technique. A fabricated reference with a Cyrillic `е` in the journal title will pass a visual inspection but fail an exact-match lookup against Crossref or PubMed, making the fabrication harder to catch by automated tools that rely on string matching — while still looking correct to a human reviewer.

**Limitations:**
- Author names from non-Latin-script languages may contain legitimate Unicode characters that trigger false positives if the threshold is set too aggressively.
- Some legitimate journals have titles that include non-ASCII characters by design.
- The heuristic catches the substitution but cannot determine intent — a genuine encoding error looks identical to deliberate manipulation.

**Test evidence:** The adversarial test set includes references with Cyrillic `а` and `о` substituted into journal titles of real nursing journals. The auditor successfully flagged these while passing clean references with identical journal names in standard Latin characters.

---

## 3. Digit-Swap Analysis

**What it catches:** Transposed or altered volume numbers, issue numbers, page ranges, or article numbers that make an otherwise real citation unfindable through standard lookup.

**How it works:** When Crossref or PubMed metadata is available for a reference, the auditor computes the Levenshtein distance between the supplied numeric tokens (volume, issue, pages) and the authoritative values. A distance of 1 (single transposition or substitution) is flagged as a probable digit-swap; a distance >1 is flagged as a more significant discrepancy.

**Why it matters:** Digit-swapping is a low-effort manipulation technique that creates plausible deniability ("it was a typo"). A reference to volume 48 instead of volume 84 looks like carelessness, but it makes the citation unverifiable and may indicate that the author never consulted the original source.

**Limitations:**
- Genuine typos are indistinguishable from deliberate manipulation at the single-reference level. The heuristic flags both; editorial judgment determines which is more likely.
- Online-first articles with changing page numbers may show legitimate discrepancies between the supplied citation and the Crossref record.
- Some journals have both a volume/issue system and an article-number system, and authors sometimes mix them.

---

## 4. Author-Shifting

**What it catches:** Author lists that have been subtly manipulated compared to the actual publication record — names added, removed, reordered, or replaced.

**How it works:** The auditor compares the supplied author list against the authoritative Crossref or PubMed record. Discrepancies are categorized:
- **Author added** — A name appears in the supplied reference that is not in the authoritative record.
- **Author removed** — A name from the authoritative record is missing from the supplied reference.
- **Author reordered** — The same names appear but in a different sequence.
- **Author substituted** — A name in the supplied reference replaces a different name from the authoritative record (may indicate name confusion or deliberate substitution).

**Why it matters:** Author-shifting serves multiple purposes in academic fraud:
- Adding a fabricated co-author to make the reference appear connected to a different research group.
- Removing authors to obscure the true provenance of a paper.
- Reordering to change the apparent first/last author (significant in academic credit conventions).

**Limitations:**
- Author name variations (initials vs. full names, transliterated names, hyphenation differences, maiden vs. married names) create legitimate discrepancies that are not manipulations.
- Large collaborative papers (50+ authors) may have author lists that differ between Crossref and PubMed due to metadata processing differences.
- Pre-print to final publication author list changes are legitimate.

---

## 5. Double-Real Trap

**What it catches:** A citation constructed by pairing a real DOI from one paper with metadata (title, authors, journal) from a different real paper, creating a composite that passes surface-level checks against either source individually.

**How it works:** When a DOI resolves successfully, the auditor cross-checks the resolved metadata against the supplied metadata. If the DOI resolves but the metadata matches a *different* paper, the reference is flagged as a probable Double-Real composite.

**Why it matters:** This is one of the more sophisticated fabrication techniques. Both the DOI and the metadata are independently real, so tools that check only DOI resolution (does the DOI exist?) or only metadata plausibility (does this title sound real?) will miss the mismatch. You have to check both and compare them to each other.

**Limitations:**
- Requires both Crossref metadata and sufficient supplied metadata to perform the cross-check. References with minimal metadata (no title, only DOI) cannot be fully evaluated.
- Erratum and correction notices sometimes have DOIs that resolve to related but different content.

**Test evidence:** The adversarial test set includes multiple Double-Real composites pairing DOIs from high-profile nursing research papers with metadata from different papers in the same journal. The auditor detected the mismatch by comparing Crossref-returned titles against supplied titles.

---

## 6. Journal Mutation

**What it catches:** Journal titles that have been subtly altered — word substitutions, abbreviation changes, added/removed qualifiers — that point to nonexistent journals or to different journals than intended.

**How it works:** The auditor compares the supplied journal title against known journal title registries (Crossref, PubMed/NLM catalog, ISSN portal). String similarity is computed; titles below a 95% match threshold are flagged for review.

**Common mutation patterns:**
- Word substitution: "Journal of Obstetric, Gynecologic & Neonatal Nursing" → "Journal of Obstetric, Gynecologic & Perinatal Nursing"
- Abbreviation manipulation: "JOGNN" → "J Obstet Gynecol Neonat Nurs" (plausible but non-standard abbreviation)
- Qualifier addition: "International Journal of Nursing Studies" → "International Journal of Advanced Nursing Studies"
- Scope change: "Nursing for Women's Health" → "Nursing for Maternal Health"

**Why it matters:** Mutated journal titles create citations that look legitimate on casual inspection but cannot be verified against any real publication record. This is a common paper mill technique for manufacturing citations to fabricated papers.

**Limitations:**
- Journals that have changed names over time (common in academic publishing) may trigger false positives when older citations use the historical title.
- Some journals have very similar titles that are distinct publications (e.g., "Nursing Research" vs. "Research in Nursing & Health").

---

## 7. Shadow-Paper Signatures

**What it catches:** Citations with fully plausible metadata — reasonable title, real-sounding authors, appropriate journal, correct formatting — that match no known publication in any authoritative database.

**How it works:** After all other heuristics have been applied, the auditor performs an exhaustive search for the reference across Crossref, PubMed, the publisher's site, Google Scholar, and library catalogs. If no record is found anywhere, the reference is classified as a probable shadow paper.

**Why it matters:** Shadow papers are fully fabricated citations constructed to resist casual verification. Unlike crude fabrications (obviously fake journal titles, impossible dates), shadow papers use:
- Author names from real researchers in the field
- Journal titles that exist
- Volume/issue numbers that are plausible for the publication year
- Titles that sound like real research in the subject area

The only way to catch them is exhaustive verification — checking that the specific paper actually exists, not just that the components are plausible.

**Limitations:**
- Very new publications may not yet be indexed in all databases.
- Conference proceedings, dissertations, and institutional reports may be poorly indexed and appear to be shadow papers when they are legitimate.
- Publications in non-English-language journals may be harder to verify and may be misclassified.
- The classification depends on the exhaustiveness of the search. A shadow paper can only be identified with confidence after multiple authoritative sources return no results.

**Test evidence:** The adversarial test set includes shadow papers constructed using real nursing researcher names, real JOGNN volume/issue numbers, and titles modeled on actual published research in the field. The auditor correctly identified them as unverifiable after exhaustive search.

---

## 8. Sneaked Reference

**What it catches:** References that appear in the reference list but are never cited in the manuscript body. This pattern is used to inflate the apparent evidence base, misrepresent the scope of literature reviewed, or launder fabricated sources past detection by mixing them with real citations.

**Availability:** Mode B (full manuscript input) only. When the auditor receives only a reference list (Mode A), this heuristic is skipped and noted as unavailable in the report.

**How it works:** During Stage 0 input detection, the auditor builds an in-text citation index by scanning the manuscript body for all parenthetical Author-Year pairs. After normalization (handling variants like "Smith et al., 2022" and "Smith and colleagues, 2022"), each reference list entry is cross-referenced against this index. Any reference with no matching in-text citation becomes an orphan candidate.

Orphan references are assigned **Elevated** risk by default. If the orphan also triggers any other heuristic (e.g., it is also a shadow paper or has a homoglyph), the combination escalates to **High** risk — a sneaked reference that is also fabricated is the highest-risk finding the auditor can return.

**Why it matters:** A fabricated reference buried among 30 legitimate, well-cited sources is much harder to spot than an isolated suspicious entry. Sneaking it in without any in-text citation is the insertion mechanism. Once detected, the citation pattern (real sources cited, suspicious one never referenced) becomes itself an integrity signal.

**Limitations:**
- Citations appearing only in tables, figures, or appendices may not be included in the pasted manuscript text and will appear as orphans even though they are legitimately cited. The auditor notes this caveat and recommends the editor verify against the full submitted file.
- Citation forms other than APA parenthetical (e.g., numbered footnotes, endnotes) may not be caught by the parser. The Stage 0 extraction summary notes the citation style identified.
- A reference cited only in an abstract that was not included in the pasted text will also appear as an orphan.

**Test evidence:** Not yet validated against the adversarial test set (Mode B / full-manuscript testing in progress). Validated structurally against two real-article audit runs that included manuscript body text.

---

## 9. Temporal Impossibility

**What it catches:** Citations whose stated dates are chronologically impossible given the journal's founding year, the manuscript's submission timeline, or the journal's volume and issue history for the stated publication year.

**How it works:** The auditor applies three temporal sub-checks during Stage 2 verification:

- **Pre-existence date:** The auditor retrieves the journal's founding year from Crossref journal metadata (`api.crossref.org/journals/{ISSN}`) and the NLM Catalog. If the reference year predates the journal's first published issue, the reference is flagged. Exception: if the journal changed its name, the auditor verifies whether the cited name was the active title in the cited year — an older name used with a date valid for that period is not a temporal anomaly.

- **Post-submission date:** If the manuscript provides a submission date (in a cover page, header, or metadata block), the auditor compares every reference year against it. References dated after the submission date are flagged unless they can be confirmed as ahead-of-print or preprint publications (arXiv, bioRxiv, medRxiv, or equivalent). This sub-check is skipped when no submission date is present.

- **Impossible volume/issue:** The auditor cross-checks cited volume and issue numbers against the journal's publication history for the stated year. Crossref journal metadata and the publisher's archive are the primary sources. A cited volume or issue that could not have existed in that year is flagged (e.g., citing Volume 58 of a journal that was on Volume 53 in the cited year, or citing Issue 4 of a journal that published only two issues that year).

**Why it matters:** Temporal impossibility is among the most unambiguous fabrication signals in forensic citation analysis. A paper purportedly published in a journal that did not yet exist requires no further inference — it is definitively impossible. Similarly, a future-dated citation in a submitted manuscript (with no preprint provenance) cannot reflect a source the author consulted. These signals require no judgment about intent; they are structural impossibilities.

**Risk calibration:** Elevated in isolation. Escalates to High when combined with any other heuristic trigger on the same reference.

**Limitations:**
- Crossref and NLM Catalog coverage of journal founding dates is comprehensive for major biomedical journals but may be incomplete for smaller, newer, or recently renamed journals. When founding-date data is unavailable, the sub-check is noted as inconclusive rather than a non-finding.
- Journal name changes are a common source of false positives. The historical title of a renamed journal may not be immediately apparent from Crossref metadata alone; the NLM Catalog's title history records are the authoritative source for confirming active title periods.
- Ahead-of-print and preprint exemptions depend on confirming the specific paper's preprint status — a general preprint exception does not apply unless the individual reference can be traced to a preprint repository.
- Volume/issue history gaps (years with incomplete Crossref coverage) should be noted as inconclusive, not as a temporal impossibility flag.

**Test evidence:** Validated against `test-sets/temporal-impossibility.md`, a three-trap dedicated test set covering pre-existence dating, impossible volume numbering, and post-submission dating.

**Source authority:** Crossref journal metadata (`api.crossref.org/journals`) is authoritative for volume/year mappings and ISSN-level metadata. NLM Catalog is authoritative for journal founding dates, title histories, and ISSN records. Publisher archives are the fallback source when Crossref coverage is incomplete.

---

## Heuristic Interaction

These heuristics are not independent — they interact and compound:

- A **Double-Real Trap** will pass DOI Resolution (the DOI is real) but fail on Author-Shifting or title mismatch when cross-checked.
- A **Shadow Paper** with a fabricated DOI will trigger both DOI Resolution failure and Shadow-Paper classification.
- A **Homoglyph** in a journal title may also trigger Journal Mutation if the substitution creates a string that doesn't match the real journal name.
- **Digit-Swap** combined with **Author-Shifting** on the same reference is a strong coordinated-fabrication signal.
- A **Sneaked Reference** (Heuristic 8) that also triggers any other heuristic escalates immediately to High risk — the combination of "never cited in the body" and "metadata anomaly" is the highest-risk finding the auditor can produce.
- **Temporal Impossibility** (Heuristic 9) in isolation is Elevated; combined with any other heuristic trigger on the same reference, it escalates to High. A pre-existence date combined with a Shadow-Paper finding (the journal didn't exist and the paper can't be found) is a near-certain fabrication.

The risk classification system accounts for these interactions. A reference triggering multiple heuristics receives a higher risk tier than one triggering a single heuristic in isolation.

---

---

## COPE Alignment

The Committee on Publication Ethics (COPE) provides standardized investigation flowcharts for editorial integrity concerns. The following table maps this auditor's heuristic findings to the applicable COPE procedure. COPE flowcharts are publicly available at publicationethics.org/guidance/Flowcharts.

### Flowchart mapping by finding type

| Finding type | Triggered heuristics | Applicable COPE flowchart |
|---|---|---|
| Fabricated or falsified citation data — submitted manuscript | H1, H3, H5, H7, H9 (and combinations) | Suspected Fabricated Data in a Submitted Manuscript |
| Fabricated or falsified citation data — published article | H1, H3, H5, H7, H9 (and combinations) | Suspected Fabricated Data in a Published Article |
| Author-list credit dispute | H4 (order or removal anomaly) | Authorship Disputes |
| Undisclosed authorship contribution (ghost/gift/guest) | H4 (addition anomaly) | Suspected Ghost, Gift, or Guest Authorship |
| Duplicate or redundant publication signals | Reference-level cross-match | Suspected Redundant (Duplicate) Publication |

### Escalation levels

COPE procedures distinguish three escalation levels, which the auditor's recommendations follow:

1. **Author query** — The first step for all H-tier and confirmed E-tier findings. Contact the corresponding author requesting original source documentation: library access records, DOI resolution screenshots, or copies of the original article. Author query is always initiated before escalating.

2. **Editorial investigation** — Convene an independent editorial review if the author query is inconclusive or if the author cannot substantiate the flagged reference. Do not rely solely on author-supplied documentation for H-tier findings.

3. **Publisher or institution notification** — For confirmed fabrication in a submitted manuscript: consider rejection and notify the publisher. For a published article: consider a correction, expression of concern, or retraction, and notify the publisher and/or the author's institution per COPE guidelines.

### When each flowchart applies

- **Suspected Fabricated Data in a Submitted Manuscript** — Use when H-tier citation findings (dead DOIs, shadow papers, Double-Real composites, temporal impossibilities) appear in a manuscript under review that has not yet been accepted or published.
- **Suspected Fabricated Data in a Published Article** — Use when the same findings appear in an already-published work and are discovered post-publication (e.g., via a post-publication audit or reader complaint).
- **Authorship Disputes** — Use when H4 (author-shifting) indicates that a legitimate contributor's name has been removed or reordered in a way that misrepresents credit.
- **Suspected Ghost, Gift, or Guest Authorship** — Use when H4 indicates that an author has been added who does not appear in the authoritative publisher record, suggesting an undisclosed contribution.
- **Suspected Redundant (Duplicate) Publication** — Use when cross-reference analysis reveals that the same substantive content has been submitted or published more than once without disclosure.

---

## Version History

| Version | Changes |
|---|---|
| **v1** | DOI resolution only |
| **v2** | Added homoglyph detection, digit-swap analysis, shadow-paper signatures |
| **v3** | Added author-shifting, Double-Real trap detection, journal mutation. Refined scoring formula. Added grey-literature handling. |
| **v4** | Added sneaked-reference detection (Heuristic 8, Mode B only). Added Mode A/B input detection stage. Added Cochrane Library and government sources to verification set. Added COPE alignment note for H-tier findings. |
| **v5** | Added temporal impossibility detection (Heuristic 9): pre-existence dating, post-submission dating, and impossible volume/issue checks. Added NLM Catalog to verification sources. Expanded COPE alignment from a single note to a full structured mapping across five flowcharts and three escalation levels. |
