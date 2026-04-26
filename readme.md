# Policy Intelligence Lab

An agentic framework for improving policy documents while preserving regulatory
obligations, scenario decisions, and an auditable change trail.

The current architecture is v4. It is designed for higher progress per run than
the original one-clause micro-pass loop. Instead of spending a full run on one
clause and one quality dimension, v4 works in **rewrite waves**: one coherent
policy capability, one compact evidence packet, one authoring pass, one editorial
review, and one verification pass.

## How it works

The framework has two loops:

**Inner loop - rewrite waves.** The default path. A planner selects a topic
cluster, builds a compact packet, an author rewrites the relevant clauses as one
policy region, an editor challenges the draft, and a verifier checks obligations,
scenario parity, and map updates.

**Outer loop - structure and queueing.** Maintains compact maps, identifies
coverage or structural opportunities, sets the architecture strategy, creates a
structural backlog, and queues high-value rewrite waves. When the corpus grows
to multiple standards, the outer loop can still run a full obligation-clause
graph to detect contradictions, duplication, and consolidation opportunities.

For a larger estate, the outer loop starts with **defined estate intake**. The
user defines the bounded population of standards being assessed, such as "all
data standards", "the standards owned by the CDO function", or "these uploaded
standards only". The framework should not assume it is reviewing every bank
policy unless that is the explicit population.

The legacy one-clause scorer workflow remains available as **surgical mode** for
high-risk changes involving retention periods, legal scope, obligation strength,
or scenario parity uncertainty.

## Architecture

```
programme.md                  Default v4 rewrite-wave orchestrator
programme-structural.md       Outer-loop map, queue, and structural orchestrator
constraints.md                Inner-loop guardrails
constraints-structural.md     Structural guardrails
policy-architecture-strategy.md
                              Macro estate strategy and authority model
structural-backlog.md         Ranked structural/capability backlog
rewrite-wave-queue.md         Handoff queue from outer loop to inner loop

agents/
  architect.md                  Builds strategy, backlog, and queue
  planner.md                    Builds compact rewrite packets
  author.md                     Rewrites coherent policy regions
  editor.md                     Reviews drafts for obligation, scope, and parity risk
  verifier.md                   Verifies applied waves and updates maps

  scorer-*.md                   Legacy surgical-mode scorers
  rewriter.md                   Legacy one-clause rewriter
  parity-tester.md              Scenario parity tester
  coverage-assessor.md          Obligation coverage assessor
  graph-builder.md              Full structural graph builder
  coherence-checker.md          Full contradiction checker
  deduplication-analyser.md     Full duplication analyser
  simplification-proposer.md    Full simplification proposer
  structural-rewriter.md        Approved structural-change executor

maps/
  policy-map.md                 Standards, sections, topics, and notes
  obligation-register.yaml      Compact obligation cards and scope notes
  clause-obligation-map.yaml    Clause-to-obligation traceability
  scenario-index.yaml           Scenario-to-topic index
  quality-ledger.tsv            Known quality/risk state by topic

standards/                     Policy documents being improved
regulatory_reference/          Read-only ground truth obligations
scenario_packs/                Immutable scenario tests; add-only
runs/                          Rewrite-wave run history
structural-runs/               Full structural run history, when used
```

## Key concepts

- **Policy architecture strategy** - the macro view of what the policy estate
  should become and which standard is authoritative for which topic.
- **Structural backlog** - ranked work derived from the strategy.
- **Rewrite wave** - one topic cluster, several related clauses, all quality
  objectives handled together.
- **Rewrite-wave queue** - the outer-loop handoff to the inner loop. The outer
  loop decides what belongs in the queue; the inner loop executes one item.
- **Packet** - compact context bundle for a wave: clauses, obligation cards,
  scenario cards, weaknesses, target outcomes, and constraints.
- **Author/editor split** - the author makes the improvement; the editor protects
  obligations, scope, strength, and parity.
- **Verifier** - produces the audit trail and escalates risky fragments.
- **Surgical mode** - legacy one-clause, multi-scorer assurance used only for
  high-risk fragments.
- **Persistent maps** - lightweight indexes that prevent every run from
  rediscovering the entire repository.

## Usage

For macro planning, strategy, backlog generation, queue generation, or
cross-standard consolidation, point the harness at:

```
programme-structural.md
```

For normal policy rewriting once the queue exists, point the harness at:

```
programme.md
```

Typical outer-loop planning flow:

1. Architect records the defined estate boundary, if a scoped population was
   provided.
2. Architect updates `policy-architecture-strategy.md`.
3. Architect updates `structural-backlog.md`.
4. Architect writes the next executable items to `rewrite-wave-queue.md`.
5. Stop for human review or switch to the inner loop.

Typical inner-loop rewrite flow:

1. Planner selects a topic cluster and writes `runs/wave-NNN/PACKET.md`.
2. Author writes `runs/wave-NNN/DRAFT.md`.
3. Editor writes `runs/wave-NNN/EDITOR_REVIEW.md`.
4. Accepted text is applied to `standards/*.md`.
5. Verifier writes `VERIFY.md`, `RUN_SUMMARY.md`, `results.tsv`, updates maps,
   appends to `blog.md`, and stops.

## Planning Example

Suppose the estate grows to two standards:

- Record Keeping Standard
- Information Governance Standard

and both contain personal-data retention wording. The outer loop should not jump
straight into rewriting a clause. It should first produce a strategy decision:

```markdown
Recommendation: Hub and spoke.

RKS is authoritative for retention periods and disposal actions.
IGS is authoritative for privacy governance responsibilities.
IGS should cross-reference RKS-5.8 rather than restating storage-limitation
retention rules.
```

That strategy becomes a backlog item:

```markdown
### personal_data_authority_model - Choose authoritative home

- **Type:** architecture_decision
- **Status:** needs_strategy_decision
- **Recommended strategy:** Make RKS authoritative for retention periods and IGS
  authoritative for privacy governance.
```

After the human approves the authority model, the queue gets executable waves:

```markdown
## 1. personal_data_authority_model

- **Objective:** Implement the approved authority model for personal-data
  retention.
- **Risk tier:** high
- **Likely clauses:** RKS-5.8, IGS-3.2
- **Surgical triggers:** Any change to lawful basis, retention period, or MUST
  NOT language.

## 2. personal_data_reader_flow

- **Objective:** Clean up reader-facing wording after the authority model is in
  place.
- **Risk tier:** medium
```

That is the intended chain:

```text
defined estate intake -> policy architecture strategy -> structural backlog -> rewrite-wave queue -> packet -> draft -> review -> verify
```

The first step is scoped. For example, "all data standards" means the data
standards population provided or identified by the user, not the entire bank
policy estate.

## Guardrails

- Obligations are never removed or weakened.
- RFC 2119 strength is not changed without source support.
- Regulatory references are read-only.
- Existing scenario packs are immutable; new scenarios may be added.
- Clause IDs and frontmatter are preserved unless explicitly authorised.
- Scenario decisions must remain the same unless the human approves a policy
  change.
- High-risk fragments are escalated to surgical mode or human review.

## Current corpus

- 1 standard loaded: Record Keeping Standard / Information Management:
  Retention and Disposal Policy
- 33 clause headings in the current standard
- 1 regulatory workbook under `regulatory_reference/record-keeping/`
- 4 scenario packs with 14 scenarios
- Persistent maps initialised under `maps/`
- Full cross-standard structural analysis is available, but currently limited by
  the single-standard corpus

## Design intent

The framework is meant to spend tokens where they create policy progress:

- build one focused packet per wave
- rewrite coherent policy regions instead of isolated sentences
- review drafts through a strict editor
- verify only changed material
- reserve expensive micro-scoring for genuinely risky clauses

This keeps the safety case from the original design while making each run much
more likely to produce a noticeable improvement.
