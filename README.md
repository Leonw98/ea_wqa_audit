# EA Water Quality Archive: Data Quality Audit

**A reproducible data-quality audit built against the Environment Agency's public Water Quality Archive API. Open data, open-source code (MIT licence). A portable work sample demonstrating the cross-reference, root-cause, and caveat workflow any data or data-quality analyst role requires.**

No proprietary or client material is involved. Every finding is traceable to a public API endpoint; every check reproduces from the code in this repository against a cached snapshot committed under `data/`.

The audit is not a claim that it surfaces a new problem for the Agency. Formatting drift in a long-lived reference codelist is expected signal, not an indictment.

---

## What it does

1. Pulls the **determinand codelist** (8,070 records) and the **sampling-point-type codelist** (113 records) in full via the EA WQA REST API.
2. Pulls a **sample of 200 sampling points** (JSON-LD) for referential-integrity checks.
3. Runs five DQ checks:
   - Exact duplicate `prefLabel` values
   - Near-duplicate `prefLabel` pairs (rapidfuzz at 90% or higher)
   - Missing `altLabel` prevalence
   - `prefLabel` formatting inconsistencies (whitespace, case, non-ASCII)
   - Sampling-point to samplingPointType referential integrity
4. Emits a human-readable findings markdown and a three-page A4 PDF summary suitable for stakeholder briefing.

## Findings snapshot

| Severity | Check | Count |
|---|---|---|
| MEDIUM | Exact duplicate `prefLabel` rows in determinand codelist | 1,514 |
| MEDIUM | Near-duplicate `prefLabel` pairs (90% or higher fuzzy) | 502 |
| MEDIUM | `prefLabel` formatting inconsistencies | 301 |
| LOW | Missing `altLabel` on determinand records | 0 |
| INFO | Sampling-point to samplingPointType orphan codes | 0 |

**Headline:** 1,122 unique `prefLabel` values appear more than once across 8,070 determinands, including real analytes such as *Flow, instantaneous* (7x), *Benzene* (6x), *Dichloromethane* (6x), and *Enterovirus* (6x). Referential integrity from sampling points into the type codelist holds cleanly in the 200-record sample.

**Honest caveat on the near-duplicate finding.** Most of the 502 pairs flagged at 90% or higher are *not* duplicates. They are chemically distinct variants whose names differ by one letter, for example `2-(N-Ethylperfluorooctanesulfonamido)acetic acid` vs `2-(N-Methylperfluorooctanesulfonamido)acetic acid` (ethyl vs methyl PFAS variants). A naive fuzzy scan flags them as twins; a subject-matter expert would correctly keep them separate. This is an intentional demonstration of where automated DQ rules need manual review, not a claim that the codelist is 502 items too big.

## Run it yourself

```bash
pip install -r requirements.txt
python audit.py
```

Outputs land in `out/findings.md` and `out/summary.pdf`. Cached API responses are in `data/` so the repo reproduces offline against the same snapshot used to write this README.

## Stack

- Python 3.11+ (stdlib `urllib` for HTTP, no `requests` dependency)
- pandas for codelist manipulation
- rapidfuzz for near-duplicate detection
- matplotlib (`backend_pdf.PdfPages`) for the multi-page PDF summary

## Files

```
.
├── README.md           this file
├── requirements.txt    pandas, rapidfuzz, matplotlib
├── audit.py            end-to-end script
├── data/               cached API responses (committed)
│   ├── codelist_determinand_*.json
│   ├── codelist_sampling-point-type_*.json
│   └── sampling_points_200.json
└── out/
    ├── findings.md     written summary
    └── summary.pdf     three-page A4 infographic
```

## What this demonstrates

- **Cross-referencing records** across codelists: sampling-point to samplingPointType referential-integrity check.
- **Identifying inaccuracies, inconsistencies, and missing data at scale:** duplicates, near-duplicates, formatting drift, and prevalence of optional fields.
- **Root-cause attribution:** findings linked to methodology accretion over decades rather than reflexive cleaning recommendations.
- **Honest caveats alongside findings** so non-technical readers understand what the numbers do and do not mean.
- **Power BI-equivalent stakeholder summary** rendered as a single-source-of-truth PDF with severity-coloured visuals.
- **Reproducibility:** API responses cached offline, code re-runs idempotently against the same snapshot.

## Author

Leon Wilkinson · leon.wilkinson98@hotmail.co.uk · github.com/Leonw98

Built April 2026 as a reusable data-quality work sample.

## Licence

MIT.
