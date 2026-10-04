# NERC Q1 2026 report: PDF-to-dataset extraction

Turns the DisCo performance tables of Nigeria's NERC *First Quarter 2026 Quarterly Report* (94-page PDF) into a clean, validated, traceable dataset, and replicates one published chart and one published calculation.

## Headline results
| | |
|---|---|
| Dataset | `data/processed/nerc_2026Q1_disco_long.csv`: 434 values from Tables 4-9 (11 DisCos + total, 2025/Q4 and 2026/Q1), each with source table, PDF page and printed page |
| Internal validation | 272 pass, 5 source discrepancies confirmed on the page images, 0 unexplained failures (288 checks) |
| Cross-check vs original | 426/426 cells agree with a second engine; 72/72 with Camelot; 30/30 random cells match the page images |
| Chart replicated | Figure 8 (remittance to NBET): bars, performance row and totals reproduced (`figures/fig8_replication_side_by_side.png`) |
| Calculation replicated | Table 9 revenue loss/gain column (11/11 within 0.014pp) and the quoted N140.64bn loss |
| Findings about the source | DisCo rows don't sum to published totals in 3 places (S1); Table 5 and Table 7 disagree on 2025/Q4 EAE because of an Ibadan revision, which we reproduce numerically (S2) |

## Layout
```
data/raw/        untouched PDF + SHA256SUMS.txt (read-only)
data/interim/    page inventory, raw wide extracts exactly as printed (strings)
data/processed/  tidy dataset, validation log, replication outputs
scripts/         01_inventory -> 02_extract -> 03_clean -> 04_validate -> 05_crosscheck
                 -> 05b_record_visual_check -> 06_replicate -> 07_make_chart -> 08_engine_checks
docs/            SOURCES, DATA_DICTIONARY, CHANGELOG (manual rules + defects), VALIDATION_REPORT,
                 manual_verification.csv (30-cell visual sample)
figures/         replication chart; verify/ = page crops used for visual checks
logs/            stdout of each step
run_all.sh       verifies the hash, then re-runs everything from the raw PDF
```

## Method in one paragraph
Page inventory (text layer vs scan, tables per page) -> word-position row parser (default `extract_tables()` merged cells, see CHANGELOG) -> tidy long format with units, standardised names and page references -> rounding-aware recomputation of every published ratio, sums, cross-table and cross-unit checks -> independent re-reads (poppler, Camelot) and a seeded visual sample -> replication of a chart and an undocumented calculation.


