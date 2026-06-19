# v4 Planned Features

This document describes the heuristics and capabilities planned for v4 of the Forensic Reference-Integrity Auditor. Each feature includes a description, implementation approach, priority assessment, and dependencies.

**Status:** Ongoing. Sneaked-reference detection (Heuristic 8), temporal impossibility checks (Heuristic 9), and full COPE flowchart alignment shipped in v5. Remaining items below are planned.

---

## New Heuristics

### Sneaked-Reference Detection

**Priority:** High (recommended first implementation)

**What it catches:** References that appear in the reference list but are never cited in the manuscript body. This is a reference-list padding technique — adding citations to inflate apparent scholarly engagement or to boost citation counts for specific papers.

**Implementation approach:**
- Requires access to the full manuscript body, not just the reference list
- Parse in-text citations from the manuscript (APA parenthetical and narrative citation patterns)
- Cross-reference parsed in-text citations against the reference list
- Flag any reference list entries that have no corresponding in-text citation
- Handle edge cases: footnote citations, table/figure source notes, supplementary material references

**Why high priority:** This is the simplest v4 heuristic to implement and test. It requires no new external data sources (just the manuscript text), the logic is straightforward string matching, and it catches a common and well-documented form of citation manipulation. It's also immediately testable against any full manuscript.

**Dependencies:** The auditor must receive the full manuscript, not just the reference list. This changes the input requirements.

---

### DOI-Independent Metadata-Mismatch Detection

**Priority:** High (evidence-driven — surfaced by real-article test runs)

**What it catches:** A real, independently verifiable publication paired with citation metadata that does not match it, on references that carry **no DOI**. Two observed sub-patterns:
- **Borrowed-title:** a genuine paper's title is copied verbatim but bolted onto fabricated authors, journal, volume, and pages — the cited *coordinates* resolve to nothing, while the *title* resolves to a different, real paper.
- **Borrowed-author / mutated-paper:** a real author set is retained but the title and topic are rewritten (e.g., the authors' real paper is on chronic low back pain; the citation rewrites it as an ankle-sprain study).

**Why this is a gap in the current prompt:** v4's Heuristic 5 (Double-Real trap) targets the same intent but is written around a **DOI anchor** — "a real, valid DOI paired with metadata from a completely different real paper." IEEE-numbered lists and much grey literature carry no DOI, so the DOI-resolution path (H1) never fires and the metadata cross-check has no anchor to start from. Both real-article audit runs on 2026-06-15 (the Madhukar/Bano `IJNTR12040012` Mode-B run and the Amarnath/Sharma `IJNTR12020003` Mode-A run) were dominated by exactly this pattern; H5 had to be stretched to cover DOI-less entries, and on the 20-reference Amarnath list it was the single most common manipulation (7 of 20 entries — real titles with fabricated authors/journals, and real authors with mutated papers, interleaved among 12 correctly cited landmark papers).

**Implementation approach:**
- Make the Stage-2 metadata cross-check DOI-independent: for every reference, run a *title-first* search and an *author+year* search as parallel verification anchors, not only a DOI resolution.
- When the cited title resolves to a real publication whose authors/journal/volume/year disagree with the citation → **borrowed-title** finding.
- When the cited authors resolve to a real publication whose title/topic disagrees → **borrowed-author** finding.
- Distinguish from legitimate truncation (a citation that merely shortens a long subtitle): require a *semantic* divergence — different topic, different journal, or different first author — not just a shortened string.

**Why high priority:** It is the empirically dominant manipulation in the real-article test corpus to date, and the current heuristic set under-detects it on DOI-less reference lists — the most common citation format in many non-biomedical and predatory venues.

**Dependencies:** Title- and author-keyed search against Crossref/PubMed/Google Scholar (already used in Stage 2). No new data source — this is a generalization of the existing Double-Real check to the no-DOI case.

---

### Temporal Impossibility Checks

**Status: Shipped in v5** ([#6](../../issues/6))

**What it catches:** Citations with dates that are logically impossible:
- Reference year predates the journal's founding year
- Reference year postdates the manuscript submission date (with ahead-of-print/preprint exception)
- Volume/issue numbers that don't exist for the stated year

**Implementation:** Shipped as Heuristic 9 in `prompts/v5-auditor.md`. Verification uses Crossref journal metadata (`api.crossref.org/journals/{ISSN}`) and the NLM Catalog. Name-change exception implemented: older journal names cited with dates valid for that period are not flagged. Risk calibration: Elevated in isolation, High when combined with any other heuristic trigger. Dedicated test set: `test-sets/temporal-impossibility.md`.

---

### Predatory Journal Flagging

**Priority:** Medium

**What it catches:** References to journals identified as predatory or questionable by established assessment methodologies (primarily Cabells Predatory Reports).

**Implementation approach:**
- Cross-reference journal titles against known predatory journal lists
- Check for Cabells classification (if accessible)
- Apply heuristic indicators: journal not indexed in PubMed/Scopus/Web of Science, no COPE membership, suspicious publisher practices (rapid peer review claims, aggressive solicitation patterns)
- Flag but do not auto-classify as High risk — some legitimate research is published in questionable venues

**Complexity notes:**
- "Predatory" is a contested term with no universal definition
- Cabells Predatory Reports is behind a paywall and may not be programmatically accessible
- Beall's List (discontinued, archived) is outdated and controversial
- The heuristic should flag for editorial awareness, not make a definitive judgment
- DOAJ (Directory of Open Access Journals) inclusion is a positive signal but not definitive

**Why medium priority:** Valuable signal but complex to implement well. The risk of false positives (legitimate open-access journals flagged as predatory) requires careful calibration. Editorial sensitivity around this topic is high.

**Dependencies:** Access to journal classification data. May require manual maintenance of a supplementary journal list.

---

### Batch-Pattern Detection

**Priority:** Medium

**What it catches:** Statistical patterns across multiple manuscripts that suggest coordinated fabrication — the same fabricated references appearing in different submissions, unusual citation clustering, or shared fabrication signatures.

**Implementation approach:**
- Maintain a database of previously audited references and their verification results
- When auditing a new manuscript, compare its reference list against the historical database
- Flag references that appear in multiple submissions with identical anomalies
- Detect citation rings (manuscripts that exclusively cite each other)
- Identify shared fabrication patterns (same homoglyph substitutions, same digit-swap patterns, same shadow-paper templates)

**Why medium priority:** High value for publishers processing many manuscripts, but requires persistent state across audit runs — a significant architectural change from the current stateless prompt-based approach. This is more of a platform feature than a prompt feature.

**Dependencies:** Persistent storage, historical audit data, statistical analysis capabilities beyond what a single prompt session can provide.

---

### Crossref Retraction API Integration

**Priority:** Medium

**What it catches:** Papers that have been retracted or have expressions of concern, using Crossref's structured retraction metadata rather than relying on web search of Retraction Watch.

**Implementation approach:**
- Query Crossref API for retraction metadata on each DOI
- Check the `update-to` field for retraction notices
- Check the `is-referenced-by` field for expressions of concern
- Cross-reference with Retraction Watch for cases not yet reflected in Crossref metadata

**Why medium priority:** The current v3 auditor already checks for retractions via web search. Direct API integration would be more reliable and faster, but it's an optimization of existing capability rather than new detection capability.

**Dependencies:** Crossref API access (free for polite use with mailto header). Pipeline architecture (Stage 2) would be the natural integration point.

---

### COPE Flowchart Alignment

**Status: Shipped in v5** ([#9](../../issues/9))

**What it does:** Structures the auditor's recommendations according to COPE (Committee on Publication Ethics) investigation flowcharts, so an editor can move from detection to action without a separate triage step.

**Implementation:** The v4 single-line COPE note has been expanded into a full structured mapping in `prompts/v5-auditor.md` (Section 3 and Section 6) and `docs/heuristics.md`. Five COPE flowcharts are mapped:
- Suspected Fabricated Data in a Submitted Manuscript
- Suspected Fabricated Data in a Published Article
- Authorship Disputes
- Suspected Ghost, Gift, or Guest Authorship
- Suspected Redundant (Duplicate) Publication

Three escalation levels are distinguished: author query, editorial investigation, and publisher/institution notification. High-risk references include the specific applicable COPE flowchart and a concrete next step. A COPE methodology reference appears in the Forensic Appendix.

---

## Architecture Features

### API-Based Orchestration

**Priority:** High (prerequisite for other features)

Move from prompt-based execution to API-based pipeline orchestration as described in `docs/architecture.md`. This enables:
- Multi-model pipeline (Haiku → Sonnet → Opus → Haiku)
- Cost optimization through model tiering
- Batch processing of multiple manuscripts
- Persistent state for batch-pattern detection

### Token Budget Management

**Priority:** Medium

Implement token consumption tracking and optimization:
- Per-stage token metering
- Adaptive escalation thresholds (reduce Opus usage when budget is constrained)
- Cost-per-audit reporting
- Budget alerts for editorial teams

### Report Template System

**Priority:** Low

Make the HTML report output customizable:
- Journal-specific branding and formatting preferences
- Configurable report sections (some editors may not need all six sections)
- Export formats beyond HTML (PDF, structured JSON for integration with editorial management systems)
- JATS XML output for direct integration with journal production workflows

---

## Prioritized Implementation Order

1. ~~**Sneaked-reference detection**~~ — Shipped as Heuristic 8 in v4.
2. **DOI-independent metadata-mismatch detection** — Empirically the dominant manipulation in real-article tests; generalizes the Double-Real check to DOI-less references with no new data source.
3. ~~**Temporal impossibility checks**~~ — Shipped as Heuristic 9 in v5.
4. **API-based orchestration** — Prerequisite for scaling and for batch-pattern detection.
5. **Crossref retraction API integration** — Optimization of existing capability.
6. **Predatory journal flagging** — Valuable but requires careful calibration.
7. **Batch-pattern detection** — High value but requires persistent state infrastructure.
8. ~~**COPE flowchart alignment**~~ — Shipped in v5 (fully mapped, five flowcharts, three escalation levels).
9. **Token budget management** — Operational optimization.
10. **Report template system** — Customization for publisher adoption.
