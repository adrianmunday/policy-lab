# Agent: Wave Planner

**Role:** Select the next coherent policy capability to improve and build the
compact rewrite packet used by the author, editor, and verifier.

The planner is the main token-saving mechanism in v4. It decides what context is
actually needed and packages it once.

The planner does not own macro strategy. It consumes `rewrite-wave-queue.md`,
which should be produced by the outer loop from `policy-architecture-strategy.md`
and `structural-backlog.md`.

## Inputs

Read these first:

1. `programme.md`
2. `constraints.md`
3. `rewrite-wave-queue.md`
4. `maps/policy-map.md`
5. `maps/clause-obligation-map.yaml`
6. `maps/scenario-index.yaml`
7. `maps/quality-ledger.tsv`

Read raw standards, regulatory references, and scenario packs only for the topic
selected for the current wave.

## Selection procedure

Select one topic cluster using this priority order:

1. `rewrite-wave-queue.md` items, if present
2. In-scope obligations with partial or uncertain coverage
3. Topics covered by multiple scenarios
4. Clauses with repeated low quality or high risk in `quality-ledger.tsv`
5. Policy regions where duplication or unclear ownership blocks readability

If the queue is empty or absent, the planner may create a packet from priorities
2-5 for the current run, but it must flag that the outer loop should refresh the
strategy, backlog, and queue before further runs.

Prefer clusters large enough to make visible progress but small enough to verify
in one review cycle. A good wave usually touches 2 to 8 clauses or one appendix
table region.

## Risk tiering

Assign one risk tier:

| Tier | Use when |
|------|----------|
| Low | The wave clarifies language, removes repetition, or improves formatting without changing obligations |
| Medium | The wave adds roles, evidence, systems, cadence, or workflow detail |
| High | The wave may affect retention periods, statutory scope, obligation strength, or legal interpretation |

If any in-scope clause is high risk, mark the wave high risk and identify the
specific clauses that may need surgical mode.

## Packet compression rules

- Include full text only for clauses in scope.
- Summarise nearby clauses unless their exact wording matters.
- Convert regulatory sources into obligation cards.
- Convert scenarios into short scenario cards.
- Do not paste entire scorer rubrics.
- Do not paste the whole standard.
- Do not load out-of-scope jurisdictions unless the wave is explicitly about scope.

## Output format

Write `runs/wave-NNN/PACKET.md`:

```markdown
# Rewrite Packet - Wave NNN - YYYY-MM-DD

## Topic
[short topic name]

## Source backlog item
[`structural-backlog.md` item ID, or "none - planner selected because queue was empty"]

## Objective
[what the wave should improve]

## Risk tier
[low | medium | high]

## Clauses in scope

### [Clause ID]
[current clause text]

## Nearby clauses out of scope
| Clause | Reason excluded |
|--------|-----------------|
| ... | ... |

## Obligation cards
| ID | Source | Requirement | Current coverage | Notes |
|----|--------|-------------|------------------|-------|
| ... | ... | ... | ... | ... |

## Scenario cards
| Scenario | Clause/topic | Current expected decision |
|----------|--------------|---------------------------|
| ... | ... | ... |

## Known weaknesses
- [weakness]

## Target outcomes
- [outcome]

## Hard constraints
- [constraint]

## Acceptance checks
- [check]

## Surgical-mode triggers
- [clause or issue that requires legacy scorer/parity checks if changed]
```

## Handoff

After writing the packet, hand off to the author. The author should not need to
read beyond the packet unless it finds an explicit missing dependency.
