# Task: Conditional Entropy Decomposition with a Latent Variable

## Background

Shannon entropy quantifies uncertainty in a probability distribution. When multiple variables are involved, information can be partitioned — decomposed into components that describe how much uncertainty remains after observing one variable, and how much is shared between variables. This decomposition has applications in feature selection, causal inference, and model evaluation wherever understanding the *structure* of information matters, not just its total quantity.

This task requires you to work through a sequence of entropy calculations over a joint distribution involving three discrete variables. Each step builds on the previous one, and errors compound — show your intermediate results explicitly.

---

## Setup

You are given a joint probability table over three discrete variables:

- $X \in \{x_1, x_2\}$ — an observed input  
- $Y \in \{y_1, y_2, y_3\}$ — an observed output  
- $Z \in \{z_1, z_2\}$ — a latent grouping variable (never directly observed)

**Joint distribution** $P(X, Y, Z)$:

| $X$ | $Y$ | $Z$ | $P(X, Y, Z)$ |
|-----|-----|-----|---------------|
| $x_1$ | $y_1$ | $z_1$ | 0.12 |
| $x_1$ | $y_2$ | $z_1$ | 0.08 |
| $x_1$ | $y_3$ | $z_1$ | 0.10 |
| $x_2$ | $y_1$ | $z_1$ | 0.04 |
| $x_2$ | $y_2$ | $z_1$ | 0.06 |
| $x_2$ | $y_3$ | $z_1$ | 0.00 |
| $x_1$ | $y_1$ | $z_2$ | 0.06 |
| $x_1$ | $y_2$ | $z_2$ | 0.04 |
| $x_1$ | $y_3$ | $z_2$ | 0.10 |
| $x_2$ | $y_1$ | $z_2$ | 0.18 |
| $x_2$ | $y_2$ | $z_2$ | 0.12 |
| $x_2$ | $y_3$ | $z_2$ | 0.10 |

Verify that the table sums to 1.0 before proceeding.

---

## Tasks

Work through the following steps in order. Use $\log_2$ throughout. Define $0 \log_2 0 = 0$.

### Step 1 — Marginal distribution of $Y$

Marginalize out $X$ and $Z$ to obtain $P(Y)$.  
Compute the marginal entropy:
$$H(Y) = -\sum_{y} P(y) \log_2 P(y)$$

### Step 2 — Conditional distribution $P(Y \mid X)$

Marginalize out $Z$ to obtain $P(X, Y)$, then derive $P(X)$ and $P(Y \mid X)$.  
Compute the conditional entropy:
$$H(Y \mid X) = \sum_{x} P(x) \cdot H(Y \mid X = x)$$
where $H(Y \mid X = x) = -\sum_{y} P(y \mid x) \log_2 P(y \mid x)$.

### Step 3 — Mutual information $I(X; Y)$

Using your results from Steps 1 and 2:
$$I(X; Y) = H(Y) - H(Y \mid X)$$

### Step 4 — Conditional mutual information $I(X; Y \mid Z)$

Compute the conditional entropy $H(Y \mid X, Z)$ by working within each slice of $Z$:
$$H(Y \mid X, Z) = \sum_{z} P(z) \cdot H(Y \mid X, Z = z)$$
where $H(Y \mid X, Z = z) = \sum_{x} P(x \mid z) \cdot H(Y \mid X = x, Z = z)$.

Then compute:
$$I(X; Y \mid Z) = H(Y \mid Z) - H(Y \mid X, Z)$$

You will need $H(Y \mid Z)$ as an intermediate — compute it explicitly.

### Step 5 — Decompose $I(X; Y)$

Use the **chain rule for mutual information**:
$$I(X; Y) = I(X; Y \mid Z) + I(X; Z \to Y)$$

where $I(X; Z \to Y)$ is the reduction in uncertainty about $Y$ attributable to the association between $X$ and $Z$, computed as:
$$I(X; Z \to Y) = I(X; Y) - I(X; Y \mid Z)$$

Report both components and interpret: does $X$ explain $Y$ primarily through its own direct association, or through its correlation with the latent grouping $Z$?

---

## Output Format

Show all intermediate probability tables, entropy calculations, and running totals. Round final values to 4 decimal places. Use clearly labeled subsections for each step.

This task has a unique correct answer. Numerical tolerance for grading is ±0.001 bits.
