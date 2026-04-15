# Task: ATAC-seq Sample QC Decision

## Background

Quality control in ATAC-seq pipelines requires making pass/fail decisions based on multiple metrics that reflect different aspects of library quality. These metrics include measures of signal enrichment at biologically meaningful regions, the fraction of reads in peaks, library complexity, and contamination from mitochondrial reads.

No universal QC standard exists. Published guidelines vary depending on cell type, tissue source, sequencing depth, and experimental protocol. A well-reasoned QC decision requires selecting defensible thresholds, acknowledging tradeoffs, and treating borderline samples with appropriate nuance rather than forcing binary verdicts.

---

## Input

You are given QC metrics for six ATAC-seq samples processed through a standard bulk ATAC-seq pipeline (paired-end, hg38 alignment, post-deduplication). All samples come from the same cell type and experimental batch.

| Sample | TSS Enrichment | FRiP | Library Complexity (NRF) | MT Read Fraction |
|--------|---------------|------|--------------------------|-----------------|
| S1 | 12.4 | 0.42 | 0.91 | 0.03 |
| S2 | 3.1  | 0.18 | 0.61 | 0.08 |
| S3 | 7.8  | 0.31 | 0.88 | 0.05 |
| S4 | 6.2  | 0.28 | 0.72 | 0.18 |
| S5 | 1.9  | 0.11 | 0.45 | 0.31 |
| S6 | 8.9  | 0.35 | 0.85 | 0.04 |

**Metric definitions:**
- **TSS Enrichment:** Signal-to-noise ratio computed as read enrichment at transcription start sites relative to flanking regions. Higher is better.
- **FRiP (Fraction of Reads in Peaks):** Proportion of aligned reads that fall within called peaks. Higher is better.
- **NRF (Non-Redundant Fraction):** Library complexity measure — fraction of reads that are non-duplicate. Ranges 0–1; higher is better.
- **MT Read Fraction:** Proportion of reads mapping to the mitochondrial genome. Lower is better; high values indicate poor nuclear signal or cell lysis issues.

---

## Task

1. **Set thresholds.** For each metric, state the threshold(s) you will use to evaluate samples and explain why you chose those values. Cite the tradeoff between stringency and sample retention where relevant.

2. **Classify each sample.** Apply your thresholds to assign each sample one of three categories:
   - **PASS** — acceptable quality for downstream analysis
   - **BORDERLINE** — marginal quality; acceptable with caveats or after closer inspection
   - **FAIL** — quality too poor to include

3. **Justify borderline calls.** For any sample classified as BORDERLINE, explain what specific concern it raises, what downstream impact it might have, and under what conditions you would include or exclude it.

4. **Summarize the batch.** Comment on whether the batch as a whole is acceptable, and whether any patterns across samples suggest a systemic experimental issue rather than sample-specific failures.

---

## Output Format

Structure your response with clearly labeled sections:
- **Threshold Selection**
- **Sample Classifications** (a table is acceptable)
- **Borderline Justifications**
- **Batch-Level Assessment**

There is no single correct answer. You will be evaluated on the defensibility of your thresholds, the consistency of their application, the quality of your reasoning about borderline cases, and the appropriateness of your uncertainty.
