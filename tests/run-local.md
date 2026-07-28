# Run-Local Guide

Run the similarity board anywhere — manual paste, script, or CI.

---

## Rung 1: Manual Paste Protocol

For each probe in `tests/probes.jsonl`, paste both snippets into your embedding endpoint and compute cosine similarity. Compare against the expected verdict and threshold.

| Probe | Snippet A | Snippet B | Expected | Threshold |
|-------|-----------|-----------|----------|-----------|
| NEAR-1 | chest pain | angina | NEAR | ≥ 0.70 |
| NEAR-2 | SOB | dyspnea | NEAR | ≥ 0.70 |
| FAR-1 | fracture | gastritis | FAR | < 0.40 |
| DECOY-1 | chest pain | anxiety | MID | 0.40–0.65 |
| LEARNER-1 | Clinical desk fill for gate: NEAR/FAR/decoy evidence, dimension choice, and a hand-checked geometry score against expectation. #1 | — | — | — |
| LEARNER-2 | Clinical desk fill for gate: NEAR/FAR/decoy evidence, dimension choice, and a hand-checked geometry score against expectation. #2 | — | — | — |

Record each score beside its expected line. Mark PASS if the score lands in the expected band, FAIL otherwise.

---

## Rung 2: Script Runner (~20 lines)

Save as `run_board.py`. Requires `EMBEDDING_API_KEY` in environment.

```python
import json
import os
import numpy as np

# Replace with your embedding client
def get_embedding(text):
    # Stub: call your embedding API here
    # Return a numpy array of shape (dim,)
    raise NotImplementedError("Wire your embedding endpoint")

def cosine(a, b):
    return np.dot(a, b) / (np.linalg.norm(a) * np.linalg.norm(b))

def main():
    api_key = os.environ.get("EMBEDDING_API_KEY")
    if not api_key:
        print("Set EMBEDDING_API_KEY in environment")
        return

    with open("tests/probes.jsonl", "r") as f:
        probes = [json.loads(line) for line in f if line.strip()]

    results = []
    for p in probes:
        emb_a = get_embedding(p["input"][0])
        emb_b = get_embedding(p["input"][1])
        score = cosine(emb_a, emb_b)
        verdict = "PASS" if p["invariant"](score) else "FAIL"
        results.append({"id": p["id"], "score": round(score, 4), "expected": p["expected"], "verdict": verdict})

    print("=== Separation / Calibration Grid ===")
    for r in results:
        print(f"{r['id']:12} | score={r['score']:.4f} | expected={r['expected']:5} | {r['verdict']}")

    gate = all(r["verdict"] == "PASS" for r in results)
    print(f"\n=== Gate Verdict: {'PASS' if gate else 'FAIL'} ===")

if __name__ == "__main__":
    main()
```

Run:
```bash
export EMBEDDING_API_KEY=your_key_here
python run_board.py
```

---

## Rung 3: Eval Tool / CI Integration

Load `tests/probes.jsonl` into any eval runner (e.g., pytest, GitHub Actions, internal eval harness). The board re-runs automatically whenever:

- The encoder changes (model version, fine-tune checkpoint)
- The embedding width changes (384d → 768d → 1536d)
- The snippet set changes (new triage notes, updated pair set)

Example CI trigger (GitHub Actions):
```yaml
on:
  push:
    paths:
      - 'tests/probes.jsonl'
      - 'config/encoder.yaml'
jobs:
  board:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: pip install numpy
      - run: python run_board.py
        env:
          EMBEDDING_API_KEY: ${{ secrets.EMBEDDING_API_KEY }}
```

---

## Diffing Against the EP-Certified Board

After running locally, compare your grid output to the certified board on the listing:

1. Export your local grid to `local-board.csv`
2. Fetch the EP-certified board from the listing's `/board.csv` endpoint
3. Diff column-by-column:
   - **Separation**: NEAR pairs must stay above threshold; FAR pairs must stay below
   - **Calibration**: Decoy pairs must land in the mid-band; no drift beyond ±0.05 from certified scores

If any probe drifts beyond tolerance, the re-survey trigger fires — see `tests/trust-gate.md` for the full trigger specification.
