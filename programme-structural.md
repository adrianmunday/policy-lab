# Policy Intelligence Lab v4 - Structural Programme

This is the outer-loop programme. It owns the macro policy architecture, the
structural backlog, and the rewrite-wave queue consumed by `programme.md`.

The outer loop answers:

1. What should the policy estate become?
2. What structural work follows from that strategy?
3. Which rewrite waves should the inner loop execute next?

## Three-tier planning structure

| Tier | File | Owner | Purpose |
|------|------|-------|---------|
| 1 | `policy-architecture-strategy.md` | Outer loop / architect | Macro strategy, authority model, consolidation options, human decisions |
| 2 | `structural-backlog.md` | Outer loop / architect | Ranked structural and capability work derived from the strategy |
| 3 | `rewrite-wave-queue.md` | Outer loop produces, inner loop consumes | Executable rewrite waves for planner/author/editor/verifier |

The queue is deliberately named `rewrite-wave-queue.md`, not
`outer-loop-wave-queue.md` or `inner-loop-wave-queue.md`. It is the handoff point:
the outer loop decides what belongs in it; the inner loop executes one item at a
time.

## Relationship to the inner loop

```
Outer loop (this file)
  - maintain maps
  - decide policy architecture strategy
  - create structural backlog
  - populate rewrite-wave queue
  - run full structural graph analysis when multiple standards justify it

Inner loop (`programme.md`)
  - read rewrite-wave queue
  - build one compact packet for the next queued item
  - author a coherent rewrite
  - editor-review the draft
  - verify obligations and scenarios
  - update maps and stop
```

## Structural roles

| Role | File | Purpose |
|------|------|---------|
| Policy Architect | `agents/architect.md` | Strategy, backlog, and queue generation |
| Graph Builder | `agents/graph-builder.md` | Full obligation-clause graph for multi-standard analysis |
| Coherence Checker | `agents/coherence-checker.md` | Contradiction detection |
| Deduplication Analyser | `agents/deduplication-analyser.md` | Duplicate obligation analysis |
| Simplification Proposer | `agents/simplification-proposer.md` | Merge/consolidation proposals |
| Structural Rewriter | `agents/structural-rewriter.md` | Execute approved structural edits |

## Operating modes

| Mode | Use when | Output |
|------|----------|--------|
| Defined estate intake | The user provides a bounded population of standards to assess | Updated estate scope in strategy and maps |
| Strategy | A standard is added, overlap is suspected, or the estate needs an authority model | Updated `policy-architecture-strategy.md` |
| Backlog | Strategy exists but work has not been ranked | Updated `structural-backlog.md` |
| Queue | Backlog exists and executable rewrite waves are needed | Updated `rewrite-wave-queue.md` |
| Map refresh | Maps are missing or stale | Updated `maps/` entries |
| Structural graph | Multiple standards exist or cross-standard contradictions/duplication are suspected | `structural-runs/srun-NNN/` artifacts |
| Approved structural edit | Human approved merges, moves, cross-references, or contradiction resolutions | Edited standards plus updated backlog/queue |

For a newly provided population of standards, the normal mode sequence is:

```text
Defined estate intake -> Strategy -> Backlog -> Queue -> Inner rewrite wave
```

For the current single-standard corpus, the normal mode sequence is:

```text
Strategy -> Backlog -> Queue -> Inner rewrite wave
```

Use full structural graph mode when there are enough standards or suspected
cross-standard issues to justify the extra context.

## Setup

Read:

1. `constraints-structural.md`
2. `agents/architect.md`
3. `policy-architecture-strategy.md`
4. `structural-backlog.md`
5. `rewrite-wave-queue.md`
6. `maps/policy-map.md`
7. `maps/obligation-register.yaml`
8. `maps/clause-obligation-map.yaml`
9. `maps/scenario-index.yaml`
10. `maps/quality-ledger.tsv`

Read all standards only if the maps are stale or a strategy decision requires
raw clause comparison. Read raw regulatory references only when obligation cards
are insufficient.

## Mode selection

Choose one mode per outer-loop run:

- If strategy is missing, stale, or a new standard has been added, run
  **Strategy**.
- If the user provides a bounded population of standards, run **Defined estate
  intake** before strategy mode.
- If the strategy is current but the backlog does not reflect it, run
  **Backlog**.
- If the backlog is current but the queue is empty, stale, or missing high-value
  executable items, run **Queue**.
- If maps are stale enough to block strategy or queue decisions, run
  **Map refresh**.
- If multiple standards exist and overlap/contradiction needs evidence, run
  **Structural graph**.
- If the human has approved a structural backlog item, run **Approved structural
  edit**.

Run one mode and stop. The human decides the next action.

## Defined estate intake mode

Use this mode when the user provides a scoped population of standards, such as:

- "all data standards"
- "records, retention, privacy, and data governance standards"
- "the standards owned by the CDO function"
- "these uploaded standards only"
- "standards mapped to UK GDPR and operational resilience"

This mode does not assume enterprise-wide policy coverage. It records the scope
boundary so later strategy and backlog decisions do not overclaim.

Update `policy-architecture-strategy.md` and, where needed, `maps/policy-map.md`
with:

- population name
- included standards
- explicitly excluded standards or domains
- owner or policy domain, if known
- rationale for inclusion
- known missing standards or documents
- confidence in the completeness of the population

If the population boundary is ambiguous, ask the human to define it before
creating an authority model or consolidation strategy.

## Strategy mode

Read `agents/architect.md` and update `policy-architecture-strategy.md`.

The strategy must answer:

- which standards exist and what each is for
- which standard is authoritative for each major topic
- whether overlapping standards should be kept separate, consolidated, or
  arranged as hub-and-spoke
- which decisions require human approval before drafting
- what strategic actions should happen next

Do not produce rewrite waves until the strategy is clear enough to justify them.

## Backlog mode

Read `agents/architect.md` and update `structural-backlog.md`.

The backlog must translate strategy into ranked work. Each item should include:

- type
- priority
- status
- strategy rationale
- likely clauses
- obligations
- scenarios, if known
- expected wave or structural edit
- human decision needed, if any

Use `needs_strategy_decision` where the architecture is unresolved. Do not queue
that item for inner-loop rewriting until the decision is made.

## Queue mode

Read `agents/architect.md` and update `rewrite-wave-queue.md`.

Queue items must come from `structural-backlog.md`. Each item should include:

- source backlog item
- objective
- risk tier
- likely clauses
- obligations
- scenarios
- why now
- surgical triggers
- status

The queue should contain the next 3 to 7 executable waves, ordered by strategic
value and verification practicality.

## Map refresh mode

Update affected map entries only. Do not rebuild everything by default.

Refresh checks:

- `maps/policy-map.md` - standards, sections, and topic clusters
- `maps/obligation-register.yaml` - obligation cards and scope notes
- `maps/clause-obligation-map.yaml` - traceability links
- `maps/scenario-index.yaml` - scenario-topic links
- `maps/quality-ledger.tsv` - known risks and candidate status

After refresh, stop or continue to another mode only if the human explicitly
asked for a combined outer-loop run.

## Structural graph mode

Use this mode when a multi-standard estate needs evidence before strategy or
backlog decisions.

Follow this sequence:

1. Read `agents/graph-builder.md` and build `structural-runs/srun-NNN/graph.yaml`.
2. Read `agents/coherence-checker.md` and find contradictions.
3. Read `agents/deduplication-analyser.md` and find duplicated obligations.
4. Read `agents/simplification-proposer.md` and propose merges.
5. Update `policy-architecture-strategy.md` with the architectural implications.
6. Update `structural-backlog.md` with proposed decisions and work.
7. Stop for human approval where needed.

Do not execute structural edits without human approval.

## Approved structural edit mode

When the human approves backlog items:

1. Read `constraints-structural.md`.
2. Read `agents/structural-rewriter.md`.
3. Execute only the approved items.
4. Verify the traceability invariant.
5. Update maps.
6. Update `structural-backlog.md` item statuses.
7. Add affected topics or clauses to `rewrite-wave-queue.md` for rewrite-wave
   verification and polish.
8. Write `structural-runs/srun-NNN/STRUCTURAL_SUMMARY.md`.
9. Stop.

## Example flow

If two standards both contain personal-data retention rules:

1. Strategy mode decides whether to keep separate, consolidate, or use
   hub-and-spoke.
2. Backlog mode creates an item such as `personal_data_authority_model`.
3. Human approves the authority model if cross-standard consolidation is needed.
4. Queue mode adds `personal_data_authority_model` and
   `personal_data_reader_flow` to `rewrite-wave-queue.md`.
5. The inner loop executes one queued wave at a time.

## Structural constraints

The outer loop must not:

- create or delete entire standards without human approval
- remove obligations
- execute structural changes without an approved backlog item
- modify regulatory references
- modify existing scenario packs
- silently change obligation strength
- queue a rewrite wave that has no strategy or backlog rationale

If a proposed change would break traceability, do not execute it. Record a human
decision instead.

## Stop rule

Run one outer-loop action and stop:

- one strategy update
- one defined estate intake
- one backlog update
- one queue update
- one map refresh
- one structural graph/backlog
- or one approved structural edit batch
