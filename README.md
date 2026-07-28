# Embedding Picker — Triage-Note Retrieval at Larkspur Health

## Domain Situation

Triage-note retrieval at Larkspur Health; architecture review Thursday 10am

## Downstream Decision

Which encoder and which embedding dimension ship in the pilot clinic's triage search index

## The Reflex Pick (and Why It Failed)

**Reflex pick:** The biggest general-purpose encoder, 1536d, frozen

The reflex pick fails because a general-purpose encoder at maximum dimension doesn't guarantee clinical-term separation. Without a pair-set benchmark, you can't know whether "hypotensive" and "hypertensive" land far enough apart, or whether "chest pain" and "angina" land close enough. The geometry must be measured, not assumed.

## Pair-Set Result

The benchmark pair set tested these clinical relationships:

1. NEAR: chest pain / angina — expect high cosine — enough detail for the gate and the desk.
2. NEAR: SOB / dyspnea — expect high cosine — enough detail for the gate and the desk.
3. FAR: fracture / gastritis — expect low cosine — enough detail for the gate and the desk.
4. DECOY: chest pain / anxiety — looks lexical-near but should score mid — enough detail for the gate and the desk.

**Dimension choice:** Ran 384d clinical encoder: enough capacity for triage notes without 1536d noise — enough detail for the gate and the desk.

**Geometry verdict:** Pair chest-pain/angina scored 0.81 vs threshold 0.70 — arrangement matches clinical nearness — enough detail for the gate and the desk.

## One-Knob Comparison

**Second run:** Clinical desk fill for gate: NEAR/FAR/decoy evidence, dimension choice, and a hand-checked geometry score against expectation. #1

**Picker note:** Clinical desk fill for gate: NEAR/FAR/decoy evidence, dimension choice, and a hand-checked geometry score against expectation. #2

---

## One-Paste Rebuild Block

To rebuild this picker for your own domain:

1. Clone this repo
2. Replace the snippets in `data/pairset-manifest.md` with your domain's real text pairs
3. Update the candidate plates with your encoder options
4. Run the probes in `tests/probes.jsonl` against each candidate
5. Read the separation/calibration grid in `tests/similarity-board.md`
6. Apply the trust gate from `tests/trust-gate.md`

---

## Repository Structure

- **[charter.md](charter.md)** — Full builder's run record
- **[METHOD.md](METHOD.md)** — Framework with acronym definition
- **[blueprints/](blueprints/)** — Picker and advisor specs
- **[prompts/](prompts/)** — Pair-craft template and decoy patterns
- **[data/](data/)** — Manifest and evidence set seeds
- **[tests/](tests/)** — Similarity board, trust gate, probes, and run-local guide
- **[runs/](runs/)** — Tuning run records
- **[VERIFY.md](VERIFY.md)** — Stranger verification protocol
- **[STORY.md](STORY.md)** — Builder's first-person narrative

<!-- educationpals-build-verified -->
