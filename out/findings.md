# EA Water Quality Archive: Data Quality Audit

**Source:** Environment Agency Water Quality Archive (public, open data).
**API docs:** https://environment.data.gov.uk/water-quality/api-docs
**Snapshot:** cached in `data/`; repo reproduces against this fixed snapshot.

## Scope

- Determinand codelist: **8070** records
- Sampling-point-type codelist: **113** records
- Sampling-point sample: **200** records

## Summary of findings

| Severity | Check | Count |
|---|---|---|
| MEDIUM | Exact duplicate prefLabels in determinand codelist | 1514 |
| MEDIUM | Near-duplicate prefLabels (rapidfuzz >= 90%) | 502 |
| LOW | Missing altLabel on determinand records | 0 |
| MEDIUM | prefLabel formatting inconsistencies | 301 |
| INFO | Sampling-point -> samplingPointType referential integrity | 0 |

## Detail

### MEDIUM: Exact duplicate prefLabels in determinand codelist

1122 prefLabel values appear more than once (covering 2636 codelist rows). Top offenders: 'Flow, instantaneous' (7x), '2-Propanol :- {iso-Propanol}' (7x), 'Benzene' (6x), 'Dichloromethane :- {Methylene Dichloride}' (6x), 'Enterovirus' (6x)

### MEDIUM: Near-duplicate prefLabels (rapidfuzz >= 90%)

502 near-duplicate pairs within the first 1500 records. Representative pairs: '2-(N-Ethylperfluorooctanesulfonamido)acetic acid - branched' <-> '2-(N-Methylperfluorooctanesulfonamido)acetic acid - branched' (99%); '2-(N-Ethylperfluorooctanesulfonamido)acetic acid - linear' <-> '2-(N-Methylperfluorooctanesulfonamido)acetic acid - linear' (99%); '2-(N-Methylperfluorooctanesulfonamido)acetic acid: wet wt' <-> '2-(N-Ethylperfluorooctanesulfonamido)acetic acid: wet wt' (99%); '2-(N-Methylperfluorooctanesulfonamido)acetic acid (Total)' <-> '2-(N-Ethylperfluorooctanesulfonamido)acetic acid (Total)' (99%); 'N-Methyl-N-(2-hydroxyethyl)perfluorooctanesulfonamide' <-> 'N-Ethyl-N-(2-hydroxyethyl)perfluorooctanesulfonamide' (99%)

### LOW: Missing altLabel on determinand records

0 of 8070 determinand records (0.0%) have no altLabel. altLabel is a recognised optional field in the schema, but the prevalence is worth surfacing for downstream search / disambiguation use cases.

### MEDIUM: prefLabel formatting inconsistencies

Counts by type: leading/trailing whitespace: 38, double spaces: 12, all-caps words (4+ char runs): 249, non-ASCII characters: 2. Root-cause hypothesis: the determinand codelist has been accreted over many years and collection methodologies; formatting drift is typical signal of this, not data-entry error.

### INFO: Sampling-point -> samplingPointType referential integrity

0 of 200 sampling points in sample cite a samplingPointType code not present in the codelist. All codes resolve.

## Near-duplicate prefLabel pairs (top 15)

| ratio | notation A | label A | notation B | label B |
|---|---|---|---|---|
| 99% | `2864` | 2-(N-Ethylperfluorooctanesulfonamido)acetic acid - branched | `2866` | 2-(N-Methylperfluorooctanesulfonamido)acetic acid - branched |
| 99% | `2863` | 2-(N-Ethylperfluorooctanesulfonamido)acetic acid - linear | `2865` | 2-(N-Methylperfluorooctanesulfonamido)acetic acid - linear |
| 99% | `2916` | 2-(N-Methylperfluorooctanesulfonamido)acetic acid: wet wt | `2917` | 2-(N-Ethylperfluorooctanesulfonamido)acetic acid: wet wt |
| 99% | `2891` | 2-(N-Methylperfluorooctanesulfonamido)acetic acid (Total) | `2892` | 2-(N-Ethylperfluorooctanesulfonamido)acetic acid (Total) |
| 99% | `2976` | N-Methyl-N-(2-hydroxyethyl)perfluorooctanesulfonamide | `2978` | N-Ethyl-N-(2-hydroxyethyl)perfluorooctanesulfonamide |
| 99% | `2942` | 2-(N-Ethylperfluorooctanesulfonamido)acetic acid (B) | `2943` | 2-(N-Methylperfluorooctanesulfonamido)acetic acid (B) |
| 99% | `2990` | 2-(N-Methylperfluorooctanesulfonamido)acetic acid (L) | `2991` | 2-(N-Ethylperfluorooctanesulfonamido)acetic acid (L) |
| 99% | `2929` | Flow Passed Forward readings when overflow is operating: Annual median | `2930` | Flow Passed Forward readings when overflow is operating: Annual mean |
| 99% | `2975` | N-methylperfluorooctanesulfonamide | `2977` | N-Ethylperfluorooctanesulfonamide |
| 99% | `0093` | Permanganate Value N/80 3 Minutes | `0094` | Permanganate Value N/80 30 Minutes |
| 98% | `3136` | GCMS Scan : Target Based multi-residue screening : Additional 3 | `3137` | GCMS Scan : Target Based multi-residue screening : Additional 2 |
| 98% | `3137` | GCMS Scan : Target Based multi-residue screening : Additional 2 | `3353` | GCMS Scan : Target Based multi-residue screening : Additional 1 |
| 98% | `3136` | GCMS Scan : Target Based multi-residue screening : Additional 3 | `3353` | GCMS Scan : Target Based multi-residue screening : Additional 1 |
| 98% | `3135` | Liquid Chromatography Mass Spectroscopy : Additional 2 | `3352` | Liquid Chromatography Mass Spectroscopy : Additional 1 |
| 98% | `3134` | Liquid Chromatography Mass Spectroscopy : Additional 3 | `3135` | Liquid Chromatography Mass Spectroscopy : Additional 2 |

## Methodology and caveats

- Near-duplicate scan is capped at the first 1500 determinand records (8,070 total) so that the demo runs in under a minute. A production run would scan the full Cartesian product (≈ 32M pairs) or use blocking on the first characters to stay linear.
- `rapidfuzz.ratio` is Levenshtein-based; a label pair scoring 90% is suggestive of duplication but not conclusive. Each pair flagged here would be reviewed manually against the source documentation before any codelist merge.
- The determinand codelist has been accreted over multiple decades and measurement methodologies. Formatting drift and near-duplicate labels are **expected signal** of that history, not indictments. The point of the audit is to surface them for governance decisions, not to auto-clean them.

## What this demonstrates

- Cross-referencing records (sampling-point -> samplingPointType).
- Identifying inaccuracies, inconsistencies, and missing data at scale.
- Attributing findings to a root cause (methodology accretion over time) rather than a short-term fix.
- Communicating caveats alongside findings so non-technical readers understand what the numbers do and do not mean.
- Power BI or matplotlib-equivalent visual summary for at-a-glance dashboarding (see `summary.pdf`).
