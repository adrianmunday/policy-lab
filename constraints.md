# Policy Lab v4 - Constraints

This document defines guardrails for rewrite waves and surgical-mode checks.
Violating these constraints invalidates a wave unless the human explicitly
approves the policy change.

## What you CAN do

- Modify clause body text within `standards/*.md` files when the clause is inside
  the current rewrite packet.
- Modify multiple related clauses in one rewrite wave if the planner included
  them in scope and the editor accepted the draft.
- Improve clarity, readability, testability, operational executability,
  consistency, evidence requirements, role ownership, triggers, and review
  cadence.
- Add new test scenarios to `scenario_packs/` if a genuine coverage gap is found.
  Existing scenario files remain immutable.
- Update affected entries in `maps/` after a kept wave.
- Use surgical mode for high-risk clauses that need one-clause assurance.

## What you CANNOT do

- Modify `regulatory_reference/`. These files are read-only ground truth.
- Remove or weaken an existing obligation.
- Delete or modify existing scenario pack files.
- Modify clauses outside the current packet unless the editor explicitly accepts
  the nearby edit.
- Create new clauses, new sections, or new standards unless the human has
  explicitly authorised that structural change.
- Modify YAML frontmatter fields unless the human explicitly authorises it.
- Renumber or reorganise clause identifiers unless the human explicitly
  authorises a structural change.
- Upgrade guidance to mandatory language or downgrade mandatory language to
  guidance without source support.
- Treat an out-of-scope banking or non-UK obligation as binding on the SSRO
  standard without human confirmation.

## Obligation language

When the source text implies a mandatory requirement, use RFC 2119 language:

| Keyword | Strength | Use when |
|---------|----------|----------|
| MUST / SHALL | Mandatory | The regulation or statute requires it |
| MUST NOT / SHALL NOT | Mandatory prohibition | The regulation prohibits it |
| SHOULD / SHOULD NOT | Recommended | Best practice or guidance recommends it |
| MAY | Optional | Permitted but not required |

Never invent obligation strength. If the original clause says "should", the
rewrite says "should" unless a cited regulation justifies upgrading to "must".

## Rewrite-wave keep criteria

A wave is **kept** only if all of the following are true:

1. All in-scope obligations remain preserved or explicitly escalated for human
   decision.
2. Scenario decisions remain unchanged unless the human approved a policy
   change.
3. No RFC 2119 strength changes are unsupported.
4. Clause IDs and frontmatter are preserved unless authorised.
5. The editor accepted the draft or accepted the narrowed subset that was
   applied.
6. The verifier records residual risks clearly.

If any criterion fails, the verifier marks the wave **escalate** or **reject**.

## Surgical-mode criteria

Use surgical mode when a high-risk fragment needs the old assurance style.
Triggers include:

- retention period changes
- statutory scope changes
- MUST/SHALL/SHOULD/MAY strength changes
- uncertain obligation preservation
- scenario parity failures
- legal interpretation not resolved by the packet

In surgical mode, evaluate the affected clause or clause pair using the legacy
scorer and parity files. Do not expand surgical mode to the whole wave unless the
whole wave is high risk.

## Compact quality rubric

The verifier assesses changed regions using:

| Dimension | What it measures |
|-----------|------------------|
| Obligation preservation | All original obligations retained |
| Scenario parity | Same policy decisions for relevant scenarios |
| Clarity | Easier to understand and apply |
| Testability | Clear triggers, thresholds, evidence, and expected outcomes |
| Operational executability | Named roles, systems, workflow steps, and cadence |
| Readability | Simpler prose without loss of meaning |
| Consistency | Fits nearby clauses and avoids contradictions |

Use `improved`, `unchanged`, `mixed`, or `degraded` unless surgical mode requires
numeric scoring.

## Legacy scoring weights

When surgical mode requires numeric scoring, use the existing weights:

| Dimension | Weight |
|-----------|--------|
| Obligation Preservation | 0.30 |
| Ambiguity Reduction | 0.25 |
| Readability Improvement | 0.15 |
| Testability | 0.15 |
| Operational Executability | 0.15 |

The legacy composite score is a surgical-mode diagnostic, not the default wave
selection mechanism.
