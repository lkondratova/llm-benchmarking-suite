# Evaluation Notes

## Task Overview

This task evaluates a model's ability to execute a multi-step probabilistic computation with algebraic discipline — without a natural checkpoint between steps, where errors compound and partial credit requires examining intermediate work rather than just the final answer.

The competencies under test are:
- Correct marginalization from a joint three-variable table
- Correct formulation of conditional entropy as a weighted average (not arithmetic mean)
- Handling of zero-probability events without breaking the computation
- Maintaining numerical precision through repeating-decimal intermediate values
- Correctly interpreting a negative decomposition result rather than flagging it as an error

This task is **deterministic**: there is one correct answer for each quantity. The challenge is procedural and numerical, not definitional.

---

## Step-Level Answer Key

Use this table for rapid verification against a model's intermediate outputs:

| Quantity | Correct value | Tolerance |
|---|---|---|
| $P(y_1), P(y_2), P(y_3)$ | 0.40, 0.30, 0.30 | exact |
| $H(Y)$ | 1.5710 bits | ±0.001 |
| $P(x_1), P(x_2)$ | 0.50, 0.50 | exact |
| $P(Y \mid x_1)$ | (0.36, 0.24, 0.40) | ±0.001 |
| $P(Y \mid x_2)$ | (0.44, 0.36, 0.20) | ±0.001 |
| $H(Y \mid x_1)$ | 1.5535 bits | ±0.001 |
| $H(Y \mid x_2)$ | 1.5161 bits | ±0.001 |
| $H(Y \mid X)$ | 1.5348 bits | ±0.001 |
| $I(X; Y)$ | 0.0361 bits | ±0.001 |
| $P(z_1), P(z_2)$ | 0.40, 0.60 | exact |
| $P(x_1 \mid z_1), P(x_2 \mid z_1)$ | 0.75, 0.25 | ±0.001 |
| $P(x_1 \mid z_2), P(x_2 \mid z_2)$ | 0.3333, 0.6667 | ±0.001 |
| $H(Y \mid z_1)$ | 1.5589 bits | ±0.001 |
| $H(Y \mid z_2)$ | 1.5656 bits | ±0.001 |
| $H(Y \mid Z)$ | 1.5629 bits | ±0.001 |
| $H(Y \mid x_1, z_1)$ | 1.5656 bits | ±0.001 |
| $H(Y \mid x_1, z_2)$ | 1.4855 bits | ±0.001 |
| $H(Y \mid x_2, z_1)$ | 0.9710 bits | ±0.001 |
| $H(Y \mid x_2, z_2)$ | 1.5395 bits | ±0.001 |
| $H(Y \mid X, Z)$ | 1.4797 bits | ±0.001 |
| $I(X; Y \mid Z)$ | 0.0832 bits | ±0.001 |
| $I(X; Z \to Y)$ | −0.0471 bits | ±0.001 |

---

## Scoring Philosophy

### Credit propagation

Steps build on each other. When a model makes an error at Step $k$ but correctly applies the Step $k+1$ formula to its (incorrect) intermediate, that model should receive:
- **No credit** on the rubric criterion for the erroneous intermediate
- **Full credit** on the rubric criterion for the correctly-applied formula in the next step
- Evaluators should note the cascade explicitly rather than treating a wrong final answer as a single undifferentiated failure

### Where models most often fail

Ranked by observed frequency of error:

1. **Step 4 weighting** (EC-05) — Using $P(x) \cdot P(z)$ instead of $P(x, z)$ as weights in assembling $H(Y \mid X, Z)$. This is the single most common conceptual error and produces a plausible-looking but incorrect $H(Y \mid X, Z)$.

2. **Step 5 interpretation** (EC-02) — Treating the negative result as an error. Models sometimes re-derive the decomposition formula, invert the subtraction order, or report $|I(X; Z \to Y)|$ instead of the signed value.

3. **Zero entry handling** (EC-01) — Either crashing on the zero probability or silently dropping the $(x_2, y_3, z_1)$ entry from the support of $P(Y \mid x_2, z_1)$ and computing a binary entropy instead of a ternary one.

4. **Premature rounding** (EC-04) — Truncating $1/3$ and $2/3$ to 2–3 decimal places early in Step 4, causing a precision cascade.

5. **Unweighted average confusion** (EC-03) — Relying on the symmetric $P(X)$ coincidence without stating the general formula.

---

## Calibration Anchors

Use these as fixed scoring points across multiple responses:

| Criterion | Definite full credit | Definite zero credit |
|---|---|---|
| R14 (zero convention) | $H(Y\|x_2,z_1)$ reported as 0.9710 with formula shown | Model reports "undefined" or crashes |
| R17 (negative component) | $-0.0471$ reported with correct formula | Any positive value reported for $I(X;Z→Y)$ |
| R19 (interpretation) | Mentions suppressor effect, Simpson's paradox, or confounding direction | Calls negative value a rounding error |
| R15 (assembly weights) | Weights shown as 0.30, 0.10, 0.20, 0.40 | Weights shown as 0.20, 0.20, 0.30, 0.30 (independence assumption) |

---

## Score Interpretation Guide

| Total score (%) | Interpretation |
|---|---|
| 88–100 | Correct throughout; correctly handles zero entry, negative decomposition, and interpretation |
| 72–87 | One step-level error (typically weighting or precision) without propagating cascade |
| 55–71 | Two step-level errors, or one with significant cascade, or correct numerics but wrong interpretation of negative result |
| 38–54 | Multiple errors including at least one fundamental formula error (wrong conditional entropy formula or wrong decomposition structure) |
| < 38 | Unable to complete the marginalization chain correctly, or systematic formula confusion |

---

## Design Notes for Future Task Variants

This task was designed with the following properties:

- **Non-symmetric marginals for $Z$** ($P(z_1)=0.40, P(z_2)=0.60$) prevent shortcutting the $H(Y \mid Z)$ weighted average.
- **Symmetric marginals for $X$** ($P(x_1)=P(x_2)=0.50$) create the coincidence in EC-03, which tests whether models understand the formula or just execute the arithmetic.
- **Negative correlation between $X$ and $Z$** produces the negative decomposition in Step 5, which is the primary conceptual challenge.
- **One zero probability entry** at $(x_2, y_3, z_1)$ tests the zero convention without making the computation ill-defined.
- **Repeating decimals** in $P(X \mid Z)$ create a controlled precision stress test.

To create a simpler variant, replace the table with one where $X \perp Z$ (making the decomposition positive and the weights in Step 4 trivial). To create a harder variant, add a fourth value of $Y$ or use a $3 \times 3 \times 2$ joint table.

---

## Difficulty Assessment

**Estimated difficulty:** High  
**Primary challenge:** Correct weighting in H(Y|X,Z) assembly and interpretation of negative decomposition component  
**Secondary challenge:** Zero-probability convention and precision maintenance through repeating decimals  
**Determinism type:** Fully deterministic — unique correct answer at every step  
**Error propagation risk:** High — errors in Steps 1–3 propagate through Steps 4–5; errors in Step 4 margin computations propagate to all of Step 4 and Step 5
