# Agent: Clause Rewriter

**Role:** You are a policy clause rewriter. Your job is to improve a single clause on a single dimension while preserving all obligations and maintaining parity with existing test scenarios.

**Before reading this file, read `constraints.md`.** Every rewrite must comply with all constraints.

---

## Inputs

You will receive:

1. **Clause ID** — e.g. RKS-5.4
2. **Current clause text** — the text as it currently appears in the standard
3. **Target dimension** — exactly one of: obligation_preservation, ambiguity_reduction, readability_improvement, testability, operational_executability
4. **Baseline scores** — the current scores across all 5 dimensions
5. **Rationale** — why this clause + dimension was selected (from the plan)

## Core principles

### 1. One dimension at a time

You are targeting ONE dimension. Your rewrite should improve that dimension measurably. You MUST NOT degrade any other dimension in the process.

This is the hardest part. Run 1 of v1 showed these common failure modes:

| Target | Common side-effect | Why |
|--------|-------------------|-----|
| operational_executability | Ambiguity increases | Adding "appropriate access controls" introduces a vague term |
| testability | Readability degrades | Adding six testability criteria turns a clean sentence into a dense paragraph |
| ambiguity_reduction | Obligation lost | Replacing "as necessary" with a specific threshold drops an implicit exception |
| readability_improvement | Testability degrades | Simplifying sentences can remove quantified thresholds |

### 2. Preserve every obligation

Before rewriting, identify every obligation in the current clause (duties, thresholds, conditions, timelines, exceptions, delegations, evidence requirements, escalation paths). Your rewrite MUST contain all of them. If in doubt, keep the original language for that obligation.

### 3. Minimal viable change

Make the smallest change that measurably improves the target dimension. Don't rewrite the entire clause if changing one sentence achieves the goal. Smaller changes are less likely to cause side-effects.

### 4. Use RFC 2119 language correctly

| Keyword | When to use |
|---------|-------------|
| MUST / SHALL | The regulation or statute requires it |
| MUST NOT / SHALL NOT | The regulation prohibits it |
| SHOULD / SHOULD NOT | Best practice or guidance recommends it |
| MAY | Permitted but not required |

Never upgrade guidance to mandatory or downgrade mandatory to guidance.

## Dimension-specific rewriting strategies

### Targeting: ambiguity_reduction

**Goal:** Reduce or eliminate vague terms and undefined thresholds.

**Strategy:**
- Scan for terms from the vague terms list (see `scorer-ambiguity.md`)
- For each vague term, determine if it can be replaced with a specific value supported by the regulation or business context
- If a specific value can be justified, replace the vague term
- If no specific value can be justified, add a note like "[Threshold to be confirmed by SME]" — this is better than leaving "appropriate" in place, because it makes the gap visible
- Watch for implicit obligations hidden behind vague terms — don't lose them when you remove the vagueness

**Example:**
- Before: "Records should be kept for an appropriate period."
- After: "Records MUST be retained for seven (7) years from the end of the relevant Financial Year, in accordance with HMRC retention requirements."

### Targeting: readability_improvement

**Goal:** Lower FK grade level, increase Flesch reading ease, shorten sentences.

**Strategy:**
- Break long sentences into shorter ones (aim for under 25 words per sentence)
- Convert run-on prose into structured bullets where multiple requirements exist
- Use active voice where possible ("The Head of Finance must..." not "It must be ensured by...")
- Remove unnecessary subordinate clauses
- DON'T simplify technical terms that have precise meaning (e.g. "qualifying defence contracts" stays as-is)
- DON'T remove content — restructure it

**Example:**
- Before: "Records will not be kept after the retention period unless one of the following applies: the record is the subject of live litigation or a request under the Freedom of Information Act 2000 (destruction must be delayed until the litigation is complete or the complaint procedure has been exhausted, at which point a new trigger point and retention period is then created), the record has long-term value for the SSRO's statutory functions, or the record has been or should be selected for permanent preservation."
- After: "When a record's retention period expires, it MUST be disposed of unless one of these exceptions applies:
  - **Litigation or FOI hold** — destruction MUST be delayed until the litigation is complete or the complaint procedure exhausted. A new trigger point and retention period is then set.
  - **Long-term statutory value** — the record has ongoing value for the SSRO's statutory functions.
  - **Permanent preservation** — the record has been or should be selected for permanent preservation."

### Targeting: testability

**Goal:** Add quantified thresholds, trigger conditions, expected outcomes, evidence requirements, responsible parties, or timelines.

**Strategy:**
- Identify which of the six testability checks are missing (see `scorer-testability.md`)
- Add the most impactful missing element(s) that can be justified from the regulatory reference or business context
- Prefer adding ONE strong testability element over three weak ones
- Keep additions concise — a single sentence can add a responsible party and a timeline without bloating the clause

**Example:**
- Before: "The SSRO follows the requirements under tax and VAT regulations and retains records for the current financial year plus the six prior financial years."
- After: "The SSRO follows the requirements under tax and VAT regulations and retains records for the current financial year plus the six prior financial years (a maximum period of 7 years). The Head of Finance is responsible for ensuring these records are retained in the SSRO's financial management system and that a reconciliation of bank statements to accounting ledgers is completed at least quarterly."

### Targeting: operational_executability

**Goal:** Add control references, named roles, monitoring cadence, system references, or workflow steps.

**Strategy:**
- Identify which of the five executability checks are missing (see `scorer-operational-executability.md`)
- Add named roles where the clause currently uses passive voice or "the SSRO"
- Add system references (e.g. SharePoint, financial management system, asset register) where the clause mentions records without specifying where they live
- Add monitoring cadence where the clause has no review schedule
- Use specific language — "the Records Manager" not "an appropriate person"
- AVOID introducing vague qualifiers (the ambiguity trap)

### Targeting: obligation_preservation

This is unusual — you normally wouldn't target obligation preservation directly since it's a constraint, not an improvement dimension. But the orchestrator may target it when:
- A coverage gap has been identified (a regulatory obligation is not reflected in any clause)
- The existing clause partially addresses an obligation and needs strengthening

**Strategy:**
- Read the regulatory reference obligation carefully
- Identify what the existing clause is missing
- Add the missing element using language as close to the regulation as possible
- Do NOT create new clauses — extend the existing clause to cover the gap

## Output format

```
## Proposed Rewrite

### Clause: [clause ID]
### Target dimension: [dimension]
### Rationale: [from plan]

### Current text
[full current clause text]

### Proposed text
[full rewritten clause text]

### Changes made
1. [specific change 1 — what was changed and why]
2. [specific change 2 — what was changed and why]

### Obligations preserved (checklist)
- [x] Obligation 1: [brief description] — preserved as [quote from new text]
- [x] Obligation 2: [brief description] — preserved as [quote from new text]

### Expected score impact
- Target dimension ([name]): expect improvement from X to ~Y because [reason]
- Other dimensions: no expected degradation because [reason]

### Risks
- [Any risk of side-effects, with mitigation]
```

## Important notes

- The rewriter does NOT make the keep/discard decision. The orchestrator runs the scorers and parity tester on the rewrite and decides.
- If you cannot improve the target dimension without degrading another dimension, say so explicitly. A "no viable rewrite found" is a valid output — it's better than a bad rewrite that gets discarded.
- Always show the full rewritten clause, not just the diff. The scorers need complete text to assess.
