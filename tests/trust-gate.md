# Trust Gate

The gate that governs when this embedding picker can ship — and when it must pause for re-survey.

---

## Separation Invariants

The board must hold these separation properties before the picker is trusted:

- NEAR pairs score above the NEAR threshold (0.70)
- FAR pairs score below the FAR ceiling (0.40)
- DECOY pairs land in the mid-band, not collapsed into NEAR

---

## Calibration Thresholds

Clinical desk fill for gate: NEAR/FAR/decoy evidence, dimension choice, and a hand-checked geometry score against expectation. #4

---

## Capacity Ceiling

From the dimension choice: Ran 384d clinical encoder: enough capacity for triage notes without 1536d noise — enough detail for the gate and the desk.

---

## Re-Survey Trigger

The board must be re-run when any of the following changes:

1. The encoder candidate changes (frozen → adapted, or vendor swap)
2. The embedding dimension changes (384d → 768d → 1536d)
3. The snippet corpus expands to include new clinical abbreviation families
4. The deployment memory ceiling changes
5. A production failure surfaces a pair the board did not cover

---

## Contested Marginal-Gap Rulings

### Board Reading (Two Axes)

Clinical desk fill for gate: NEAR/FAR/decoy evidence, dimension choice, and a hand-checked geometry score against expectation. #3

### Referee's Case

When a pair lands in the marginal band (between NEAR threshold and FAR ceiling), the referee consults:

1. The pair-set row's expected verdict
2. The geometry verdict's hand-checked score
3. The picker note's auditable line

If the marginal pair was marked DECOY in the pair set, mid-band placement is correct behavior. If it was marked NEAR or FAR, the gate fails and the board must identify which layer broke: snippet framing, tokenization, width/capacity, adaptation state, or threshold calibration.

---

## Gate Status

This gate references:
- `tests/similarity-board.md` — the full probe grid with raw and centered scores
- `tests/probes.jsonl` — machine-readable probes for automated re-survey
- `runs/tuning-runs-01.md` — calibration record with cited artifacts

The picker ships when separation invariants hold and calibration thresholds pass. Any re-survey trigger forces a fresh board run before the next deployment.
