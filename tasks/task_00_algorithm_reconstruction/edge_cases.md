# Edge Cases

This document describes known edge cases and ambiguities in the task, along with recommended handling for evaluators.

---

## EC-01 — Length mismatch in y5

**Structure:** `y5 = ".(.())."`  
**Issue:** This structure has 7 characters, but the sequence `"GCAUCG"` has only 6 nucleotides.  
**Impact:** The structure cannot be validly aligned to the sequence. Any pair or unpaired position beyond index 6 references a non-existent nucleotide.

**Recommended handling:**
- A model that **silently processes y5** as if the 7th character were valid should receive **no credit** for R16 and partial credit reductions on R17/R18.
- A model that **flags the mismatch and excludes y5** is behaving correctly.
- A model that **flags the mismatch but still attempts to score y5** with a stated assumption (e.g., treating position 7 as an extra unpaired nucleotide) may receive partial credit for R16 if clearly reasoned.
- **Note:** The original source material did include y5 in its calculation with the value ΔG = −1 and weight ≈ 2.718. This was an error in the source. The canonical solution excludes y5.

---

## EC-02 — Non-canonical base pairs

**Pairs affected:** (1,6) = G–G, (2,5) = C–C  
**Issue:** These pairs do not fall into GC, AU, or GU categories, so they use the "other" rule (−1).  
**Common error:** Models trained on Watson–Crick pairing may incorrectly classify G–G as GC (scoring −3) or refuse to score it.  
**Recommended handling:** Only the "other" rule (−1) is correct here per the stated energy model. No additional biological knowledge should override the explicit rules.

---

## EC-03 — Indexing convention

**Issue:** Dot-bracket indexing is conventionally 1-based in the prompt (nucleotide 1 through 6). Internal computation may use 0-based indices.  
**Common error:** Off-by-one errors when reporting pairs, e.g., reporting pair (0,5) instead of (1,6) in the final answer.  
**Recommended handling:** The final answer must use 1-based indexing to match the prompt. Internal indexing is acceptable as long as it is consistent and the output is correctly translated.

---

## EC-04 — Zero-energy structure

**Structure:** y1, with ΔG = 0  
**Issue:** A model that confuses "zero energy" with "zero weight" would incorrectly assign w(y1) = 0 instead of w(y1) = exp(0) = 1.  
**Recommended handling:** This is a fundamental conceptual error. Credit for R07 and all downstream steps should be withheld if this mistake is present.

---

## EC-05 — Partition function scope

**Issue:** If y5 is incorrectly included, the partition function Z changes from ~21.27 to ~23.99, shifting all probabilities.  
**Impact on final answer:** Including y5 raises Z, which lowers p(y1) and p(y2), changing p(1,6) from ~0.049 to ~0.044.  
**Recommended handling:** Score the full pipeline internally consistent with the model's own treatment of y5. Deduct points only at R16 for the initial mishandling.

---

## EC-06 — Symmetric pair classification

**Issue:** The energy lookup must be symmetric: GC and CG, AU and UA, GU and UG should all be recognized.  
**Common error:** A model may only check pair in one orientation (e.g., G–C but not C–G).  
**Recommended handling:** If a model incorrectly classifies a pair due to orientation and this affects ΔG, deduct credit at R03 and R04.

---

## EC-07 — Pseudoknots

**Issue:** The dot-bracket format used here does not encode pseudoknots. No pseudoknots are present in this task. Models should not attempt to infer them.  
**Recommended handling:** Not applicable here; note only as a reminder for future tasks with extended bracket notation.
