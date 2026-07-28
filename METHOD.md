# METHOD.md

## The Framework: PGTC

This repository follows the **PGTC** framework for embedding picker decisions:

| Letter | Stage | Purpose |
|--------|-------|---------|
| **P** | Pair-set construction | Build domain-specific snippet pairs with expected verdicts (NEAR, FAR, DECOY) before touching any encoder |
| **G** | Geometry measurement | Run candidates, read cosine scores against thresholds, hand-check at least one pair against domain reality |
| **T** | Tuning comparison | Change exactly one variable per run, watch which pairs move, derive the picker note from movement patterns |
| **C** | Ceiling and gate | Lock the capacity arithmetic, separation invariants, calibration thresholds, and the re-survey trigger into a trust gate |

---

### Where each letter lives in this repo

- **P** → `data/pairset-manifest.md`, `prompts/pair-craft-pack.md`
- **G** → `charter.md` (geometry verdict), `tests/similarity-board.md`
- **T** → `runs/tuning-runs-01.md`, `blueprints/embedding-advisor.md`
- **C** → `tests/trust-gate.md`, `blueprints/embedding-picker.md`

---

### Why the acronym appears only here

The rest of the repository uses plain language. A stranger reading `README.md` or `VERIFY.md` should not need to decode letters. This file exists so that anyone who wants the mnemonic can find it in one place, then forget it.
