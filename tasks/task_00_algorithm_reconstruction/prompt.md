# Task: Probabilistic Ensemble Scoring over Structured Outputs

## Background

Many problems in computational biology and natural language processing require reasoning over an **ensemble of candidate structures** rather than committing to a single prediction. A standard approach assigns each candidate structure a score based on a domain-specific energy model, then interprets these scores as defining a probability distribution over the candidate set via the Boltzmann weighting scheme.

This task asks you to evaluate such a model for a short RNA sequence.

---

## Input

**Sequence:**
```
x = "GCAUCG"
```

**Candidate structures** (dot-bracket notation — `.` = unpaired, `(` / `)` = paired positions):
```
y1 = "((..))"
y2 = "(....)"
y3 = "..().."
y4 = ".(().)
y5 = ".(.())."
```

---

## Energy Model

Compute the free energy $\Delta G$ of each structure as the sum of contributions from all positions:

| Position type | Contribution |
|---|---|
| Unpaired nucleotide | +1 |
| GC base pair | −3 |
| AU or GU base pair | −2 |
| Any other base pair | −1 |

Use the following physical constants:
```
R = 1
T = 1
```

---

## Tasks

1. **Parse** each dot-bracket structure and identify all base pairs and unpaired positions.
2. **Compute** the free energy $\Delta G(y_i)$ for each candidate structure.
3. **Compute** the unnormalized weight for each structure:
$$w(y_i) = e^{-\Delta G(y_i) \;/\; (RT)}$$
4. **Compute** the partition function:
$$Z = \sum_{i} w(y_i)$$
5. **Compute** the normalized probability of each structure:
$$p(y_i) = \frac{w(y_i)}{Z}$$
6. **Compute** the marginal probability that nucleotide 1 is paired with nucleotide 6 across the full distribution:
$$p(1,6) = \sum_{i \;:\; (1,6) \in y_i} p(y_i)$$

## Output
float - rounded to 3 decimal places