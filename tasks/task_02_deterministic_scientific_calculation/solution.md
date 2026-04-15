# Reference Solution

All values computed with $\log_2$. Define $0 \log_2 0 = 0$. Final answers reported to 4 decimal places; intermediate values shown to 6.

---

## Verification: Table sums to 1.0

$$\sum_{x,y,z} P(x,y,z) = 0.12 + 0.08 + 0.10 + 0.04 + 0.06 + 0.00 + 0.06 + 0.04 + 0.10 + 0.18 + 0.12 + 0.10 = 1.00 \checkmark$$

---

## Step 1 — Marginal distribution of $Y$ and $H(Y)$

Marginalize over $X$ and $Z$:

| $Y$ | $P(Y)$ | Calculation |
|-----|--------|-------------|
| $y_1$ | $0.12+0.04+0.06+0.18 = 0.40$ | |
| $y_2$ | $0.08+0.06+0.04+0.12 = 0.30$ | |
| $y_3$ | $0.10+0.00+0.10+0.10 = 0.30$ | |

$$H(Y) = -0.40\log_2(0.40) - 0.30\log_2(0.30) - 0.30\log_2(0.30)$$
$$= 0.528771 + 0.521090 + 0.521090$$

$$\boxed{H(Y) = 1.5710 \text{ bits}}$$

---

## Step 2 — Conditional distribution $P(Y \mid X)$ and $H(Y \mid X)$

**Marginal $P(X, Y)$** (sum over $Z$):

| | $y_1$ | $y_2$ | $y_3$ | $P(X)$ |
|---|---|---|---|---|
| $x_1$ | $0.12+0.06=0.18$ | $0.08+0.04=0.12$ | $0.10+0.10=0.20$ | **0.50** |
| $x_2$ | $0.04+0.18=0.22$ | $0.06+0.12=0.18$ | $0.00+0.10=0.10$ | **0.50** |

$P(x_1) = P(x_2) = 0.50$

**Conditional distribution** $P(Y \mid X = x) = P(X,Y) / P(X)$:

| $Y$ | $P(Y \mid x_1)$ | $P(Y \mid x_2)$ |
|-----|-----------------|-----------------|
| $y_1$ | $0.18 / 0.50 = 0.36$ | $0.22 / 0.50 = 0.44$ |
| $y_2$ | $0.12 / 0.50 = 0.24$ | $0.18 / 0.50 = 0.36$ |
| $y_3$ | $0.20 / 0.50 = 0.40$ | $0.10 / 0.50 = 0.20$ |

**Per-condition entropies:**

$$H(Y \mid X = x_1) = -0.36\log_2(0.36) - 0.24\log_2(0.24) - 0.40\log_2(0.40) = 1.553521 \text{ bits}$$

$$H(Y \mid X = x_2) = -0.44\log_2(0.44) - 0.36\log_2(0.36) - 0.20\log_2(0.20) = 1.516148 \text{ bits}$$

**Weighted average** (weights are $P(X)$, not $1/2$ assumed — confirm they equal $0.5$ each):

$$H(Y \mid X) = 0.50 \times 1.553521 + 0.50 \times 1.516148$$

$$\boxed{H(Y \mid X) = 1.5348 \text{ bits}}$$

> **Common error:** Using a simple average of the two conditional entropies is only correct here because $P(x_1) = P(x_2) = 0.5$. In general, the weighted average $\sum_x P(x) \cdot H(Y|X=x)$ must be used.

---

## Step 3 — Mutual information $I(X; Y)$

$$I(X; Y) = H(Y) - H(Y \mid X) = 1.570951 - 1.534834$$

$$\boxed{I(X; Y) = 0.0361 \text{ bits}}$$

$X$ and $Y$ share very little marginal information — knowing $X$ reduces uncertainty about $Y$ by only ~2.3%.

---

## Step 4 — Conditional mutual information $I(X; Y \mid Z)$

### 4a — Marginals involving $Z$

**$P(Z)$** (sum over $X$ and $Y$):

$$P(z_1) = 0.12+0.08+0.10+0.04+0.06+0.00 = 0.40$$
$$P(z_2) = 0.06+0.04+0.10+0.18+0.12+0.10 = 0.60$$

**$P(X, Z)$**:

| | $z_1$ | $z_2$ |
|---|---|---|
| $x_1$ | $0.12+0.08+0.10=0.30$ | $0.06+0.04+0.10=0.20$ |
| $x_2$ | $0.04+0.06+0.00=0.10$ | $0.18+0.12+0.10=0.40$ |

**$P(X \mid Z)$**:

| | $z_1$ | $z_2$ |
|---|---|---|
| $P(x_1 \mid z)$ | $0.30/0.40 = 0.75$ | $0.20/0.60 = 0.3\overline{3}$ |
| $P(x_2 \mid z)$ | $0.10/0.40 = 0.25$ | $0.40/0.60 = 0.\overline{6}$ |

**$P(Y, Z)$**:

| | $z_1$ | $z_2$ |
|---|---|---|
| $y_1$ | $0.12+0.04=0.16$ | $0.06+0.18=0.24$ |
| $y_2$ | $0.08+0.06=0.14$ | $0.04+0.12=0.16$ |
| $y_3$ | $0.10+0.00=0.10$ | $0.10+0.10=0.20$ |

**$P(Y \mid Z)$**:

| | $z_1$ | $z_2$ |
|---|---|---|
| $y_1$ | $0.16/0.40 = 0.40$ | $0.24/0.60 = 0.40$ |
| $y_2$ | $0.14/0.40 = 0.35$ | $0.16/0.60 = 0.2\overline{6}$ |
| $y_3$ | $0.10/0.40 = 0.25$ | $0.20/0.60 = 0.\overline{3}$ |

### 4b — $H(Y \mid Z)$

$$H(Y \mid Z=z_1) = -0.40\log_2(0.40) - 0.35\log_2(0.35) - 0.25\log_2(0.25) = 1.558872 \text{ bits}$$

$$H(Y \mid Z=z_2) = -0.40\log_2(0.40) - 0.2\overline{6}\log_2(0.2\overline{6}) - 0.\overline{3}\log_2(0.\overline{3}) = 1.565596 \text{ bits}$$

$$H(Y \mid Z) = 0.40 \times 1.558872 + 0.60 \times 1.565596 = 0.623549 + 0.939358$$

$$\boxed{H(Y \mid Z) = 1.5629 \text{ bits}}$$

### 4c — $P(Y \mid X, Z)$ and $H(Y \mid X, Z)$

For each $(x, z)$ cell, divide joint by $P(X=x, Z=z)$:

**(x₁, z₁):** $P(X=x_1, Z=z_1) = 0.30$

| $y$ | $P(x_1, y, z_1)$ | $P(y \mid x_1, z_1)$ |
|---|---|---|
| $y_1$ | 0.12 | $0.12/0.30 = 0.40$ |
| $y_2$ | 0.08 | $0.08/0.30 = 0.2\overline{6}$ |
| $y_3$ | 0.10 | $0.10/0.30 = 0.\overline{3}$ |

$$H(Y \mid x_1, z_1) = 1.565596 \text{ bits}$$

**(x₁, z₂):** $P(X=x_1, Z=z_2) = 0.20$

| $y$ | $P(x_1, y, z_2)$ | $P(y \mid x_1, z_2)$ |
|---|---|---|
| $y_1$ | 0.06 | $0.06/0.20 = 0.30$ |
| $y_2$ | 0.04 | $0.04/0.20 = 0.20$ |
| $y_3$ | 0.10 | $0.10/0.20 = 0.50$ |

$$H(Y \mid x_1, z_2) = -0.30\log_2(0.30) - 0.20\log_2(0.20) - 0.50\log_2(0.50) = 1.485475 \text{ bits}$$

**(x₂, z₁):** $P(X=x_2, Z=z_1) = 0.10$

| $y$ | $P(x_2, y, z_1)$ | $P(y \mid x_2, z_1)$ |
|---|---|---|
| $y_1$ | 0.04 | $0.04/0.10 = 0.40$ |
| $y_2$ | 0.06 | $0.06/0.10 = 0.60$ |
| $y_3$ | 0.00 | $0.00/0.10 = 0.00$ |

$$H(Y \mid x_2, z_1) = -0.40\log_2(0.40) - 0.60\log_2(0.60) - 0 = 0.970951 \text{ bits}$$

> **Note:** $P(y_3 \mid x_2, z_1) = 0$ contributes $0 \log_2 0 = 0$ by convention. This is the only zero-probability entry in the joint table and must be handled explicitly.

**(x₂, z₂):** $P(X=x_2, Z=z_2) = 0.40$

| $y$ | $P(x_2, y, z_2)$ | $P(y \mid x_2, z_2)$ |
|---|---|---|
| $y_1$ | 0.18 | $0.18/0.40 = 0.45$ |
| $y_2$ | 0.12 | $0.12/0.40 = 0.30$ |
| $y_3$ | 0.10 | $0.10/0.40 = 0.25$ |

$$H(Y \mid x_2, z_2) = -0.45\log_2(0.45) - 0.30\log_2(0.30) - 0.25\log_2(0.25) = 1.539491 \text{ bits}$$

### 4d — Assemble $H(Y \mid X, Z)$

$$H(Y \mid X, Z) = \sum_{x,z} P(z) \cdot P(x \mid z) \cdot H(Y \mid X=x, Z=z)$$

| $(x, z)$ | $P(z)$ | $P(x \mid z)$ | Weight | $H(Y \mid x, z)$ | Contribution |
|---|---|---|---|---|---|
| $(x_1, z_1)$ | 0.40 | 0.75 | 0.3000 | 1.565596 | 0.469679 |
| $(x_2, z_1)$ | 0.40 | 0.25 | 0.1000 | 0.970951 | 0.097095 |
| $(x_1, z_2)$ | 0.60 | $1/3$ | 0.2000 | 1.485475 | 0.297095 |
| $(x_2, z_2)$ | 0.60 | $2/3$ | 0.4000 | 1.539491 | 0.615796 |
| | | | **1.0000** | | **1.479665** |

$$\boxed{H(Y \mid X, Z) = 1.4797 \text{ bits}}$$

### 4e — $I(X; Y \mid Z)$

$$I(X; Y \mid Z) = H(Y \mid Z) - H(Y \mid X, Z) = 1.562906 - 1.479665$$

$$\boxed{I(X; Y \mid Z) = 0.0832 \text{ bits}}$$

---

## Step 5 — Decomposition of $I(X; Y)$

$$I(X; Z \to Y) = I(X; Y) - I(X; Y \mid Z) = 0.036116 - 0.083241$$

$$\boxed{I(X; Z \to Y) = -0.0471 \text{ bits}}$$

**Verification (chain rule):**
$$I(X; Y \mid Z) + I(X; Z \to Y) = 0.083241 + (-0.047125) = 0.036116 = I(X; Y) \checkmark$$

### Interpretation

The negative value of $I(X; Z \to Y)$ is a valid and meaningful result — it is not a computation error.

It indicates that **conditioning on $Z$ increases the apparent association between $X$ and $Y$**. Marginally, $X$ and $Y$ share only 0.0361 bits. But within each stratum of $Z$, the conditional mutual information rises to 0.0832 bits. This is the information-theoretic analogue of **Simpson's paradox**: $X$ and $Z$ are negatively correlated (in $z_1$, $x_1$ dominates; in $z_2$, $x_2$ dominates), and $Z$ itself is associated with $Y$ (the conditional distributions $P(Y \mid Z=z_1)$ and $P(Y \mid Z=z_2)$ differ). When these two associations partially cancel at the margin, the marginal $I(X;Y)$ is suppressed below the within-group $I(X;Y \mid Z)$.

In practical terms: a naive analysis that ignores $Z$ would underestimate how informative $X$ is about $Y$ within any given group. The decomposition reveals that $X$'s marginal information about $Y$ is **negative after accounting for confounding** — meaning the confound $Z$ was actively masking the direct signal.

---

## Summary of Results

| Quantity | Value (bits) |
|---|---|
| $H(Y)$ | **1.5710** |
| $H(Y \mid X)$ | **1.5348** |
| $I(X; Y)$ | **0.0361** |
| $H(Y \mid Z)$ | **1.5629** |
| $H(Y \mid X, Z)$ | **1.4797** |
| $I(X; Y \mid Z)$ | **0.0832** |
| $I(X; Z \to Y)$ | **−0.0471** |
