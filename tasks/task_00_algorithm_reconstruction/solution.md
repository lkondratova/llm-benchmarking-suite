# Reference Solution

## Step 1 — Parse Structures and Identify Base Pairs

Sequence: `x = "GCAUCG"` → positions 1:G, 2:C, 3:A, 4:U, 5:C, 6:G

Dot-bracket parsing uses a stack: `(` pushes the index, `)` pops and records a pair.

| Structure | Pairs | Unpaired positions |
|---|---|---|
| y1 = `((..))`  | (1,6), (2,5) | 3, 4 |
| y2 = `(.....)`  | (1,6) | 2, 3, 4, 5 |
| y3 = `..()..`  | (3,4) | 1, 2, 5, 6 |
| y4 = `.(().)`  | (2,6), (3,4) | 1, 5 |
| y5 = `.(.()).` | (2,5), (4,6) | 1, 3, 7* |

> **Note on y5:** The structure `.(.()).` has 7 characters but the sequence has 6. This is a malformed candidate — it cannot be validly aligned to the sequence. A well-formed evaluation should flag this and either exclude it or treat it as an error. In the reference solution below, y5 is evaluated as written assuming the 7th position is an additional unpaired nucleotide not present in the sequence; however, the recommended handling is to **exclude y5** (see `edge_cases.md`).

---

## Step 2 — Classify Base Pairs

Sequence: G(1) C(2) A(3) U(4) C(5) G(6)

| Pair | Nucleotides | Type | Energy |
|---|---|---|---|
| (1,6) | G–G | other | −1 |
| (2,5) | C–C | other | −1 |
| (3,4) | A–U | AU | −2 |
| (2,6) | C–G | GC | −3 |
| (4,6) | U–G | GU | −2 |

---

## Step 3 — Compute Free Energies

$$\Delta G(y) = \sum_{\text{pairs}} E_{\text{pair}} + \sum_{\text{unpaired}} (+1)$$

**y1** = `((..))`  
Pairs: (1,6) → −1, (2,5) → −1. Unpaired: 2 positions → +2  
$$\Delta G(y_1) = -1 - 1 + 2 = 0$$

**y2** = `(....)`  
Pairs: (1,6) → −1. Unpaired: 4 → +4  
$$\Delta G(y_2) = -1 + 4 = +3$$

**y3** = `..()..`  
Pairs: (3,4) → −2. Unpaired: 4 → +4  
$$\Delta G(y_3) = -2 + 4 = +2$$

**y4** = `.(().)`  
Pairs: (2,6) → −3, (3,4) → −2. Unpaired: 2 → +2  
$$\Delta G(y_4) = -3 - 2 + 2 = -3$$

**y5** — excluded due to length mismatch (see edge_cases.md).

---

## Step 4 — Compute Weights

$$w(y_i) = e^{-\Delta G(y_i)}$$

| Structure | ΔG | Weight |
|---|---|---|
| y1 | 0 | $e^0 = 1.0000$ |
| y2 | +3 | $e^{-3} \approx 0.0498$ |
| y3 | +2 | $e^{-2} \approx 0.1353$ |
| y4 | −3 | $e^{+3} \approx 20.0855$ |

---

## Step 5 — Partition Function

$$Z = 1.0000 + 0.0498 + 0.1353 + 20.0855 = 21.2706$$

---

## Step 6 — Normalized Probabilities

$$p(y_i) = \frac{w(y_i)}{Z}$$

| Structure | Probability |
|---|---|
| y1 | $1.0000 / 21.2706 \approx 0.0470$ |
| y2 | $0.0498 / 21.2706 \approx 0.0023$ |
| y3 | $0.1353 / 21.2706 \approx 0.0064$ |
| y4 | $20.0855 / 21.2706 \approx 0.9443$ |

Sum ≈ 1.000 ✓

---

## Step 7 — Marginal Probability of Pair (1,6)

Structures containing pair (1,6): **y1**, **y2**

$$p(1,6) = p(y_1) + p(y_2) \approx 0.0470 + 0.0023 = 0.0493$$

---

## Final Answer

$$\boxed{p(1,6) \approx 0.049}$$

---

## Implementation Notes

The reference code (not provided here) should:
1. Parse dot-bracket strings using a stack-based algorithm.
2. Look up base identities from the sequence string.
3. Map pairs to energy values with a lookup function.
4. Compute weights with `math.exp(-energy)`.
5. Normalize and accumulate probabilities for the target pair.

Common bug: using 0-based indexing internally but 1-based in output — validate with a known pair before reporting.
