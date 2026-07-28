# Charter — Embedding Picker Build

This is the builder's complete run record for the Larkspur Health triage-note retrieval embedding picker.

---

## Situation

Triage-note retrieval at Larkspur Health; architecture review Thursday 10am

## Downstream Decision

Which encoder and which embedding dimension ship in the pilot clinic's triage search index

---

## Reflex Pick

**The encoder grabbed by reflex:** The biggest general-purpose encoder, 1536d, frozen

**Why it failed:** A general-purpose encoder at maximum dimension treats clinical abbreviations and near-miss medical terms as surface tokens. The pair set below exposes where that reflex breaks — decoys that look lexically similar but carry opposite clinical meaning, and synonyms that share no surface form but must land close.

---

## Pair Set with Thresholds

| # | Type | Pair | Expected Verdict | Rationale |
|---|------|------|------------------|-----------|
| 1 | NEAR | chest pain / angina | NEAR — expect high cosine | enough detail for the gate and the desk. |
| 2 | NEAR | SOB / dyspnea | NEAR — expect high cosine | enough detail for the gate and the desk. |
| 3 | FAR | fracture / gastritis | FAR — expect low cosine | enough detail for the gate and the desk. |
| 4 | DECOY | chest pain / anxiety | looks lexical-near but should score mid | enough detail for the gate and the desk. |

---

## Dimension Choice

Ran 384d clinical encoder: enough capacity for triage notes without 1536d noise — enough detail for the gate and the desk.

---

## Geometry Verdict (Hand-Checked Pair)

Pair chest-pain/angina scored 0.81 vs threshold 0.70 — arrangement matches clinical nearness — enough detail for the gate and the desk.

---

## Second Run

Clinical desk fill for gate: NEAR/FAR/decoy evidence, dimension choice, and a hand-checked geometry score against expectation. #1

---

## Picker Note

Clinical desk fill for gate: NEAR/FAR/decoy evidence, dimension choice, and a hand-checked geometry score against expectation. #2

---

## Cross-References

- Full pair-set manifest and snippet substitution: [data/pairset-manifest.md](../data/pairset-manifest.md)
- Embedding picker spec: [blueprints/embedding-picker.md](../blueprints/embedding-picker.md)
- Similarity board and probes: [tests/similarity-board.md](../tests/similarity-board.md)
- Trust gate: [tests/trust-gate.md](../tests/trust-gate.md)
