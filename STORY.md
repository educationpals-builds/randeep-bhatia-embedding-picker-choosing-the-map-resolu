# Builder's Story

I built an embedding picker for triage-note retrieval at Larkspur Health, with an architecture review deadline of Thursday 10am. The downstream decision: which encoder and which embedding dimension ship in the pilot clinic's triage search index.

## The Reflex Pick That Failed

My first instinct was the biggest general-purpose encoder, 1536d, frozen. I assumed more dimensions meant better separation. The pair set exposed the gap.

## The Probe That Fooled It

The decoy pair — chest pain / anxiety — looked lexically close but should have scored mid-range. From the board:

> DECOY: chest pain / anxiety — looks lexical-near but should score mid — enough detail for the gate and the desk.

This pair revealed that surface similarity in clinical language does not map to clinical meaning. The frozen general-purpose encoder could not distinguish a cardiac symptom from a psychiatric presentation when the words overlapped.

## The Fix

I ran 384d clinical encoder: enough capacity for triage notes without 1536d noise — enough detail for the gate and the desk.

The geometry verdict confirmed the fix: Pair chest-pain/angina scored 0.81 vs threshold 0.70 — arrangement matches clinical nearness — enough detail for the gate and the desk.

## The Gate It Now Holds

Clinical desk fill for gate: NEAR/FAR/decoy evidence, dimension choice, and a hand-checked geometry score against expectation. #4

## Re-Survey Cadence

The board re-runs whenever the encoder changes, the dimension changes, or the snippet set expands to new triage categories. Any change to the clinical vocabulary — new abbreviations, new chief complaint codes — triggers a fresh survey.

## The Domain Lesson

Clinical triage language carries meaning in single-letter differences (hypotensive vs hypertensive) and in abbreviation families (SOB vs dyspnea). A picker that passes the pair set on these distinctions earns deployment. One that fails the decoy pair — confusing symptom with differential — does not ship, regardless of its benchmark scores on general corpora.

The desk manifest in `data/pairset-manifest.md` and the machine-readable board in `tests/probes.jsonl` let the next builder point this picker at their own snippets and run the same gate.
