# Policy Intelligence Lab v4 - Rewrite Wave Programme

This is the default operating programme for improving policy documents with an
agentic harness such as Claude Code. It replaces the v2/v3 micro-pass loop as the
normal path.

The old loop optimised for maximum assurance on one clause and one dimension at
a time. That is still useful for high-risk text, but it is too token-expensive as
the main way to improve a policy. v4 uses **rewrite waves**: one coherent policy
capability, several related clauses, all quality objectives, one compact evidence
packet, one authoring pass, one editorial review, and one verification pass.

## Core idea

Treat the unit of progress as a **policy capability**, not an isolated clause.

Examples:

- Retention tiers, trigger points, and expiry actions
- Personal data retention and annual review
- Statutory-function records and referral records
- HR and finance retention schedules
- Email, SharePoint, personal drives, and official record location

Each wave should make visible policy progress while preserving regulatory
obligations, decision parity, clause IDs, and auditability.

Rewrite waves should normally come from `rewrite-wave-queue.md`. That queue is
created by the outer loop from `policy-architecture-strategy.md` and
`structural-backlog.md`, so the inner loop is executing a strategic plan rather
than inventing local work in isolation.

## Role model

The harness should use four compact roles:

| Role | File | Purpose |
|------|------|---------|
| Planner | `agents/planner.md` | Select the next topic cluster and build a compact rewrite packet |
| Author | `agents/author.md` | Rewrite the selected clauses as a coherent policy region |
| Editor | `agents/editor.md` | Challenge the draft for obligation loss, scope drift, strength changes, and parity breaks |
| Verifier | `agents/verifier.md` | Produce the audit trail, update maps, and decide whether the wave is keep, revise, or escalate |

Existing files such as `agents/scorer-*.md`, `agents/parity-tester.md`, and
`agents/coverage-assessor.md` remain available as specialist tools. Use them only
when the verifier escalates a clause to **surgical mode**.

## Persistent maps

The wave programme relies on lightweight, reusable maps so each run does not
have to rediscover the whole repo.

```
maps/
  policy-map.md                 Human-readable map of standards, sections, and clause purposes
  obligation-register.yaml      Normalised obligation cards and scope notes
  clause-obligation-map.yaml    Clause-to-obligation traceability map
  scenario-index.yaml           Scenario-to-clause and scenario-to-topic map
  quality-ledger.tsv            Latest known quality/risk state per clause or topic
```

If a map is missing or stale, update only the affected entries needed for the
current wave. Do not rebuild everything unless the structural loop requires it.

## Risk tiers

Each wave receives a risk tier from the planner.

| Tier | Typical change | Required assurance |
|------|----------------|--------------------|
| Low | Readability, duplication removal, formatting, clearer labels | Editor review plus verifier checklist |
| Medium | Operational roles, evidence, systems, cadence, workflow detail | Editor review, scenario parity, obligation spot-check |
| High | Retention period, legal scope, obligation strength, statutory interpretation | Surgical mode for affected clauses before keep |

Surgical mode means using the legacy one-clause scorers and parity tester for the
specific high-risk clause, not for the whole wave.

## Run directory

Each wave is logged under:

```
runs/wave-NNN/
  PACKET.md          Planner output
  DRAFT.md           Author output
  EDITOR_REVIEW.md   Editor output
  VERIFY.md          Verifier output
  RUN_SUMMARY.md     Human-readable summary
  results.tsv        Compact machine-readable log
```

Use the next available `wave-NNN` number. If no `runs/` directory exists, create
`runs/wave-001/`.

## Wave workflow

### 1. Load the minimum context

Read:

1. `constraints.md`
2. this programme
3. `agents/planner.md`
4. the relevant map files in `maps/`

Do not read every agent file by default. Do not load the full regulatory
workbook unless the selected topic requires raw obligation detail.

### 2. Select a topic cluster

The planner selects one coherent policy capability using this priority order:

1. Items in `rewrite-wave-queue.md`, if present
2. Topics with in-scope obligation gaps or partial coverage
3. Topics touched by several scenarios
4. Topics with repeated operational weaknesses in `quality-ledger.tsv`
5. High-value readability or duplication improvements

The planner writes `PACKET.md`. The packet is the only context the author should
need for the first draft.

If `rewrite-wave-queue.md` is missing or empty, the planner may select a topic
from maps and quality signals, but it must note that the outer loop should run
strategy/backlog/queue mode before the next wave.

### 3. Build a compact rewrite packet

`PACKET.md` must include:

- topic and objective
- risk tier
- clauses in scope, with current text
- clauses out of scope but nearby
- obligation cards relevant to the wave
- scenario cards relevant to the wave
- known weaknesses and target outcomes
- hard constraints
- acceptance checks

Keep the packet compact. Prefer obligation cards over pasting full regulatory
sources. Prefer scenario summaries over full packs unless the exact wording is
needed.

### 4. Author the draft

The author reads `agents/author.md` and `PACKET.md`, then produces `DRAFT.md`.

The author should rewrite all in-scope clauses as one coherent policy region.
The draft may touch multiple clauses, but it must not:

- change clause IDs
- change YAML frontmatter
- modify regulatory references
- modify existing scenario packs
- invent new mandatory obligations
- weaken or remove obligations

### 5. Edit the draft

The editor reads `agents/editor.md`, `PACKET.md`, `DRAFT.md`, and the current
standard text for the affected clauses. The editor writes `EDITOR_REVIEW.md`
with one of:

- **accept** - safe to verify
- **revise** - author gets one focused revision pass
- **escalate** - affected clause requires surgical mode or human decision
- **reject** - do not apply

The editor must be strict about obligation strength, retention periods, scope,
and decision parity. The editor should be pragmatic about style.

### 6. Apply or revise

If the editor says **revise**, the author gets one revision pass. Avoid open-ended
back-and-forth. If the revision still has material risk, escalate the affected
clause to surgical mode or human review.

Only apply text to `standards/*.md` after the editor has accepted the draft or
accepted a narrowed subset of it.

### 7. Verify the wave

The verifier reads `agents/verifier.md`, the applied diff, `PACKET.md`, and
`EDITOR_REVIEW.md`. It writes `VERIFY.md`, `RUN_SUMMARY.md`, and `results.tsv`.

Verification checks:

- obligations preserved or explicitly escalated
- no unsupported RFC 2119 strength changes
- scenarios produce the same decisions unless a human approved a policy change
- clause IDs and frontmatter preserved
- maps updated for changed clauses
- residual risks are explicit

### 8. Update shared records

After a kept wave:

- update the affected entries in `maps/`
- append a short entry to `blog.md`
- stop

Do not start another wave automatically.

## Keep, revise, escalate, reject

| Status | Meaning |
|--------|---------|
| keep | Apply and log the wave |
| revise | One focused author revision is allowed |
| escalate | Use surgical mode or ask the human |
| reject | Do not apply the draft |

## Surgical mode

Use surgical mode only when the change risk justifies the extra tokens. Triggers:

- retention period changes
- statutory scope changes
- MUST/SHALL/SHOULD strength changes
- partial or uncertain obligation preservation
- scenario decision mismatch
- editor cannot resolve a legal interpretation from the packet

In surgical mode, use the existing specialist files:

- `agents/scorer-obligation-preservation.md`
- `agents/scorer-ambiguity.md`
- `agents/scorer-readability.md`
- `agents/scorer-testability.md`
- `agents/scorer-operational-executability.md`
- `agents/parity-tester.md`
- `agents/coverage-assessor.md`

Apply them only to the affected clause or clause pair.

## Results log format

`results.tsv` uses this header:

```
topic	risk_tier	clauses_changed	status	scenarios_checked	obligation_issues	editor_decision	notes
```

Example:

```
personal_data_retention	medium	RKS-5.8	keep	SC-012	none	accept	added DPO annual review and lawful-basis evidence without changing storage-limitation duty
```

## Output discipline

The programme is designed to reduce token waste:

- read maps before raw sources
- build one packet per wave
- do not re-score five dimensions for every clause by default
- do not paste full agent rubrics into run artifacts
- do not repeat unchanged standard text in every artifact
- escalate only the risky fragments

The goal is faster, larger policy improvements with enough evidence for a human
to trust what changed.

## Planning artifacts

The inner loop should treat these top-level files as planning inputs:

- `policy-architecture-strategy.md` - why the queued work matters
- `structural-backlog.md` - the backlog item behind the queued wave
- `rewrite-wave-queue.md` - the executable queue

The planner normally reads only the queue plus relevant maps. It should consult
the strategy and backlog when a queued item is ambiguous or when the queue is
missing the rationale needed to build a safe packet.
