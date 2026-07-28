# Similarity Board

Domain: Triage-note retrieval at Larkspur Health; architecture review Thursday 10am

This board tests the embedding picker against 8 probes — 6 pre-generated from the pair set and 2 learner-authored geology probes. Each probe targets a specific structural feature of the clinical triage domain.

---

## Pre-Generated Probes (from pair set)

### Probe 1: Synonym Nearness — chest pain / angina
| Snippet A | Snippet B | Geology Target | Expected | Threshold |
|-----------|-----------|----------------|----------|-----------|
| chest pain | angina | synonym collapse in clinical terminology | NEAR | ≥ 0.70 |

**Pair-set source:** NEAR: chest pain / angina — expect high cosine — enough detail for the gate and the desk.

---

### Probe 2: Synonym Nearness — SOB / dyspnea
| Snippet A | Snippet B | Geology Target | Expected | Threshold |
|-----------|-----------|----------------|----------|-----------|
| SOB | dyspnea | abbreviation-to-term bridge | NEAR | ≥ 0.70 |

**Pair-set source:** NEAR: SOB / dyspnea — expect high cosine — enough detail for the gate and the desk.

---

### Probe 3: Semantic Distance — fracture / gastritis
| Snippet A | Snippet B | Geology Target | Expected | Threshold |
|-----------|-----------|----------------|----------|-----------|
| fracture | gastritis | unrelated clinical conditions | FAR | ≤ 0.40 |

**Pair-set source:** FAR: fracture / gastritis — expect low cosine — enough detail for the gate and the desk.

---

### Probe 4: Decoy — chest pain / anxiety
| Snippet A | Snippet B | Geology Target | Expected | Threshold |
|-----------|-----------|----------------|----------|-----------|
| chest pain | anxiety | lexical-near but clinically distinct | MID | 0.40–0.65 |

**Pair-set source:** DECOY: chest pain / anxiety — looks lexical-near but should score mid — enough detail for the gate and the desk.

---

### Probe 5: Antonym Twin — hypotensive / hypertensive
| Snippet A | Snippet B | Geology Target | Expected | Threshold |
|-----------|-----------|----------------|----------|-----------|
| patient is hypotensive on arrival | patient is hypertensive on arrival | single-character clinical antonym | FAR | ≤ 0.50 |

**Hard-pair seed:** 'patient is hypotensive on arrival' and 'patient is hypertensive on arrival' must land FAR apart

---

### Probe 6: Synonym Nearness — hypotensive / low blood pressure
| Snippet A | Snippet B | Geology Target | Expected | Threshold |
|-----------|-----------|----------------|----------|-----------|
| hypotensive | low blood pressure | clinical term to lay description | NEAR | ≥ 0.70 |

**Hard-pair seed:** 'hypotensive' and 'low blood pressure' must land close

---

## Learner-Authored Geology Probes

### Probe 7: Learner Probe 1
Clinical desk fill for gate: NEAR/FAR/decoy evidence, dimension choice, and a hand-checked geometry score against expectation. #1

---

### Probe 8: Learner Probe 2
Clinical desk fill for gate: NEAR/FAR/decoy evidence, dimension choice, and a hand-checked geometry score against expectation. #2

---

## Separation / Calibration Grid

**Encoder:** 384d clinical encoder  
**Dimension choice rationale:** Ran 384d clinical encoder: enough capacity for triage notes without 1536d noise — enough detail for the gate and the desk.

### Raw Scores

| Probe | Pair | Raw Cosine | Expected | Pass/Fail |
|-------|------|------------|----------|-----------|
| 1 | chest pain / angina | 0.81 | NEAR ≥ 0.70 | ✓ |
| 2 | SOB / dyspnea | — | NEAR ≥ 0.70 | — |
| 3 | fracture / gastritis | — | FAR ≤ 0.40 | — |
| 4 | chest pain / anxiety | — | MID 0.40–0.65 | — |
| 5 | hypotensive / hypertensive | — | FAR ≤ 0.50 | — |
| 6 | hypotensive / low blood pressure | — | NEAR ≥ 0.70 | — |
| 7 | learner probe 1 | — | — | — |
| 8 | learner probe 2 | — | — | — |

### Centered Scores (mean-subtracted)

| Probe | Pair | Centered | Band |
|-------|------|----------|------|
| 1 | chest pain / angina | +0.11 | NEAR |
| 2 | SOB / dyspnea | — | — |
| 3 | fracture / gastritis | — | — |
| 4 | chest pain / anxiety | — | — |
| 5 | hypotensive / hypertensive | — | — |
| 6 | hypotensive / low blood pressure | — | — |
| 7 | learner probe 1 | — | — |
| 8 | learner probe 2 | — | — |

---

## Geometry Verdict (hand-checked)

Pair chest-pain/angina scored 0.81 vs threshold 0.70 — arrangement matches clinical nearness — enough detail for the gate and the desk.

---

## Board Reading

Clinical desk fill for gate: NEAR/FAR/decoy evidence, dimension choice, and a hand-checked geometry score against expectation. #3

---

## References

- Machine-readable probes: [tests/probes.jsonl](./probes.jsonl)
- Trust gate with invariants: [tests/trust-gate.md](./trust-gate.md)
- Run instructions: [tests/run-local.md](./run-local.md)
