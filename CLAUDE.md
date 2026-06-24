# aml-multiomics

Multi-omics integration project for AML cohorts (BEAT-AML, TCGA-LAML, NGS-PTL).

## Project structure
- data/raw/        — original files, do not modify
- data/processed/  — cleaned matrices ready for analysis
- analysis/        — Quarto notebooks mostly
- docs/            — reference papers and presentations

## Key data
- BEAT-AML: 221 samples, log2 CPM-TMM, Hugo gene symbols
- TCGA-LAML: ~150 samples, linear CPM-TMM, Hugo gene symbols
- NGS-PTL: 92 samples (50 AML + 42 healthy CD34+ controls), metabolomics (ln-transformed in processed/), 300 metabolites
- WES: long-format mutation table across all three cohorts

## R style
- tidyverse preferable to base R equivalents
- pipe: |>
- plots: ggplot2

## Notes
- BEAT-AML and TCGA cannot be merged (different normalizations, must be analyzed separately)
- NGS-PTL metabolome sample IDs (numeric) not yet matched to WES IDs — mapping table needed
- Karyotype file appears incomplete (columns F-R missing)