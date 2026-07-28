# Pairset Manifest — Larkspur Health Triage-Note Retrieval

This manifest seeds the embedding picker desk with snippet sets, candidate plates, and pre-computed vector coverage for the triage-note retrieval domain.

---

## Domain Context

**Situation:** Triage-note retrieval at Larkspur Health; architecture review Thursday 10am

**Downstream decision:** Which encoder and which embedding dimension ship in the pilot clinic's triage search index

---

## Snippet Sets

### Seeded Pair Set

The following pairs anchor the benchmark. Each entry includes two snippets, the expected verdict (NEAR or FAR), and the reasoning.

| ID | Pair | Expected | Notes |
|----|------|----------|-------|
| 1 | NEAR: chest pain / angina — expect high cosine — enough detail for the gate and the desk. | NEAR | Clinical synonym pair |
| 2 | NEAR: SOB / dyspnea — expect high cosine — enough detail for the gate and the desk. | NEAR | Abbreviation-to-term mapping |
| 3 | FAR: fracture / gastritis — expect low cosine — enough detail for the gate and the desk. | FAR | Unrelated conditions |
| 4 | DECOY: chest pain / anxiety — looks lexical-near but should score mid — enough detail for the gate and the desk. | MID (decoy) | Surface-similar, clinically distinct |

### Hard Pair Seed (Reference)

The desk was seeded with this constraint:

> 'patient is hypotensive on arrival' and 'patient is hypertensive on arrival' must land FAR apart, while 'hypotensive' and 'low blood pressure' must land close

---

## Candidate Plates

| Candidate | Dimension | Frozen/Adapted | Notes |
|-----------|-----------|----------------|-------|
| General-purpose encoder | 1536d | Frozen | Naive pick (baseline) |
| Clinical encoder | 384d | Frozen | First run candidate |

**Dimension choice rationale:** Ran 384d clinical encoder: enough capacity for triage notes without 1536d noise — enough detail for the gate and the desk.

---

## Pre-Computed Vector Coverage

### Geometry Check

**Pair:** chest-pain / angina  
**Score:** 0.81  
**Threshold:** 0.70  
**Verdict:** Pair chest-pain/angina scored 0.81 vs threshold 0.70 — arrangement matches clinical nearness — enough detail for the gate and the desk.

---

## Substituting Your Own Snippets and Candidates

To point this manifest at your own domain:

### 1. Replace the Snippet Sets

Edit the pair set table above with your domain's pairs:

- Include at least 4 pairs (minimum for gate)
- At least one NEAR pair (synonyms or paraphrases in your domain)
- At least one FAR pair (unrelated concepts)
- At least one DECOY (surface-similar wording, different meaning)

### 2. Update the Candidate Plates

Replace the candidate rows with encoders you're evaluating:

```
| Your encoder name | dimension | frozen/adapted | notes |
```

### 3. Re-run Vector Coverage

After substitution:

1. Embed each snippet pair with your candidate encoder
2. Compute cosine similarity
3. Record scores in the geometry check section
4. Compare against your threshold

### 4. Update the Probes File

Sync your changes to `tests/probes.jsonl` so the board and gate reflect your domain.

---

## File References

- **Benchmark spec:** `blueprints/embedding-picker.md`
- **Machine-readable probes:** `tests/probes.jsonl`
- **Similarity board:** `tests/similarity-board.md`
- **Trust gate:** `tests/trust-gate.md`

---

*This manifest is the dataset sample export seed for the Larkspur Health embedding picker desk.*
