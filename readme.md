# Policy Intelligence Lab

An agentic framework for autonomously improving policy documents. Claude reads markdown behaviour files that define scoring rubrics, rewriting strategies, and structural analysis procedures, then iteratively improves policy standards while preserving every regulatory obligation.

## How it works

The framework operates as two loops:

**Outer loop (structural)** analyses the policy framework across multiple standards. It builds an obligation-clause graph mapping regulatory requirements to policy clauses, then systematically identifies contradictions, duplication, and simplification opportunities. A human approves a prioritised backlog before any structural changes are executed.

**Inner loop (clause quality)** improves individual clauses within a single standard. Each pass targets one clause on one dimension (ambiguity, readability, testability, operational executability, or obligation preservation), scores before and after, runs parity tests, and keeps or discards the change based on strict criteria.

The outer loop seeds the inner loop with clauses that need re-scoring after structural changes.

## Architecture

```
programme-structural.md    Outer loop orchestrator
programme.md               Inner loop orchestrator
constraints-structural.md  Outer loop guardrails
constraints.md             Inner loop guardrails

agents/
  graph-builder.md           Builds obligation-clause graph (YAML)
  coherence-checker.md       Finds contradictions across standards
  deduplication-analyser.md  Finds overlapping clauses
  simplification-proposer.md Proposes clause merges and consolidations
  structural-rewriter.md     Executes approved structural changes
  scorer-*.md                Five quality dimension scorers
  rewriter.md                Clause-level rewriter
  parity-tester.md           Scenario-based decision parity testing
  coverage-assessor.md       Regulatory obligation coverage mapping

standards/                 Policy documents being improved
regulatory_reference/      Read-only ground truth obligations
scenario_packs/            Test scenarios (immutable, add-only)
runs/                      Inner loop run history
structural-runs/           Outer loop run history
```

## Key concepts

- **Pass** — one clause, one dimension, one improvement attempt (inner loop)
- **Run** — a batch of 5 passes, then stop for human review (inner loop)
- **Structural run** — analyse framework, produce backlog, human approves, execute (outer loop)
- **Obligation-clause graph** — maps regulatory obligations to implementing clauses across all standards
- **Traceability invariant** — no structural change may leave a regulatory obligation without an implementing clause

## Guardrails

- Obligations are never removed or weakened
- Every rewrite is scored before and after; changes that degrade any dimension beyond threshold are discarded
- Parity tests ensure rewording doesn't change policy decisions
- Structural changes require human approval of a prioritised backlog
- Regulatory references are read-only
- The system flags issues for human decision rather than inventing structure

## Scoring dimensions

| Dimension | Weight | What it measures |
|-----------|--------|------------------|
| Obligation preservation | 0.30 | All original obligations retained |
| Ambiguity reduction | 0.25 | Vague terms and undefined thresholds reduced |
| Readability improvement | 0.15 | Clearer, shorter sentences, lower grade level |
| Testability | 0.15 | Quantified thresholds, triggers, evidence requirements |
| Operational executability | 0.15 | Named roles, systems, monitoring cadence |

## Structural health dimensions

| Dimension | What it measures |
|-----------|-----------------|
| Coherence | Absence of contradictions across standards |
| Duplication index | Single-source-of-truth adherence |
| Traceability completeness | Every obligation maps to a clause |
| Framework simplicity | Clause economy vs baseline |

## Usage

Point Claude at `programme-structural.md` to run the outer loop, or `programme.md` to run the inner loop. Each contains setup instructions and the full run procedure.

Standards go in `standards/`, regulatory references in `regulatory_reference/`, and scenario packs in `scenario_packs/`.

## Status

- 1 standard loaded: Record Keeping Standard (28 clauses)
- Inner loop: 1 run complete (5/5 passes kept)
- Outer loop: ready for first structural run
