# Stranger Verification

This guide lets you verify the embedding picker without trusting the builder's word. You run the seeded reflex pick, confirm the failure, then confirm the rebuilt pair set fixes it.

---

## Domain Context

**Situation:** Triage-note retrieval at Larkspur Health; architecture review Thursday 10am

**Decision this picker feeds:** Which encoder and which embedding dimension ship in the pilot clinic's triage search index

---

## Step 1: Run the Seeded Reflex Pick in /play

The builder's naive pick was:

> The biggest general-purpose encoder, 1536d, frozen

Load the reflex encoder (1536d frozen general-purpose) in `/play` and embed the antonym pair from the hard-pair seed:

- **Snippet A:** "patient is hypotensive on arrival"
- **Snippet B:** "patient is hypertensive on arrival"

Record the cosine similarity score.

---

## Step 2: Confirm the Antonym Pair Scores Above the NEAR Line

The reflex pick fails when clinically opposite terms (hypotensive vs hypertensive) score above the NEAR threshold. If the frozen 1536d encoder returns a high similarity for this pair, the reflex pick cannot distinguish dangerous clinical opposites.

**Expected failure:** The antonym pair lands in the NEAR zone despite opposite clinical meaning.

---

## Step 3: Confirm the Picker's Rebuilt Pair Set Separates It

Load the picker's chosen encoder (384d clinical encoder per `dimension_choice`) and run the benchmark pair set from `data/pairset-manifest.md`:

| Pair | Expected Verdict |
|------|------------------|
| chest pain / angina | NEAR — expect high cosine |
| SOB / dyspnea | NEAR — expect high cosine |
| fracture / gastritis | FAR — expect low cosine |
| chest pain / anxiety | DECOY — looks lexical-near but should score mid |

**Geometry check:** The builder's hand-checked pair (chest-pain/angina) scored 0.81 vs threshold 0.70 — arrangement matches clinical nearness.

Verify that:
1. NEAR pairs score above the threshold
2. FAR pairs score below the threshold
3. DECOY pairs land in the expected mid-range, not falsely NEAR

---

## Verification Artifacts

- Full pair set with thresholds: `data/pairset-manifest.md`
- Machine-readable probes: `tests/probes.jsonl`
- Separation/calibration grid: `tests/similarity-board.md`
- Trust gate invariants: `tests/trust-gate.md`
- Run-local instructions: `tests/run-local.md`

---

## Pass Criteria

Verification passes when:
1. The reflex pick (1536d frozen) fails the antonym pair (scores NEAR when it should be FAR)
2. The rebuilt picker (384d clinical) separates the antonym pair correctly
3. All four benchmark pairs in the pair set land in their expected zones

If any check fails, open an issue referencing the specific pair and its score.
