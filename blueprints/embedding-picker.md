# Embedding Picker — One-Paste Spec

## Domain Context

**Situation:** Triage-note retrieval at Larkspur Health; architecture review Thursday 10am

**Decision this picker feeds:** Which encoder and which embedding dimension ship in the pilot clinic's triage search index

**Reflex pick (the one that failed):** The biggest general-purpose encoder, 1536d, frozen

---

## Benchmark Pair Set

The picker evaluates candidates against this pair set. Each pair has an expected verdict (NEAR, FAR, or DECOY) and a threshold.

| # | Pair | Expected | Rationale |
|---|------|----------|-----------|
| 1 | NEAR: chest pain / angina — expect high cosine — enough detail for the gate and the desk. | NEAR | Synonymous clinical concepts |
| 2 | NEAR: SOB / dyspnea — expect high cosine — enough detail for the gate and the desk. | NEAR | Abbreviation and full term |
| 3 | FAR: fracture / gastritis — expect low cosine — enough detail for the gate and the desk. | FAR | Unrelated conditions |
| 4 | DECOY: chest pain / anxiety — looks lexical-near but should score mid — enough detail for the gate and the desk. | DECOY | Surface-similar, clinically distinct |

See [`data/pairset-manifest.md`](../data/pairset-manifest.md) for the full manifest with pre-computed vectors.

---

## Dimension Choice

Ran 384d clinical encoder: enough capacity for triage notes without 1536d noise — enough detail for the gate and the desk.

---

## Geometry Verdict

Pair chest-pain/angina scored 0.81 vs threshold 0.70 — arrangement matches clinical nearness — enough detail for the gate and the desk.

---

## One-Paste Picker Protocol

```
1. Load the pair set from data/pairset-manifest.md (or tests/probes.jsonl for machine use)
2. For each candidate encoder + dimension:
   a. Embed both snippets in every pair
   b. Compute cosine similarity
   c. Compare against expected verdict and threshold
3. Score the candidate:
   - NEAR pairs must exceed the NEAR threshold
   - FAR pairs must fall below the FAR threshold
   - DECOY pairs must land in the mid-band (neither NEAR nor FAR)
4. The candidate that separates all pairs correctly at the lowest dimension wins
5. If no candidate separates the decoy, escalate to domain adaptation
```

---

## Point-at-Your-Own-Snippets

To run this picker on your own domain:

1. **Replace the pair set** in `data/pairset-manifest.md`:
   - Write at least 4 pairs from your actual corpus
   - Include at least one NEAR (synonyms), one FAR (unrelated), one DECOY (surface-similar, meaning-different)
   - Each pair needs two real snippets, not single words

2. **Set your thresholds**:
   - Start with NEAR ≥ 0.70, FAR ≤ 0.40, DECOY in [0.40, 0.70]
   - Adjust based on your first geometry read

3. **Run the picker**:
   - Use `tests/run-local.md` for manual, script, or CI execution
   - The script reads `tests/probes.jsonl` and prints the separation grid

4. **Read the board**:
   - Check `tests/similarity-board.md` for the full probe grid
   - Check `tests/trust-gate.md` for the invariants your pick must hold

5. **Update the manifest**:
   - After your run, update `data/pairset-manifest.md` with your snippets and candidate plates
   - The picker inherits your pair-set discipline and threshold recipe
