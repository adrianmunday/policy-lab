# Policy Lab v2 — Constraints

This document defines the immutable guardrails for every agent in the Policy Lab. All agent behaviour files reference this document. Violations of these constraints invalidate a pass.

## What you CAN do

- **Modify clause body text** within `standards/*.md` files. This is the primary output. Rewrite obligations, add thresholds, clarify evidence requirements, strengthen control mappings, reduce ambiguity, improve structure.
- **Add new test scenarios** to `scenario_packs/` if you identify coverage gaps. Existing scenarios are immutable — you may only add, never modify or delete.

## What you CANNOT do

- **Modify `regulatory_reference/`**. These files are read-only ground truth.
- **Remove or weaken an existing obligation**. Obligation preservation must score >= 1.0 on every kept pass.
- **Delete or modify existing scenario pack files**. You may add new packs; you may not alter existing ones.
- **Create new clauses or new sections** in the standard. If a regulatory obligation has no corresponding clause, flag it in the run summary — do not invent structure.
- **Modify YAML frontmatter fields** (standard_id, owner, regulatory_sources, status) unless programme.md explicitly authorises it.
- **Renumber or reorganise clause identifiers** (e.g. RKS-3.2 stays RKS-3.2).
- **Change more than one clause per pass**. Each pass is atomic: one clause, one dimension.
- **Upgrade guidance to mandatory or downgrade mandatory to guidance**. Match RFC 2119 keywords to the actual obligation strength in the source material.

## Obligation language

When the source text implies a mandatory requirement, use RFC 2119 language:

| Keyword | Strength | Use when |
|---------|----------|----------|
| MUST / SHALL | Mandatory | The regulation or statute requires it |
| MUST NOT / SHALL NOT | Mandatory prohibition | The regulation prohibits it |
| SHOULD / SHOULD NOT | Recommended | Best practice or guidance recommends it |
| MAY | Optional | Permitted but not required |

Never invent obligation strength. If the original clause says "should", the rewrite says "should" unless a cited regulation justifies upgrading to "must".

## Keep / Discard criteria

A rewritten clause is **KEPT** only if ALL of the following are true:

1. `obligation_preservation` >= 1.0 (no obligations lost)
2. The targeted dimension improved
3. No other dimension degraded materially (> 0.05 drop)
4. All parity tests pass (scenario outcomes unchanged)

If ANY criterion fails, the rewrite is **DISCARDED** and the original clause is restored.

## Scoring weights

| Dimension | Weight | What it measures |
|-----------|--------|------------------|
| Obligation Preservation | 0.30 | Are all original obligations retained? |
| Ambiguity Reduction | 0.25 | Have vague terms and undefined thresholds been reduced? |
| Readability Improvement | 0.15 | Is the text clearer, shorter sentences, lower grade level? |
| Testability | 0.15 | Can the clause be linked to explicit test cases? |
| Operational Executability | 0.15 | Can the clause be traced to controls, roles, systems? |

**Composite score** = weighted sum of all five dimensions.
