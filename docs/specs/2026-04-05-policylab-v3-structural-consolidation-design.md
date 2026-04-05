# Policy Lab v3 — Inter-Standard Structural Consolidation

**Date:** 2026-04-05
**Status:** Approved design, pending implementation plan
**Scope:** Architecture for cross-standard structural analysis and consolidation

---

## Problem Statement

Policy Lab v2 operates at the clause level within a single standard: one clause, one dimension, one pass. It cannot detect or resolve structural issues that span multiple standards — contradictions, duplicated obligations, or opportunities to consolidate overlapping clauses. As more standards are added to the framework, these cross-cutting issues become the dominant source of complexity and inconsistency.

## Design Goals

1. **Coherence** — find and resolve contradictions between standards (conflicting retention periods, incompatible RFC 2119 strengths for the same obligation)
2. **Deduplication** — establish a single authoritative location for each obligation across the framework, with cross-references elsewhere
3. **Simplification** — reduce total clause count by merging overlapping or near-identical clauses, without losing obligations ("as simple as possible, but no simpler")
4. **Regulatory traceability** — every structural change must demonstrably preserve the link from regulatory obligation to implementing clause, especially when merging clauses that derive from different regulatory sources

## Architecture: Two-Loop Model

The system operates as an outer loop (structural) and inner loop (clause quality). The outer loop analyses the framework, produces a prioritised backlog of structural changes, and — after human approval — executes them. Where structural changes affect clause text, the outer loop seeds the inner loop with re-scoring work.

```
Outer Loop (structural)
  ├── Build obligation-clause graph
  ├── Analyse: coherence → deduplication → simplification
  ├── Produce prioritised backlog
  ├── [HUMAN APPROVAL GATE]
  ├── Execute approved structural changes
  ├── Seed inner loop queue with affected clauses
  ├── Rebuild graph, measure impact
  └── Report: progress + further opportunity assessment
        └── [HUMAN DECIDES: continue or stop]

Inner Loop (clause quality) — unchanged from v2
  ├── Plan 5 passes (may include outer-loop-seeded items)
  ├── Execute passes (score → rewrite → score → parity → keep/discard)
  ├── Re-assess coverage
  └── Report + stop
```

### Stop Conditions

- **Inner loop:** Measurable — composite score thresholds, coverage targets, parity pass rates. Unchanged from v2.
- **Outer loop:** Qualitative. Each run reports a "further opportunity" assessment. The human decides whether to continue based on whether meaningful structural improvements remain. The system's job is to surface honest observations, not to decide when to stop.

---

## The Obligation-Clause Graph

The graph is the rigorous foundation for all outer loop analysis. It maps relationships between three layers:

```
Regulatory Obligations  →  Clauses  →  Standards
       (leaf nodes)        (middle)     (containers)
```

### Nodes

| Node type | Source | Example |
|-----------|--------|---------|
| **Obligation** | `regulatory_reference/` | UK GDPR Art 5(1)(e) — storage limitation principle |
| **Clause** | `standards/*.md` | RKS-5.8 |
| **Standard** | `standards/*.md` | Record Keeping Standard |

### Edges

| Edge type | Direction | Meaning | Properties |
|-----------|-----------|---------|------------|
| **implements** | clause → obligation | This clause satisfies this obligation | strength: `full` or `partial` |
| **overlaps** | clause ↔ clause | Both clauses address the same obligation/topic | Key edge for deduplication |
| **contradicts** | clause ↔ clause | Incompatible statements about the same topic | Description of contradiction |
| **cross-references** | clause → clause | Explicit "see clause X" in the text | Already exists in source |
| **belongs-to** | clause → standard | Structural containment | |

### Graph lifecycle

- **Rebuilt** at the start of each outer loop run (derived, not manually maintained)
- **Persisted** as `structural-runs/srun-NNN/graph.yaml` for auditability
- The coverage assessor (v2) already does obligation → clause mapping for a single standard; the graph builder extends this across all standards

### What the graph enables

- "Which obligations are implemented by clauses in more than one standard?" → deduplication candidates
- "Which clauses implement the same obligation with different language?" → coherence check
- "Which obligations have no implementing clause anywhere?" → framework-level coverage gaps
- "If I merge these two clauses, which obligations are affected?" → impact analysis before structural changes

---

## File Layout

```
policylab-v3/
├── programme.md                  ← Inner loop orchestrator (carried from v2)
├── programme-structural.md       ← Outer loop orchestrator (new)
├── constraints.md                ← Inner loop guardrails (carried from v2)
├── constraints-structural.md     ← Outer loop guardrails (new)
├── standards/                    ← All standards (shared by both loops)
├── regulatory_reference/         ← Ground truth (shared, read-only)
├── scenario_packs/               ← Test scenarios (shared, add-only)
├── agents/
│   ├── [existing inner loop agents from v2]
│   ├── graph-builder.md          ← Builds the obligation-clause graph
│   ├── coherence-checker.md      ← Finds contradictions across standards
│   ├── deduplication-analyser.md ← Finds overlapping clauses across standards
│   ├── simplification-proposer.md← Proposes merges and consolidations
│   └── structural-rewriter.md    ← Executes approved structural changes
├── runs/                         ← Inner loop runs (carried from v2)
├── structural-runs/              ← Outer loop runs (new)
│   └── srun-NNN/
│       ├── graph.yaml            ← Obligation-clause graph for this run
│       ├── BACKLOG.md            ← Prioritised structural changes
│       ├── STRUCTURAL_SUMMARY.md ← Progress + further opportunity assessment
│       └── results.tsv           ← Per-item outcomes
├── blog.md                       ← Progress narrative (shared by both loops)
└── docs/
    └── specs/
        └── [this document]
```

---

## Outer Loop Run Sequence

### 1. Build graph

`graph-builder` reads all standards in `standards/` and all files in `regulatory_reference/`. Produces `graph.yaml` with all nodes and edges.

### 2. Analyse

Run three analysers in sequence, each reading the graph:

1. **Coherence checker** — find contradictions (conflicting retention periods, incompatible obligation strengths, inconsistent role assignments for the same topic)
2. **Deduplication analyser** — find obligation clusters where multiple clauses across standards implement the same obligation
3. **Simplification proposer** — propose merges, condensations, and clause removals (with cross-reference replacement) for clusters identified by the deduplication analyser

### 3. Produce backlog

Prioritise findings into a ranked list:
1. Contradictions first (coherence)
2. Duplications second (single source of truth)
3. Simplifications third (reduce volume)

Each backlog item includes:
- Type: `CONTRADICTION`, `DUPLICATION`, or `SIMPLIFICATION`
- Affected clauses (with standard references)
- Affected obligations (with regulatory citations)
- Recommended action
- Traceability: which obligations are satisfied before and after the change

### 4. Human approval gate

Present `BACKLOG.md` to the human. Human may approve all, approve some, modify, or reject. The outer loop does not execute without approval.

### 5. Execute

`structural-rewriter` executes each approved backlog item. Actions may include:
- Adding cross-references between clauses
- Merging clause text (preserving all obligations from both sources)
- Moving a clause from one standard to another (leaving a cross-reference at the original location)
- Creating new sections within an existing standard to house merged content
- Resolving contradictions by aligning to the stricter/more specific requirement

After each item, verify the traceability invariant (see Constraints below).

### 6. Seed inner loop queue

Clauses modified by structural changes are added to a queue file that the inner loop's planner reads. These clauses need re-scoring on all five quality dimensions.

### 7. Rebuild graph and measure impact

Regenerate the graph from the modified standards. Compare structural health scores before and after.

### 8. Report

Write `STRUCTURAL_SUMMARY.md` with:
- What was executed
- Structural health scores before and after
- "Further opportunity" assessment — honest observations about remaining contradictions, duplication clusters, simplification candidates
- Recommendation: continue, pause, or done

### 9. Stop

Human reviews the summary and decides whether to run another outer loop iteration.

---

## Backlog Format

```markdown
# Structural Backlog — SRun NNN — YYYY-MM-DD

## Graph summary
| Metric | Value |
|--------|-------|
| Standards loaded | N |
| Total clauses | N |
| Total obligations | N |
| Contradictions found | N |
| Duplication clusters | N |
| Simplification candidates | N |

## Backlog (priority order)

### 1. [CONTRADICTION] Short description
- **Clauses:** STD-X.X vs STD-Y.Y
- **Issue:** Description of the contradiction
- **Obligations affected:** Citation 1, Citation 2
- **Recommended action:** What to do and why
- **Traceability:** Which obligations are satisfied before and after

### 2. [DUPLICATION] Short description
- **Clauses:** STD-X.X, STD-Y.Y, STD-Z.Z
- **Issue:** Same obligation stated in N places
- **Recommended action:** Designate authoritative clause, replace others with cross-references
- **Traceability:** Obligation mapping before and after

### 3. [SIMPLIFICATION] Short description
- **Clauses:** STD-X.X, STD-X.Y
- **Issue:** Near-identical content that could be merged
- **Recommended action:** Merge into single clause
- **Traceability:** All obligations preserved in merged clause
```

---

## Structural Constraints

### What the outer loop CAN do (after human approval)

- Add cross-references between clauses in different standards
- Merge clauses within or across standards where they implement the same obligations
- Resolve contradictions by aligning clause language to the stricter/more specific requirement, with regulatory justification
- Move a clause from one standard to another, leaving a cross-reference at the original location
- Simplify clause text during merges (removing duplication), provided obligation preservation scores 1.0
- Create new sections within an existing standard to house merged content
- Seed the inner loop queue with clauses that need re-scoring after structural changes

### What the outer loop CANNOT do

- Create or delete entire standards (human architectural decision — surface as a recommendation in the summary)
- Remove obligations — every obligation in the graph before a structural change must be traceable after
- Execute without an approved backlog — the human approval gate is mandatory
- Modify regulatory references — read-only ground truth
- Change obligation strength — if one source says MUST and another says SHOULD for the same topic, flag it; don't silently pick one

### The Traceability Invariant

> Before and after every structural change, every in-scope regulatory obligation must map to at least one clause with strength `full` or `partial`. No obligation may lose its implementing clause without gaining a new one.

This is the structural equivalent of the inner loop's `obligation_preservation >= 1.0` rule. The graph makes it mechanically verifiable.

---

## Structural Scoring Dimensions

| Dimension | What it measures | Computation |
|-----------|-----------------|-------------|
| **Coherence** | Absence of contradictions | `1 - (contradictions / total_clause_pairs_sharing_an_obligation)` |
| **Duplication index** | Single-source-of-truth adherence | `1 - (obligations_with_multiple_implementing_clauses / total_obligations)` |
| **Traceability completeness** | Every obligation has a home | `obligations_with_full_coverage / total_in_scope_obligations` |
| **Framework simplicity** | Clause economy | Inverse of total clause count, normalised against baseline (first run) |

These produce a **structural health score** reported at the end of each run. Unlike the inner loop's composite, this is not used for keep/discard — it informs the "further opportunity" narrative.

Example narrative:

> Coherence is now 1.0 (no contradictions remain). Duplication index improved from 0.72 to 0.85 — three obligation clusters still have multiple implementing clauses. Simplification opportunity remains moderate: clauses RKS-5.3 and HRP-4.1 are near-identical and could be merged in the next run. Recommend continuing.

---

## What Carries Forward from v2

- All inner loop agents (`scorer-*.md`, `rewriter.md`, `parity-tester.md`, `coverage-assessor.md`)
- `programme.md` (inner loop orchestrator) — minor update to read the inner loop queue seeded by the outer loop
- `constraints.md` (inner loop guardrails) — unchanged
- The record-keeping standard and its regulatory reference — as the initial test case
- Run history in `runs/` — preserved for continuity
- `blog.md` — extended to cover both loop types

## What's New in v3

- `programme-structural.md` — outer loop orchestrator
- `constraints-structural.md` — outer loop guardrails
- Five new agents: `graph-builder.md`, `coherence-checker.md`, `deduplication-analyser.md`, `simplification-proposer.md`, `structural-rewriter.md`
- `structural-runs/` directory with its own run tracking
- Graph persistence (`graph.yaml`) per structural run
- Backlog format and approval workflow
- Structural scoring dimensions
- Inner loop queue mechanism (outer loop seeds inner loop work)
