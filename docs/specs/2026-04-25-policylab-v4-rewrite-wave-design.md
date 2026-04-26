# Policy Lab v4 - Rewrite Wave Design

**Date:** 2026-04-25
**Status:** Implemented as default programme
**Scope:** Higher-throughput policy rewriting with strategy/backlog/queue
planning and author/editor control

## Problem

The v2/v3 inner loop treated one clause and one dimension as the atomic unit of
work. A single pass could require reading the standard, constraints, five scorer
rubrics, the rewriter, scenario packs, and coverage material, then producing a
detailed audit for a small textual change.

That design is careful, but it spends too much context on repeated ceremony. It
also prevents the model from improving policy flow across related clauses, which
is often where the largest gains sit.

## Design change

v4 introduces a defined-estate intake step and a three-tier planning structure
above the rewrite wave:

```text
defined estate intake -> policy architecture strategy -> structural backlog -> rewrite-wave queue
```

The queue then feeds the inner loop:

```text
rewrite-wave queue -> packet -> draft -> editor review -> verification
```

This means a wave should not be an isolated local improvement. It should be an
executable step in a wider architecture plan.

Defined estate intake is scoped by the user. For example, "all data standards"
means the bounded population of data standards provided or identified for the
run, not every bank policy. The intake records included standards, excluded
standards or domains, known gaps, and confidence in completeness.

v4 also changes the atomic rewrite unit from a clause to a **rewrite wave**:

> one coherent policy capability, one compact evidence packet, one authoring
> pass, one editorial review, one verification pass.

Examples of policy capabilities:

- retention tiers and expiry actions
- statutory-function records
- HR and finance retention schedules
- personal data retention
- official record location and SharePoint/email handling

## Roles

| Role | Purpose |
|------|---------|
| Policy Architect | Records defined estate scope and builds the policy architecture strategy, structural backlog, and rewrite-wave queue |
| Planner | Selects the wave and builds the compact packet |
| Author | Rewrites all in-scope clauses as a coherent region |
| Editor | Challenges obligation preservation, scope, strength, and parity |
| Verifier | Checks the applied diff, updates maps, and produces the audit |

The author is allowed to make a meaningful improvement. The editor and verifier
constrain risk.

## Persistent maps

v4 introduces `maps/` as lightweight reusable context:

- `policy-map.md`
- `obligation-register.yaml`
- `clause-obligation-map.yaml`
- `scenario-index.yaml`
- `quality-ledger.tsv`

The maps reduce repeated discovery. They are not authoritative sources; they are
indexes into the authoritative standard, regulatory reference, and scenarios.

## Planning artifacts

| File | Purpose |
|------|---------|
| `policy-architecture-strategy.md` | Macro estate strategy, authority model, consolidation options, human decisions |
| `structural-backlog.md` | Ranked architecture and capability work derived from the strategy |
| `rewrite-wave-queue.md` | Executable wave handoff from outer loop to inner loop |

The queue was intentionally named `rewrite-wave-queue.md` because it is produced
by the outer loop but consumed by the inner loop.

## Example: two standards with overlapping retention language

If the estate contains both a Record Keeping Standard and an Information
Governance Standard, and both contain personal-data retention rules, the outer
loop first creates a strategy:

```markdown
Recommendation: Hub and spoke.

RKS is authoritative for retention periods and disposal actions.
IGS is authoritative for privacy governance responsibilities.
IGS should cross-reference RKS-5.8 rather than restating storage-limitation
retention rules.
```

That becomes a structural backlog item:

```markdown
### personal_data_authority_model - Choose authoritative home

- **Type:** architecture_decision
- **Status:** needs_strategy_decision
- **Recommended strategy:** Make RKS authoritative for retention periods and IGS
  authoritative for privacy governance.
```

Only after the authority model is approved should the architect add executable
items to `rewrite-wave-queue.md`.

## Risk handling

The legacy one-clause inner loop is retained as **surgical mode**. Use it only
for high-risk fragments:

- retention period changes
- statutory scope changes
- RFC 2119 strength changes
- uncertain obligation preservation
- scenario parity failures

This keeps the assurance method available without making every routine rewrite
pay for it.

## Expected benefits

- More policy text improved per run
- Less repeated loading of rubrics and sources
- Better cross-clause consistency
- Clearer separation between drafting and review
- Faster human review because each run has a topic, packet, draft, review, and
  verification summary
- A visible chain from macro strategy to executable rewrite work

## Non-goals

- Replacing the regulatory reference as source of truth
- Removing human approval for structural changes
- Automating legal interpretation beyond the evidence in the packet
- Treating scenario parity failures as acceptable without human approval

## Migration notes

- `programme.md` is now the v4 rewrite-wave programme.
- `programme-structural.md` now owns strategy, backlog, and queue generation,
  with full graph analysis reserved for multi-standard consolidation.
- `agents/architect.md` owns the macro policy architecture instructions.
- Existing scorer, coverage, parity, and graph files remain in `agents/` for
  surgical mode and full structural mode.
