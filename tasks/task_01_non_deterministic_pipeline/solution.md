# Reference Solution

## Important Note for Evaluators

This task is intentionally non-deterministic. The solution below represents **one well-reasoned response**, not the only acceptable one. Evaluators should assess whether a model's response is internally consistent and defensibly argued — not whether it matches these specific values. See `rubric.json` for range-based grading criteria and `evaluation_notes.md` for guidance on scoring divergent but valid responses.

---

## Threshold Selection

### TSS Enrichment
**Threshold:** PASS ≥ 7 | BORDERLINE 4–6.9 | FAIL < 4

TSS enrichment is the single most informative metric for ATAC-seq quality. The ENCODE consortium recommends a minimum of 7 for high-quality data; values between 4–6 are often acceptable for tissues with lower baseline accessibility but warrant flagging. Values below 4 indicate poor signal-to-noise and compromised chromatin accessibility data.

The upper end of the borderline range is somewhat arbitrary — a cutoff of 5 or 6 would also be defensible depending on cell type. For a same-batch, same-cell-type experiment, I apply the stricter end (7) since there is no tissue-specific justification for leniency.

### FRiP
**Threshold:** PASS ≥ 0.30 | BORDERLINE 0.20–0.29 | FAIL < 0.20

ENCODE guidelines recommend FRiP ≥ 0.30 for ATAC-seq. FRiP is dependent on sequencing depth and peak calling parameters, so it should not be interpreted in isolation. A sample with FRiP = 0.28 but high TSS enrichment may still be biologically informative. FRiP < 0.20 strongly suggests poor library quality or failed peak calling.

### Library Complexity (NRF)
**Threshold:** PASS ≥ 0.80 | BORDERLINE 0.60–0.79 | FAIL < 0.60

NRF ≥ 0.80 is the ENCODE recommendation for acceptable library complexity. Values between 0.60–0.79 may indicate over-sequencing relative to library size, which can be addressed by downsampling. Values below 0.60 suggest the library was poorly prepared or sequenced too deeply, and the effective unique read count may be insufficient for reliable peak calling.

### MT Read Fraction
**Threshold:** PASS ≤ 0.10 | BORDERLINE 0.10–0.20 | FAIL > 0.20

High mitochondrial read fraction typically indicates poor nuclear chromatin accessibility, cell membrane lysis prior to nucleus isolation, or a high proportion of dead or dying cells. Values up to 10% are routinely acceptable. Values between 10–20% represent a warning — the nuclear signal may still be sufficient if other metrics are strong, but the sample should be flagged. Above 20%, the nuclear read count is likely too depleted to reliably detect accessible regions.

---

## Sample Classifications

| Sample | TSS (≥7 / 4–6.9 / <4) | FRiP (≥0.30 / 0.20–0.29 / <0.20) | NRF (≥0.80 / 0.60–0.79 / <0.60) | MT (≤0.10 / 0.10–0.20 / >0.20) | **Call** |
|--------|------------------------|----------------------------------|---------------------------------|--------------------------------|----------|
| S1 | PASS (12.4) | PASS (0.42) | PASS (0.91) | PASS (0.03) | **PASS** |
| S2 | FAIL (3.1) | FAIL (0.18) | BORDERLINE (0.61) | BORDERLINE (0.08) | **FAIL** |
| S3 | PASS (7.8) | BORDERLINE (0.31→PASS*) | PASS (0.88) | PASS (0.05) | **PASS** |
| S4 | BORDERLINE (6.2) | BORDERLINE (0.28) | BORDERLINE (0.72) | BORDERLINE (0.18) | **BORDERLINE** |
| S5 | FAIL (1.9) | FAIL (0.11) | FAIL (0.45) | FAIL (0.31) | **FAIL** |
| S6 | PASS (8.9) | PASS (0.35) | PASS (0.85) | PASS (0.04) | **PASS** |

*S3 FRiP of 0.31 sits just above the threshold — classified as PASS but noted as low.

**Summary:** PASS: S1, S3, S6 | BORDERLINE: S4 | FAIL: S2, S5

---

## Borderline Justifications

### S4 — BORDERLINE
S4 has no outright failures but accumulates marginal values across all four metrics: TSS enrichment at 6.2 (below the strict threshold of 7 but above the minimum acceptable value of 4), FRiP at 0.28 (just below 0.30), NRF at 0.72 (moderate complexity), and MT fraction at 0.18 (approaching the upper warning boundary).

The concern is **compound marginality**: each metric is individually defensible, but the pattern across all four suggests a sample that was technically difficult — potentially lower starting cell number, partial lysis, or suboptimal transposition. In isolation, TSS enrichment of 6.2 from the same cell type and batch as samples achieving 8.9–12.4 is notably lower and warrants scrutiny.

**Include if:** The experiment requires the sample for statistical power, there are biological reasons to expect this sample to behave differently (e.g., treatment condition), and downstream analysis is accompanied by sensitivity analysis with and without S4.

**Exclude if:** Sufficient passing samples exist, or if the analysis is sensitive to signal quality (e.g., footprinting, fine-resolution motif analysis).

### S2 — Noted
S2 fails on two primary metrics (TSS and FRiP) and is borderline on NRF and MT fraction. While NRF of 0.61 is not catastrophic, the low TSS enrichment (3.1) suggests the library lacks meaningful chromatin signal. This is a FAIL, not a borderline call, but worth noting that the MT fraction (0.08) does not suggest mitochondrial contamination as the primary cause — the failure mode here is more likely poor transposition efficiency or insufficient nuclei yield.

---

## Batch-Level Assessment

The batch shows a wide range of quality, which is unusual for same-cell-type, same-batch samples. S1 and S6 are excellent; S3 is acceptable; S4 is marginal; S2 and S5 are clear failures.

**S5 is a particular concern.** With TSS enrichment of 1.9, FRiP of 0.11, NRF of 0.45, and MT fraction of 0.31, it appears to be a near-complete library failure — the chromatin accessibility signal is minimal and the library is dominated by mitochondrial and duplicate reads. This is not a borderline sample; it failed at or before the transposition step.

**Potential systemic interpretation:** The co-occurrence of high MT fraction (S4: 0.18, S5: 0.31) and low TSS enrichment in the worst-performing samples suggests that the failure mode may be **incomplete nuclear isolation** — cells that lysed before or during transposition would produce a library enriched for mitochondrial DNA and depleted of organized chromatin signal. If S2 and S5 came from the same preparation or operator, this interpretation strengthens. The batch-level takeaway is that the protocol should be reviewed for lysis efficiency, particularly for samples that showed early signs of poor viability.

---

## Acceptable Threshold Variants

The following threshold choices differ from the above but are equally defensible:

| Metric | This solution | Also acceptable |
|---|---|---|
| TSS Enrichment | ≥ 7 (PASS) | ≥ 5–8 (PASS cutoff) |
| FRiP | ≥ 0.30 | ≥ 0.20–0.25 for lower-depth libraries |
| NRF | ≥ 0.80 | ≥ 0.70 in lower-input settings |
| MT Fraction | ≤ 0.10 | ≤ 0.15 for some tissue types |

Any threshold within these ranges, applied consistently, should receive full or near-full credit on threshold-related rubric criteria.
