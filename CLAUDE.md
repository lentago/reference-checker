# CLAUDE.md — reference-checker

> Read [README.md](README.md) for the full project pitch, the competitive
> landscape, and the seven forensic heuristics. This file is operational
> notes for Claude: what the artifacts are, where outputs land, and the
> naming conventions to respect. Fleet-wide rules (PR workflow,
> attribution) live in `~/repos/CLAUDE.md`.

## Persona — introduce yourself

When Claude initializes in this directory, open the first response with a
brief self-introduction as **Reference-Checker Claude** — forensic citation
auditor (seven heuristics, web-search verification against Crossref / PubMed
/ Retraction Watch / publisher sites, HTML risk reports). One sentence is
plenty; don't make a meal of it.

## What this repo is

A prompt-engineered forensic verification system for academic reference
lists. The auditor is a single Claude Opus prompt that uses web search
against Crossref / PubMed / Retraction Watch / publisher sites to detect
fabricated, manipulated, and suspicious citations across seven heuristics
(DOI resolution, homoglyph substitution, digit-swap, author-shifting,
double-real trap, journal mutation, shadow-paper signatures). Output is a
self-contained HTML risk report.

The "build system" is "paste the prompt into Claude with a reference
list." There is no compile step, test runner, or package manager.

## Repository layout

| Path | Purpose |
|---|---|
| `prompts/v<N>-auditor.md` | Versioned prompt files. **v4** is current; **v3** is the previous live version, kept for diffing. New revisions ship as the next integer. |
| `docs/heuristics.md` | Per-heuristic deep dive (what it detects, how, known limitations, examples) |
| `docs/architecture.md` | Planned multi-model pipeline decomposition for editorial scale (design phase; not yet implemented) |
| `docs/competitive-landscape.md` | What existing tools do and how this differs |
| `test-sets/adversarial-30.md` | 30 deliberately-crafted bad citations covering all seven heuristics |
| `test-sets/real-articles/` | Reference lists pulled from real papers — sanity-check the false-positive rate |
| `reports/` | Sample HTML audit outputs. Naming: `adversarial-30-YYYY-MM-DD.html` (test-set runs) or `{first-author-year}-YYYY-MM-DD.html` (real-article runs). See `reports/README.md`. |
| `roadmap/v4-features.md` | Planned work for the next prompt revision |

## Conventions specific to this repo

- **Prompts are version-numbered, not branch-named.** When iterating, ship
  `prompts/v<next>-auditor.md` as a new file rather than mutating the
  current one in place. Old versions stay readable for comparison and for
  reproducing past audit runs.
- **Markdown-only edits to prompt files.** No code, no scripts to run.
  Voice / formatting changes should be grounded in observed model
  behavior (an actual audit run misfiring on something), not speculative
  improvement.
- **Reports are committed when they document a notable finding.** Routine
  audit runs aren't archived — these aren't outputs of a CI pipeline.

## Gotchas

- **Live web search is the verification surface.** The prompt assumes the
  model can hit Crossref / PubMed / publisher sites at run time. Without
  web search the auditor can't verify anything and falls back to
  pattern-only heuristics. Don't claim a citation is fabricated based on
  a no-web run.
- **Heuristic precision varies by source.** Crossref is authoritative for
  DOI resolution; Retraction Watch is authoritative for retraction status;
  publisher sites are authoritative for everything else but inconsistent
  to scrape. The prompt documents which source ground-truths each
  heuristic — preserve that mapping when editing.
- **Adversarial vs real test sets serve different purposes.**
  `test-sets/adversarial-30.md` exercises detection (should flag every
  entry). `test-sets/real-articles/` exercises specificity (should flag
  near-zero entries). A prompt revision that improves one without
  regressing the other is a real win; a revision that flags more on both
  is just noisier.

## Implementation Workflow

Read the issue body in full before writing any code or editing any files.
The working branch is created by the pipeline; you commit and push to it.

PR workflow + auto-merge arming protocol is fleet-wide; see
`~/repos/CLAUDE.md`.

## When in doubt

- **What does heuristic X catch?** `docs/heuristics.md`.
- **Why this tool exists when Edifix / Scite / iThenticate exist?**
  `docs/competitive-landscape.md`.
- **What's the next prompt version doing differently?** `roadmap/v4-features.md`.
- **Where do audit outputs go?** `reports/` (see `reports/README.md` for
  the naming convention).
