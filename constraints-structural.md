# Policy Lab v3 — Structural Constraints

This document defines the immutable guardrails for the outer (structural) loop. All structural agent behaviour files reference this document. Violations of these constraints invalidate a structural change.

## What the outer loop CAN do (after human approval of the backlog)

- **Add cross-references** between clauses in different standards to link related obligations.
- **Merge clauses** within or across standards where they implement the same obligations. The merged clause must demonstrably satisfy all source obligations from all originating regulatory sources.
- **Resolve contradictions** by aligning clause language to the stricter or more specific requirement, with explicit regulatory justification for the choice.
- **Move a clause** from one standard to another, leaving a cross-reference at the original location that directs the reader to the new authoritative location.
- **Simplify clause text** during merges by removing duplicated language, provided the obligation preservation score remains >= 1.0 for every affected obligation.
- **Create new sections** within an existing standard to house merged content from multiple sources.
- **Seed the inner loop queue** with clauses that need re-scoring after structural changes.

## What the outer loop CANNOT do

- **Create or delete entire standards.** This is a human architectural decision. The outer loop may recommend it in the structural summary, but cannot execute it.
- **Remove obligations.** Every in-scope regulatory obligation that maps to a clause before a structural change must still map to a clause after the change. No exceptions.
- **Execute without an approved backlog.** The human approval gate is mandatory. The outer loop produces the backlog; the human approves, modifies, or rejects items; only then does execution begin.
- **Modify `regulatory_reference/` files.** These are read-only ground truth, shared with the inner loop.
- **Change obligation strength.** If one standard says MUST and another says SHOULD for the same topic, the outer loop must flag the discrepancy for human decision. It cannot silently choose one over the other.
- **Modify existing scenario pack files.** Scenario packs are immutable. The outer loop may recommend new scenarios to cover structural changes.

## The Traceability Invariant

> Before and after every structural change, every in-scope regulatory obligation must map to at least one clause with strength `full` or `partial`. No obligation may lose its implementing clause without gaining a new one.

This invariant is verified by rebuilding the obligation-clause graph after each backlog item is executed and confirming that no obligation's coverage has been lost.

This is the structural equivalent of the inner loop's `obligation_preservation >= 1.0` rule.

## Obligation language

When merging or rewriting clauses, the structural rewriter must follow the same RFC 2119 rules as the inner loop (see `constraints.md`):

| Keyword | Strength | Use when |
|---------|----------|----------|
| MUST / SHALL | Mandatory | The regulation or statute requires it |
| MUST NOT / SHALL NOT | Mandatory prohibition | The regulation prohibits it |
| SHOULD / SHOULD NOT | Recommended | Best practice or guidance recommends it |
| MAY | Optional | Permitted but not required |

When two source clauses use different strengths for the same obligation, the structural rewriter must trace back to the regulatory source to determine the correct strength. If the sources genuinely differ (one regulation mandates, another only recommends), flag this for human decision.

## Structural scoring dimensions

These scores are reported after each outer loop run. They inform the "further opportunity" narrative but are NOT used for automatic keep/discard decisions.

| Dimension | What it measures | Computation |
|-----------|-----------------|-------------|
| Coherence | Absence of contradictions | `1 - (contradictions / total_clause_pairs_sharing_an_obligation)` |
| Duplication index | Single-source-of-truth adherence | `1 - (obligations_with_multiple_implementing_clauses / total_obligations)` |
| Traceability completeness | Every obligation has a home | `obligations_with_full_coverage / total_in_scope_obligations` |
| Framework simplicity | Clause economy | `baseline_clause_count / current_clause_count` (normalised so baseline = 1.0; lower clause count = higher score) |

## Backlog item types

Each backlog item must be classified as one of:

| Type | Priority | Description |
|------|----------|-------------|
| CONTRADICTION | 1 (highest) | Two clauses make incompatible statements about the same topic |
| DUPLICATION | 2 | The same obligation is implemented in multiple clauses across standards |
| SIMPLIFICATION | 3 | Near-identical clauses can be merged to reduce volume without losing obligations |

Within each type, items are ranked by the number of obligations affected (more = higher priority).
