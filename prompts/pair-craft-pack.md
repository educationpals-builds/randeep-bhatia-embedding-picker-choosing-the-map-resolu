# Pair-Craft Pack

A prompt pack for building embedding benchmark pair sets with decoy discipline.

---

## Pair-Set Template

Use this structure for every pair in your benchmark:

```
TYPE: [NEAR | FAR | DECOY]
SNIPPET A: [verbatim text from your domain]
SNIPPET B: [verbatim text from your domain]
EXPECTED VERDICT: [NEAR | FAR | MID]
THRESHOLD: [cosine similarity number, e.g., 0.70]
RATIONALE: [why this pair tests what you claim it tests]
```

### Learner's Seeded Pair Set

1. NEAR: chest pain / angina — expect high cosine — enough detail for the gate and the desk.
2. NEAR: SOB / dyspnea — expect high cosine — enough detail for the gate and the desk.
3. FAR: fracture / gastritis — expect low cosine — enough detail for the gate and the desk.
4. DECOY: chest pain / anxiety — looks lexical-near but should score mid — enough detail for the gate and the desk.

---

## Six Decoy Patterns

Decoys expose where an encoder conflates surface similarity with semantic similarity. Each pattern below is a trap the naive encoder falls into.

### 1. Antonym Twin

Two snippets share every word except one antonym swap.

**Pattern:** `[context] + [term]` vs `[context] + [antonym of term]`

**Example:**
- A: "patient is hypotensive on arrival"
- B: "patient is hypertensive on arrival"
- Expected: FAR (opposite clinical states)
- Trap: High lexical overlap → naive encoder scores NEAR

### 2. Negation

One snippet asserts; the other negates the same fact.

**Pattern:** `[assertion]` vs `[negation of assertion]`

**Example:**
- A: "chest pain present"
- B: "no chest pain"
- Expected: FAR (opposite clinical findings)
- Trap: Shared key terms → naive encoder scores NEAR

### 3. Disclaimer

One snippet states a condition; the other disclaims or rules it out.

**Pattern:** `[condition stated]` vs `[condition ruled out]`

**Example:**
- A: "suspected MI"
- B: "MI ruled out by troponin"
- Expected: FAR (different clinical conclusions)
- Trap: Same diagnosis term → naive encoder scores NEAR

### 4. Event vs Resolution

One snippet describes an acute event; the other describes its resolution.

**Pattern:** `[acute event]` vs `[resolution of event]`

**Example:**
- A: "active GI bleed"
- B: "GI bleed resolved, stable hemoglobin"
- Expected: FAR (different clinical states)
- Trap: Same condition term → naive encoder scores NEAR

### 5. Abbreviation Bridge

One snippet uses the abbreviation; the other uses the expansion or a synonym.

**Pattern:** `[abbreviation]` vs `[expansion or synonym]`

**Example:**
- A: "SOB on exertion"
- B: "dyspnea on exertion"
- Expected: NEAR (same clinical meaning)
- Trap: Different surface forms → naive encoder scores FAR

### 6. Unit Mismatch

Same measurement, different units or scales that change clinical meaning.

**Pattern:** `[value in unit A]` vs `[value in unit B]`

**Example:**
- A: "temp 38.5°C"
- B: "temp 101.3°F"
- Expected: NEAR (same temperature)
- Trap: Different numbers → naive encoder scores FAR

---

## Threshold-Derivation Recipe

Thresholds are not guessed. They are derived from your pair set's geometry.

### Step 1: Embed All Pairs

Run every pair through your candidate encoder. Record raw cosine similarity for each.

### Step 2: Sort by Expected Verdict

Group scores:
- **NEAR pairs:** should cluster high (e.g., 0.75–0.95)
- **FAR pairs:** should cluster low (e.g., 0.20–0.45)
- **DECOY pairs:** reveal the gap (or lack of one)

### Step 3: Find the Separation Gap

The gap is the space between your lowest NEAR score and your highest FAR score.

```
Gap = min(NEAR scores) - max(FAR scores)
```

- **Positive gap:** encoder separates your domain. Threshold lives in the gap.
- **Zero or negative gap:** encoder conflates. Change encoder or adapt.

### Step 4: Set Threshold in the Gap

Place threshold closer to the FAR ceiling than the NEAR floor. This biases toward precision (fewer false positives).

```
Threshold = max(FAR scores) + (0.6 × Gap)
```

### Step 5: Validate with Decoys

Every decoy must land on the correct side of threshold. If a decoy crosses:
1. Check if the decoy is mislabeled
2. Check if the encoder lacks the distinction
3. Adjust threshold or escalate to domain adaptation

### Step 6: Document the Derivation

Record in your desk manifest:
- Raw scores for all pairs
- The gap arithmetic
- The threshold and its derivation
- Any decoys that required adjustment

---

## Usage

1. Copy the pair-set template for each new pair
2. Include at least one decoy from each of the six patterns relevant to your domain
3. Run the threshold-derivation recipe before committing to an encoder
4. Re-run whenever encoder, dimension, or snippet set changes

See `data/pairset-manifest.md` for the seeded manifest and substitution instructions.
