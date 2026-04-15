# Evaluation Notes

## Task Overview

This task evaluates a model's ability to implement a multi-step algorithmic pipeline combining **symbolic parsing**, **domain-specific scoring**, and **probabilistic normalization**. No domain knowledge beyond the explicitly stated rules is required or rewarded.

The key competencies under test are:
- Structured data parsing (dot-bracket notation)
- Rule-based scoring from a lookup table
- Boltzmann weighting and normalization
- Marginal probability aggregation
- Detecting and handling malformed inputs

---

## Scoring Philosophy

Rubric weights are designed to reward **correctness of reasoning**, not just the final number. A model that gets the right answer via a wrong path (e.g., two errors that cancel out) should not receive full credit.

Evaluate each rubric criterion independently. Use the intermediate calculations in the model's response, not just the final answer, to determine correctness at each step.

---

## Expected Answer

| Condition | p(1,6) |
|---|---|
| y5 excluded (recommended) | ≈ 0.049 |
| y5 included (as in source) | ≈ 0.044 |

Accept answers within ±0.005 of the applicable value.

---

## Common Failure Modes

### FM-01 — Silent y5 inclusion
The most common error. The model accepts all five structures without flagging the length mismatch. This produces a subtly wrong partition function and final answer. Deduct R16 entirely; apply partial deductions to R17 and R18.

### FM-02 — G–G classified as GC
The model applies −3 to pair (1,6) [G–G] instead of −1. This inflates the weight for y1 and y2, raising p(1,6) above the correct value. Deduct R03, R04, and partial credit on R06, R17.

### FM-03 — Zero-weight for zero-energy structure
The model computes exp(−ΔG) = 0 for y1 because ΔG = 0. This is a fundamental confusion between energy and weight. Deduct R07 and all downstream steps that depend on w(y1).

### FM-04 — Indexing errors
The model produces a pair table with off-by-one errors (e.g., reporting (0,5) as the target pair, or using 0-based indexing throughout without translating back to 1-based in the conclusion). Deduct R14.

### FM-05 — Incomplete unpaired count
The model counts only the explicitly "middle" positions as unpaired and misses positions at the ends of a structure. Example: in y2 = `(....)`, positions 2–5 are unpaired, not positions 2–4. Deduct R02, R05.

### FM-06 — Missing or incomplete normalization
The model computes weights but reports them as probabilities without dividing by Z. Deduct R08, R09, R10.

---

## Calibration Notes

- This task has a clear, computable ground truth and is well suited for **automated verification** of intermediate values.
- The main evaluation judgment required is around **y5 handling** (EC-01): decide in advance whether you are scoring for the "include y5" or "exclude y5" regime and apply it consistently across all responses being evaluated.
- If using this task in a comparative evaluation, ensure all models see the same prompt text — minor wording differences in the energy model description can affect classification of non-canonical pairs.

---

## Difficulty Assessment

**Estimated difficulty:** Medium  
**Primary challenge:** Catching the y5 length mismatch and correctly classifying non-canonical pairs.  
**Secondary challenge:** Correctly applying the zero-energy → non-zero-weight transformation.  
**Algorithmic complexity:** Low (all steps are O(n) in sequence length).

## Sources
https://doi.org/10.48550/arXiv.2603.02283
https://doi.org/10.1093/bioinformatics/btaa460