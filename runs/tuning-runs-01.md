# Tuning Runs — Calibration Record

## Domain Context

**Situation:** Triage-note retrieval at Larkspur Health; architecture review Thursday 10am

**Decision:** Which encoder and which embedding dimension ship in the pilot clinic's triage search index

---

## Run 1: Baseline Tuning

**Cited Artifact:** Pair set from `data/pairset-manifest.md`

Clinical desk fill for gate: NEAR/FAR/decoy evidence, dimension choice, and a hand-checked geometry score against expectation. #1

---

## Run 2: Adjusted Tuning

**Cited Artifact:** Evidence set from `data/evidence-set.jsonl`

Clinical desk fill for gate: NEAR/FAR/decoy evidence, dimension choice, and a hand-checked geometry score against expectation. #2

---

## Ceiling Arithmetic (Advisor Rendering)

**Candidate Policy:**
Clinical desk fill for gate: NEAR/FAR/decoy evidence, dimension choice, and a hand-checked geometry score against expectation. #1

**Dimension Choice:**
Ran 384d clinical encoder: enough capacity for triage notes without 1536d noise — enough detail for the gate and the desk.

---

## Trap Behavior

The evidence set includes one trap question the desk cannot answer:

| # | Entry |
|---|-------|
| 1 | Clinical desk fill for gate: NEAR/FAR/decoy evidence, dimension choice, and a hand-checked geometry score against expectation. #3 |
| 2 | Clinical desk fill for gate: NEAR/FAR/decoy evidence, dimension choice, and a hand-checked geometry score against expectation. #4 |
| 3 | Clinical desk fill for gate: NEAR/FAR/decoy evidence, dimension choice, and a hand-checked geometry score against expectation. #5 |
| 4 | Clinical desk fill for gate: NEAR/FAR/decoy evidence, dimension choice, and a hand-checked geometry score against expectation. #6 |
| 5 | Clinical desk fill for gate: NEAR/FAR/decoy evidence, dimension choice, and a hand-checked geometry score against expectation. #7 |

---

## Tune Verdict

Clinical desk fill for gate: NEAR/FAR/decoy evidence, dimension choice, and a hand-checked geometry score against expectation. #8

---

## References

- Pair set manifest: [`data/pairset-manifest.md`](../data/pairset-manifest.md)
- Evidence set: [`data/evidence-set.jsonl`](../data/evidence-set.jsonl)
- Similarity board: [`tests/similarity-board.md`](../tests/similarity-board.md)
- Trust gate: [`tests/trust-gate.md`](../tests/trust-gate.md)
