# aml-multiomics

Multi-omics integration of genomic, transcriptomic and metabolomic data in AML cohorts (BEAT-AML, TCGA, NGS-PTL) to identify metabolic modules associated with genetic alterations.

## Biological question

Acute myeloid leukemia (AML) cells carry somatic mutations that alter their metabolism. This project asks: do specific genetic alterations (mutations, CNV, translocations) drive specific metabolic programs? If so, those programs may represent therapeutic targets.

## Data

Three cohorts, each with different data types.

**BEAT-AML**
- Gene expression: 35,566 genes × 510 samples, log2 CPM-TMM normalized, Hugo gene symbols
- WES mutations: 305 samples, 551 mutated genes

**TCGA-LAML**
- Gene expression: 30,895 genes × 152 samples, linear CPM-TMM normalized, Ensembl IDs
- WES mutations: 186 samples, 333 mutated genes

**NGS-PTL**
- Metabolomics: 301 metabolites × 92 samples (linear and log-transformed)
- WES mutations: 172 samples, 356 mutated genes

**Reference files**
- Human-GEM: genome-scale metabolic model with 12,971 reactions, 2,887 metabolic genes, 147 subsystems
- Karyotype: CNV and translocation data (columns F-R; note — current file appears incomplete, to be clarified)
- WES_filtered_final: long-format mutation table across all three cohorts

Expression data for BEAT-AML and TCGA were downloaded pre-normalized. The two cohorts use different normalization schemes and cannot be merged; analyses are run separately on each.

## Analysis approach

The method follows Chen et al. (2024, Cell Reports) adapted for AML.

1. Filter expression data to metabolic genes from Human-GEM (2,887 genes). Map Ensembl IDs to Hugo symbols for TCGA using the Human-GEM gene table.

2. Intersect samples: keep only samples with both expression and WES data.

3. Run WGCNA on metabolic gene expression (BEAT-AML and TCGA separately) to identify co-expression modules. Each module corresponds to a metabolic process (glycolysis, TCA cycle, lipid metabolism, etc.) and is annotated using Human-GEM subsystems.

4. For NGS-PTL, run WGCNA on the metabolomics matrix to identify metabolite modules.

5. For each module, compute an eigenvalue per sample (first principal component). This reduces the data from ~2,887 genes to ~25 module scores per sample.

6. Test association between module eigenvalues and genetic alterations (mutations, CNV, translocations) using Fisher's exact test with FDR correction.

7. If NGS-PTL samples overlap with transcriptomic cohorts, integrate metabolite modules with gene expression modules.

## Reference

Chen et al. (2024). Integrated metabolic-transcriptomic network identifies immunometabolic modulations in human macrophages. *Cell Reports* 43, 114741. https://doi.org/10.1016/j.celrep.2024.114741

## Data format

Raw TSV files are converted to RDS for faster loading in R:

```r
df <- read.table("raw/beat_aml_expression.tsv", sep="\t", header=TRUE, row.names=1)
saveRDS(df, "data/beat_aml_expression.rds")
df <- readRDS("data/beat_aml_expression.rds")
```

## Dependencies

R packages: WGCNA, missForest, clusterProfiler, readxl, arrow (optional, for Parquet)

## Known issues

- The Karyotype file currently contains only 7 columns. Columns F-R with CNV and translocation data appear missing — to be confirmed with data provider.
- TCGA uses Ensembl IDs; BEAT-AML uses Hugo symbols. ID mapping is required before joint analysis.
- Metabolomics sample IDs (numeric) need to be matched to WES NGS-PTL sample IDs before integration.
