# Edge Cases

---

## EC-01 — Zero-probability entry: $P(x_2, y_3, z_1) = 0.00$

**Location:** The joint table contains exactly one zero entry at $(x_2, y_3, z_1)$.

**Impact:** This entry propagates to $P(y_3 \mid X=x_2, Z=z_1) = 0$, which must be handled using the convention $0 \log_2 0 = 0$ when computing $H(Y \mid x_2, z_1)$.

**Correct result:** $H(Y \mid x_2, z_1) = -0.40\log_2(0.40) - 0.60\log_2(0.60) - 0 = 0.9710$ bits. This is the lowest per-cell conditional entropy in the table and is the most distinctive value in Step 4 — an error here is likely to propagate significantly.

**Common errors:**
- Treating $0 \log_2 0$ as undefined and reporting an error or skipping the term
- Treating $0 \log_2 0 = 0$ correctly but then incorrectly computing the distribution as only two outcomes (misidentifying the support), resulting in a binary entropy rather than a ternary one
- Forgetting the zero entry exists when computing $P(Y, Z)$ or $P(Y \mid Z=z_1)$, since the zero does not affect those marginals but models may drop the row from consideration

**Evaluator note:** If a model arrives at $H(Y \mid x_2, z_1) \approx 1.0$ bits and provides the correct binary entropy formula, this suggests it correctly applied the zero convention but may have mischaracterized the entropy as binary. The distribution is ternary; binary framing of a ternary distribution is a conceptual error even if the numerical result happens to match.

---

## EC-02 — Negative decomposition component $I(X; Z \to Y) = -0.0471$ bits

**What happens:** The chain rule decomposition yields a negative value for the confounding term $I(X; Z \to Y)$.

**Why it is correct:** Negative values of $I(X; Y) - I(X; Y \mid Z)$ are mathematically valid. They indicate that conditioning on $Z$ *increases* the observed mutual information between $X$ and $Y$ — the marginal association is weaker than the conditional association. This is the information-theoretic signature of a **negative confounder** (also called a **suppressor variable** or a structure analogous to Simpson's paradox).

**Structural explanation:** In this table, $X$ and $Z$ are negatively associated — in $z_1$, $x_1$ predominates (75/25 split); in $z_2$, $x_2$ predominates (33/67 split). Simultaneously, $Z$ shifts the distribution of $Y$. These two effects partially cancel at the margin, compressing the marginal $I(X;Y)$ below the within-stratum value.

**Common errors:**
- Reporting a positive value and claiming the decomposition is correct (sign error in the subtraction)
- Flagging the result as a computation error and re-running the calculation
- Reporting the correct numerical value but interpreting it as a rounding artifact
- Asserting that mutual information cannot be negative (true for mutual information itself; not true for the *difference* of two mutual information quantities used in decomposition)

**Evaluator note:** A model that correctly computes $-0.0471$ and then correctly explains the Simpson's paradox interpretation should receive full credit on R17 and R19. A model that computes $-0.0471$ but calls it an error receives partial credit on R17 only. A model that suppresses the negative sign and reports $+0.0471$ should not receive credit on R17.

---

## EC-03 — Coincidence: $P(x_1) = P(x_2) = 0.50$

**What happens:** Both values of $X$ are equally likely, so the weighted average in Step 2 simplifies to an arithmetic average.

**Risk:** A model may compute $H(Y \mid X)$ as $\frac{1}{2}[H(Y|x_1) + H(Y|x_2)]$ without stating the weighted formula, and arrive at the correct numerical answer. This is numerically correct but hides whether the model understands the general formula.

**Evaluator note:** If a model presents an unweighted average and gets the right number, check whether the formula $H(Y|X) = \sum_x P(x) H(Y|X=x)$ is stated explicitly. If it is stated, award full credit on R08. If only the arithmetic average is shown without reference to the general formula, award partial credit — the model may be exploiting the coincidence rather than reasoning correctly.

---

## EC-04 — Repeating decimal values in $P(X \mid Z)$ and $P(Y \mid Z)$

**What happens:** $P(x_1 \mid z_2) = 1/3$, $P(x_2 \mid z_2) = 2/3$, $P(y_2 \mid z_2) = 4/15$, $P(y_3 \mid z_2) = 1/3$ are all non-terminating decimals.

**Impact:** Models that round these prematurely (e.g., to $0.33$ rather than carrying $1/3$) will accumulate rounding error in $H(Y \mid Z)$ and downstream quantities.

**Correct handling:** Carry fractions symbolically where possible, or use sufficient decimal places (at least 6). A final answer of $H(Y \mid Z)$ between $1.562$ and $1.564$ is within tolerance. Values outside this range indicate premature rounding.

**Evaluator note:** This is a precision test disguised as an arithmetic step. Models that show $P(x_1 \mid z_2) = 0.33$ (two decimal places) should be flagged for potential precision loss, even if their final answer happens to be within tolerance.

---

## EC-05 — Marginalizing in the wrong order for $H(Y \mid X, Z)$

**What happens:** To compute $H(Y \mid X, Z)$, a model must maintain the three-variable joint table rather than working from two-variable marginals. The correct formula requires the weight $P(z) \cdot P(x \mid z) = P(x, z)$, not $P(x) \cdot P(z)$ (which would only be correct if $X$ and $Z$ were independent, which they are not).

**Correct weights:** $(x_1, z_1): 0.30$; $(x_2, z_1): 0.10$; $(x_1, z_2): 0.20$; $(x_2, z_2): 0.40$

**Incorrect weights (assuming independence):** $(x_1, z_1): 0.20$; $(x_2, z_1): 0.20$; $(x_1, z_2): 0.30$; $(x_2, z_2): 0.30$

**Numerical consequence:** Using incorrect weights changes $H(Y \mid X, Z)$ by approximately $0.02$–$0.04$ bits, placing it outside the tolerance band. The error propagates to $I(X; Y \mid Z)$ and the final decomposition.

**Evaluator note:** Check the four weights in the assembly table explicitly. If they sum to 1.0 but do not match the correct values, the model has assumed $X \perp Z$.

---

## EC-06 — Confusing $I(X; Y \mid Z)$ with $H(Y \mid X, Z)$

**What happens:** Some models may conflate the conditional mutual information $I(X; Y \mid Z)$ with the conditional entropy $H(Y \mid X, Z)$. These are different quantities.

$$I(X; Y \mid Z) = H(Y \mid Z) - H(Y \mid X, Z) = 1.5629 - 1.4797 = 0.0832$$
$$H(Y \mid X, Z) = 1.4797 \text{ (a much larger value)}$$

**Evaluator note:** If a model reports $I(X; Y \mid Z) \approx 1.48$ bits, it has confused these two quantities. Award R12 and R15 as appropriate for correct intermediate computations, but not R16.

---

## EC-07 — Incorrect formula for $I(X; Z \to Y)$

**What happens:** The notation $I(X; Z \to Y)$ is defined in the prompt as $I(X; Y) - I(X; Y \mid Z)$. This is not a standard notation across all textbooks — some sources use related but distinct quantities.

**Common substitutions:**
- A model may compute $I(X; Z)$ (mutual information between $X$ and $Z$) instead of the defined term — this is a different quantity not asked for
- A model may compute $I(Z; Y)$ instead — also a different quantity
- A model may attempt to compute the interaction information $I(X; Y; Z)$, which equals $I(X; Y) - I(X; Y \mid Z)$ and is the same as $I(X; Z \to Y)$ as defined here — this is correct and should receive full credit

**Evaluator note:** Credit R17 only if the reported value matches $-0.0471$ bits (tolerance ±0.001), regardless of the formula path taken. Do not penalize for using the interaction information identity if the result is correct.
