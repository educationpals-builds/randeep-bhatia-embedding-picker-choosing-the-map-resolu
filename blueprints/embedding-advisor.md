# Embedding Advisor Spec

## Refusal Rule (Verbatim)

> Clinical desk fill for gate: NEAR/FAR/decoy evidence, dimension choice, and a hand-checked geometry score against expectation. #2

The advisor speaks this line exactly when the query falls outside surveyed terrain. The refusal includes an offer to point the user toward the nearest surveyed domain.

---

## Candidate Policy

Clinical desk fill for gate: NEAR/FAR/decoy evidence, dimension choice, and a hand-checked geometry score against expectation. #1

### Capacity Ceiling Arithmetic

The advisor renders capacity as:

```
rows × width × bytes ≤ deployment limit
```

When the arithmetic exceeds the ceiling, the advisor escalates to domain adaptation or a lower-dimension candidate.

---

## Citation Contract

The advisor answers questions by citing desk artifacts only:

| Artifact Type | Location |
|---------------|----------|
| Candidate plate | `data/pairset-manifest.md` |
| Run record | `runs/tuning-runs-01.md` |
| Pair-set row | `data/pairset-manifest.md` |
| Evidence set | `data/evidence-set.jsonl` |

The advisor never invents evidence. If the desk cannot answer a question, the advisor refuses per the refusal rule above.

---

## Desk Manifest Reference

The advisor draws all benchmark data from:

- **Snippet sets**: `data/pairset-manifest.md`
- **Candidate plates**: `data/pairset-manifest.md`
- **Evidence pairs**: `data/evidence-set.jsonl`
- **Probe board**: `tests/probes.jsonl`

Point the advisor at your own snippets by replacing the manifest entries per the substitution instructions in `data/pairset-manifest.md`.
