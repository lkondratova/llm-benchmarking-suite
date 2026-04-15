# Evaluation Notes

## Task Overview

This task evaluates a model's ability to make **defensible, non-deterministic decisions** under realistic scientific ambiguity. Unlike algorithm reconstruction tasks with a single ground truth, this task rewards:

- Threshold choices that are grounded in published norms or explicit tradeoff reasoning
- Consistent application of self-stated criteria
- Appropriate uncertainty and hedging on borderline cases
- Pattern recognition at the batch level, beyond per-sample verdicts

The task is deliberately designed so that multiple threshold sets and classification outcomes are valid, while some outcomes (S5 as PASS, S2 as PASS, thresholds that contradict published guidance by an order of magnitude) are not.

---

## Scoring Philosophy

### What to reward
- **Explicit thresholds with rationale.** A model that states "I will use TSS ≥ 7 based on ENCODE guidelines" and applies it consistently earns full threshold credit, even if a different defensible cutoff was chosen.
- **Graduated verdicts.** Any functional equivalent of a BORDERLINE category (flagged, conditional, caveated) earns R11 credit.
- **Mechanism over label.** A model that identifies *why* S5 failed (lysis artifact, transposition failure) rather than just noting it failed demonstrates domain understanding worth rewarding on R16.
- **Conditional reasoning.** Statements like "I would include S4 only if n < 4 passing samples are available" demonstrate the kind of context-sensitive judgment that separates good QC reasoning from rule application.

### What to penalize
- **Threshold inversion.** A model that states a threshold but violates it in the classification table has made a reasoning error, not a judgment call. Penalize R14 regardless of whether the threshold itself was lenient or strict.
- **Hallucinated rescue of S5.** Invoking single-cell, low-input, or degraded-tissue leniency when the prompt specifies standard bulk conditions is a factual error about the experimental context. Penalize R07, R17.
- **False precision on non-deterministic questions.** A model that provides a single threshold per metric and presents it as the only acceptable value (rather than one of several defensible choices) is misrepresenting the nature of QC decision-making. Minor penalty on R19.
- **Batch assessment as summary.** If the "batch-level assessment" section merely restates individual verdicts without adding any cross-sample inference, penalize R15 and R16.

---

## Score Interpretation Guide

| Total score (%) | Interpretation |
|---|---|
| 85–100 | Strong response: thresholds well-grounded, classifications consistent, borderlines well-argued, batch pattern identified |
| 70–84 | Good response: minor threshold inconsistency or missed batch-level insight; no major reasoning failures |
| 55–69 | Adequate response: thresholds stated but poorly justified or partially inconsistent; borderline category present but thin |
| 40–54 | Weak response: binary classification only, or inconsistent threshold application, or S5 misclassified |
| < 40 | Poor response: S5 classified as PASS or BORDERLINE, or no thresholds stated, or major internal contradictions |

---

## Calibration Anchors

Use these fixed points to calibrate scoring across evaluators:

| Criterion | Certain PASS | Certain FAIL |
|---|---|---|
| S1 classification | Any threshold consistent with published ranges → PASS | — |
| S5 classification | — | Any response classifying S5 as PASS or BORDERLINE |
| Threshold consistency | All classifications match stated thresholds | Any classification directly contradicts stated threshold |
| Batch assessment | Identifies MT + TSS co-occurrence as systemic signal | Restates per-sample verdicts only |

---

## Comparison Across Model Responses

When scoring multiple responses to the same prompt:

1. **Anchor on S5 and S1 first.** These are non-negotiable. Any response that misclassifies either should be scored no higher than 55% regardless of other reasoning quality.

2. **Check threshold consistency before checking threshold values.** A lenient-but-consistent response outscores a strict-but-inconsistent one.

3. **Award S4 nuance differentially.** Models that provide a richer account of S4 (compound marginality, downstream sensitivity, conditional inclusion criteria) should score noticeably higher on R10, R12, R13 than models that give a brief verdict.

4. **Batch assessment is a differentiator at the high end.** The difference between a 75 and a 90 score often comes down to whether the model identifies the MT + TSS co-occurrence pattern and interprets it mechanistically.

---

## Known Ambiguities

### NRF threshold range
The NRF acceptable range in the rubric (PASS cutoff 0.70–0.85) is wider than for other metrics because published guidance on NRF varies more across protocols and sequencing platforms. A model setting NRF PASS at 0.65 with explicit acknowledgment that lower-input protocols tolerate lower complexity is reasonable; one setting it at 0.65 without rationale is not.

### FRiP depth-dependence
FRiP is known to decrease as sequencing depth increases beyond peak saturation. A model that notes FRiP should be interpreted alongside sequencing depth, and that the provided metrics may reflect different depths across samples, is demonstrating above-average domain understanding. This does not change the classification outcomes given the data, but should be noted as a positive signal in overall coherence (R20).

### What counts as "citing published guidelines"
A model does not need to cite a specific paper or URL. Phrases like "ENCODE recommends," "commonly used threshold in the field," or "values below X are widely considered insufficient" are sufficient for R05. Inventing a specific guideline that does not exist (e.g., citing a non-existent paper or consortium recommendation) should be penalized.

---

## Difficulty Assessment

**Estimated difficulty:** Medium-High  
**Primary challenge:** S4 compound marginality and batch-level pattern recognition  
**Secondary challenge:** Avoiding threshold inversion when multiple metrics are near-boundary  
**Domain knowledge required:** Basic familiarity with ATAC-seq QC conventions; no wet-lab expertise required  
**Non-determinism type:** Threshold selection (continuous), classification (ordinal), interpretation (open-ended)
