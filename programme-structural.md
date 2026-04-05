# Policy Intelligence Lab v3 — Structural Programme

This is the outer loop orchestrator. It manages the structural analysis and consolidation of the policy framework across multiple standards. The inner loop (clause-level quality improvement) is managed by `programme.md`.

## How the two loops relate

```
Outer Loop (you are here)
  │
  ├── Analyses the FRAMEWORK (all standards together)
  ├── Produces structural changes (merges, cross-references, contradiction resolutions)
  ├── Seeds the inner loop with clauses that need re-scoring
  │
  └── Inner Loop (programme.md)
        ├── Analyses individual CLAUSES within a single standard
        ├── Produces clause-level quality improvements
        └── Reads the inner loop queue seeded by the outer loop

The outer loop runs first. After structural changes are complete, the inner loop runs to re-score and improve affected clauses.
```

## File map

```
policylab-v3/
├── programme-structural.md       ← You are here. The structural orchestrator.
├── programme.md                  ← Inner loop orchestrator.
├── constraints-structural.md     ← Structural guardrails. Read before every structural run.
├── constraints.md                ← Inner loop guardrails (used by inner loop only).
├── standards/                    ← Policy documents (shared by both loops).
├── regulatory_reference/         ← Ground truth obligations (read-only, shared).
├── scenario_packs/               ← Test scenarios (shared, add-only).
├── agents/
│   ├── graph-builder.md          ← Builds the obligation-clause graph
│   ├── coherence-checker.md      ← Finds contradictions
│   ├── deduplication-analyser.md ← Finds overlapping clauses
│   ├── simplification-proposer.md← Proposes merges and consolidations
│   ├── structural-rewriter.md    ← Executes approved structural changes
│   └── [inner loop agents]       ← Used by programme.md, not by this file
├── structural-runs/              ← One subfolder per structural run
│   └── srun-NNN/
│       ├── graph.yaml
│       ├── BACKLOG.md
│       ├── STRUCTURAL_SUMMARY.md
│       └── results.tsv
├── inner-loop-queue.md           ← Clauses seeded for inner loop re-scoring
└── blog.md                       ← Shared progress narrative
```

## Setup (first time only)

1. **Read `constraints-structural.md`** — understand what the outer loop can and cannot do.
2. **Read all standards** — `standards/*.md`. Understand the scope of the framework.
3. **Read all regulatory references** — `regulatory_reference/*/`. Understand the obligation landscape.
4. **Check for prior structural runs** — read `structural-runs/srun-*/STRUCTURAL_SUMMARY.md` to understand history.
5. **Initialise run tracking** — create `structural-runs/srun-001/` if this is the first run.
6. **Confirm with the user** — report how many standards are loaded, how many obligations exist, and any notable observations. Await go-ahead.

## The structural run

Execute in this order:

### Phase 1: Build the graph

Read `agents/graph-builder.md` and follow its procedure exactly. Provide it with all standards and regulatory references. The graph builder produces `structural-runs/srun-NNN/graph.yaml`.

### Phase 2: Analyse

Run the three analysers in sequence. Each reads the graph and produces findings.

1. **Coherence** — read `agents/coherence-checker.md`. Check for contradictions.
2. **Deduplication** — read `agents/deduplication-analyser.md`. Check for overlapping clauses.
3. **Simplification** — read `agents/simplification-proposer.md`. Check for merge opportunities.

The order matters: coherence findings inform deduplication (don't deduplicate contradictory clauses), and deduplication findings inform simplification (clusters identified by deduplication are the primary merge candidates).

### Phase 3: Produce the backlog

Combine the findings from all three analysers into a single prioritised backlog. Write it to `structural-runs/srun-NNN/BACKLOG.md` using this format:

```markdown
# Structural Backlog — SRun NNN — YYYY-MM-DD

## Graph summary
| Metric | Value |
|--------|-------|
| Standards loaded | N |
| Total clauses | N |
| Total obligations (in-scope) | N |
| Contradictions found | N |
| Duplication clusters | N |
| Simplification candidates | N |

## Structural health (baseline)
| Dimension | Score |
|-----------|-------|
| Coherence | X.XXX |
| Duplication index | X.XXX |
| Traceability completeness | X.XXX |
| Framework simplicity | 1.000 (baseline) |

## Backlog (priority order)

### 1. [TYPE] Short description
- **Clauses:** ...
- **Issue:** ...
- **Obligations affected:** ...
- **Recommended action:** ...
- **Traceability:** ...

[... additional items ...]
```

### Phase 4: STOP — Human approval gate

**Present the backlog to the user.** Report:
- The graph summary
- The structural health baseline scores
- Each backlog item with its recommended action

**Do not proceed until the human approves.** The human may:
- Approve all items
- Approve some and reject others
- Modify the recommended actions
- Reject all items and request a different analysis focus

### Phase 5: Execute approved items

Read `agents/structural-rewriter.md` and follow its procedure for each approved backlog item, in priority order.

### Phase 6: Rebuild graph and score

After all items are executed:
1. Re-run `agents/graph-builder.md` to rebuild the graph from the modified standards.
2. Compute the four structural health scores from the new graph.
3. Compare before and after scores.

### Phase 7: Write structural summary

Write `structural-runs/srun-NNN/STRUCTURAL_SUMMARY.md`:

```markdown
# Structural Summary — SRun NNN — YYYY-MM-DD

## Results
| # | Type | Action | Status | Traceability |
|---|------|--------|--------|--------------|
| 1 | ... | ... | executed/skipped | preserved |

**Executed: X/Y items**

## Structural health
| Dimension | Before | After | Delta |
|-----------|--------|-------|-------|
| Coherence | X.XXX | Y.YYY | +Z.ZZZ |
| Duplication index | X.XXX | Y.YYY | +Z.ZZZ |
| Traceability completeness | X.XXX | Y.YYY | +Z.ZZZ |
| Framework simplicity | X.XXX | Y.YYY | +Z.ZZZ |

## Inner loop queue
N clauses seeded for re-scoring. See `inner-loop-queue.md`.

## Further opportunity assessment
[Honest narrative about what remains:]
- Are there more contradictions? How many?
- Are there more duplication clusters? What topics?
- Are there more simplification opportunities? What scale?
- Recommendation: continue / pause / framework looks clean

## Lessons learned
- [What worked, what didn't, what to try differently]
```

### Phase 8: Update blog

Append a structural run entry to `blog.md`:

```markdown
---

## Structural Run NNN — YYYY-MM-DD

[2-4 paragraph narrative: what the graph revealed, what was changed, what improved,
what the human decided, any surprises. Include structural health scores before and after.
Honest about what's left to do.]

**Items:** X/Y executed | **Coherence:** X.XXX → Y.YYY | **Duplication:** X.XXX → Y.YYY
```

### Phase 9: STOP

Do not start another structural run automatically. The human reviews the summary and the "further opportunity" assessment, then decides whether to:
- Run another outer loop iteration
- Switch to inner loop runs to re-score structurally changed clauses
- Stop

## Directives

**ONE STRUCTURAL RUN, THEN STOP.** Execute the approved backlog, write the summary, then stop. The human reviews and decides what's next.

**COHERENCE BEFORE DEDUPLICATION BEFORE SIMPLIFICATION.** Always analyse and resolve in this order. You cannot safely deduplicate contradictory clauses, and you cannot safely simplify without first understanding the duplication landscape.

**GRAPH IS THE SOURCE OF TRUTH.** All analysis is grounded in the obligation-clause graph. Do not make structural recommendations based on text similarity alone — always trace back to obligations.

**TRACEABILITY INVARIANT IS NON-NEGOTIABLE.** Before and after every structural change, every in-scope obligation must map to at least one clause. If a change would break this, do not execute it.

**HUMAN APPROVAL IS MANDATORY.** The backlog is a recommendation. The human decides what gets executed.

**SEED, DON'T SCORE.** The outer loop identifies structural issues and fixes them. It does NOT re-score clause quality. That's the inner loop's job. After structural changes, seed the inner loop queue and let `programme.md` handle quality.

**FLAG, DON'T CREATE.** If the analysis reveals that an entirely new standard should exist (or an existing one should be retired), flag it in the summary. The outer loop cannot create or delete standards.

**READ THE AGENT FILES.** When building the graph, analysing, or rewriting, read the relevant agent behaviour file and follow its procedure exactly. The procedures, output formats, and worked examples are there for consistency and auditability.
