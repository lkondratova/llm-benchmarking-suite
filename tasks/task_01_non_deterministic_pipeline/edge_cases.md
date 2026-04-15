# Edge Cases

This document describes samples, metrics, and situations in the task that require specific evaluator judgment. Edge cases are not trick questions — they are places where the task intentionally creates ambiguity that a good model should navigate explicitly.

---

## EC-01 — S4: Compound Marginality

**Sample:** S4 (TSS 6.2, FRiP 0.28, NRF 0.72, MT 0.18)  
**Issue:** No single metric is a clear failure, but all four are below ideal simultaneously. This is a deliberately constructed compound-marginal profile.

**Why it matters:** A model that evaluates metrics independently without aggregating their joint signal may classify S4 as PASS because each metric is "close enough." A model that recognizes the pattern of uniform marginality should flag S4 as BORDERLINE or provide explicit caveats even for a PASS call.

**Acceptable responses:**
- BORDERLINE with explanation of compound concern → full credit
- PASS with explicit acknowledgment that all metrics are near-threshold and caveats are warranted → partial credit (R10, R12, R13)
- PASS with no acknowledgment of the joint pattern → no credit on R10

**Unacceptable responses:**
- FAIL without justification (S4 does not fail any single metric under a lenient-but-defensible threshold)

---

## EC-02 — S3: FRiP Near the Boundary

**Sample:** S3 (TSS 7.8, FRiP 0.31, NRF 0.88, MT 0.05)  
**Issue:** FRiP of 0.31 sits just above the commonly cited 0.30 threshold. Whether this is PASS or BORDERLINE depends on the model's stated cutoff.

**Why it matters:** A model that sets a FRiP threshold of 0.30 and classifies S3 as PASS is being internally consistent. A model that sets 0.35 as the threshold but still classifies S3 as PASS is contradicting its own thresholds.

**Acceptable responses:**
- PASS if threshold ≤ 0.31 → consistent, full credit
- BORDERLINE if threshold > 0.31 → consistent, full credit on R14
- PASS with a note that FRiP is low → acceptable with acknowledgment

**Key evaluator check:** R14 (consistency) should be assessed by comparing the stated threshold to the classification, not by checking whether S3 is PASS or BORDERLINE in absolute terms.

---

## EC-03 — S2: Low MT Fraction Despite Overall Failure

**Sample:** S2 (TSS 3.1, FRiP 0.18, NRF 0.61, MT 0.08)  
**Issue:** S2 fails on TSS and FRiP, but its MT fraction (0.08) is within acceptable range. A model might incorrectly use the normal MT fraction to partially exonerate S2.

**Why it matters:** Low MT fraction alone does not rescue a sample with severely degraded chromatin signal. The failure mode here is likely poor transposition efficiency or insufficient nuclei yield — not mitochondrial contamination. A model that correctly identifies this distinction is demonstrating domain understanding.

**Acceptable responses:**
- FAIL, noting that low MT fraction does not compensate for absent chromatin signal → full credit on R08
- BORDERLINE with explicit acknowledgment of TSS failure and strong caveats → partial credit
- FAIL without noting the MT discrepancy → credit on R08, but no credit toward R16

**Unacceptable responses:**
- PASS or BORDERLINE citing MT fraction without acknowledging the TSS/FRiP failures

---

## EC-04 — S5: Unambiguous Failure — Watch for Hallucinated Rescue

**Sample:** S5 (TSS 1.9, FRiP 0.11, NRF 0.45, MT 0.31)  
**Issue:** This sample fails all four metrics by large margins. There is no defensible threshold set under which S5 should pass.

**Why it matters:** Some models may attempt to "rescue" S5 by citing extreme leniency (e.g., "in low-input single-cell ATAC-seq, TSS enrichment of 2 may be acceptable"). This is hallucinated context — the prompt specifies bulk ATAC-seq from a standard pipeline on the same cell type as the other samples.

**Acceptable responses:**
- FAIL, with any degree of explanation → full credit on R07 and R17

**Unacceptable responses:**
- BORDERLINE or PASS under any justification that contradicts the prompt's stated experimental context
- Any response that uses single-cell, low-input, or tissue-specific leniency without noting that the prompt specifies standard bulk conditions

---

## EC-05 — Threshold Inversion

**Potential error:** A model states a TSS enrichment threshold of 7 but classifies a sample with TSS = 6.2 as PASS, or states a MT threshold of 0.10 but classifies a sample with MT = 0.08 as BORDERLINE.

**Why it matters:** Internal inconsistency between stated thresholds and applied classifications is a clear reasoning failure, distinct from choosing a lenient or strict threshold.

**Evaluator guidance:** Always verify that each sample's classification is consistent with the model's own stated thresholds (R14). Do not penalize for lenient thresholds alone — penalize only for inconsistency between thresholds and their application.

---

## EC-06 — Missing BORDERLINE Category

**Potential structure:** A model that only uses PASS and FAIL with no intermediate category.

**Why it matters:** Binary classification is inappropriate for the data as presented. S4 is specifically designed to be irreducible to a clean PASS or FAIL without additional context. A model that forces binary verdicts is applying an inappropriately rigid QC philosophy.

**Acceptable alternatives:** A model may use terminology other than BORDERLINE (e.g., "conditional pass," "flag for review," "pass with caveats") — credit R11 if the functional equivalent of a third category is present.

**Unacceptable:** No acknowledgment of gradation or uncertainty at all.

---

## EC-07 — Batch Assessment Reduced to Per-Sample Summary

**Potential error:** The batch-level assessment section simply restates each sample's verdict without identifying any patterns or systemic interpretations.

**Why it matters:** The task explicitly asks for batch-level commentary, and the data is designed to reward pattern recognition: two samples (S4, S5) have high MT fractions alongside low TSS enrichment, suggesting a shared technical failure rather than random sample variability.

**Evaluator guidance:** Credit R15 only if the batch assessment contains at least one observation that could not be derived from any individual sample classification in isolation. Credit R16 only if the MT + TSS co-occurrence pattern is explicitly noted and a plausible mechanism is offered.
