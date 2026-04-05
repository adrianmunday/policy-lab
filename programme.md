# Policy Intelligence Lab v2 — Programme

This is an agentic framework for autonomously improving policy documents. Instead of a deterministic Python evaluation engine, each assessment dimension is handled by a specialist agent behaviour defined in markdown. You — Claude — are the orchestrator.

## How this works

You read this file first. It tells you the overall flow. When you need to score, rewrite, test parity, or assess coverage, you read the relevant agent behaviour file from `agents/` and follow its instructions. The agent files contain the rubrics, worked examples, and output formats that replace the old `evaluate.py`.

## File map

```
policylab-v2/
├── programme.md                  ← You are here. The orchestrator.
├── constraints.md                ← Immutable guardrails. Read this before every run.
├── standards/                    ← Policy documents you improve.
├── regulatory_reference/         ← Ground truth obligations. Read-only.
├── scenario_packs/               ← Test scenarios for parity testing. Add-only.
├── agents/                       ← Agent behaviour files (replace evaluate.py):
│   ├── scorer-obligation-preservation.md
│   ├── scorer-ambiguity.md
│   ├── scorer-readability.md
│   ├── scorer-testability.md
│   ├── scorer-operational-executability.md
│   ├── parity-tester.md
│   ├── coverage-assessor.md
│   └── rewriter.md
├── runs/                         ← One subfolder per run (audit trail)
│   └── run-NNN/
│       ├── PLAN.md
│       ├── RUN_SUMMARY.md
│       └── results.tsv
└── blog.md                       ← Chronological progress narrative
```

## Setup (first time only)

1. **Read `constraints.md`** — understand what you can and cannot do.
2. **Read the standard** — `standards/<standard>.md`. Understand every clause.
3. **Read the regulatory reference** — `regulatory_reference/<standard>/`. Understand the ground truth obligations. For `.xlsx` files, use the `Read` tool or parse with a bash script to extract the data.
4. **Read all scenario packs** — `scenario_packs/<standard>/*.yaml`. Understand the test scenarios and which clauses they cover.
5. **Run initial coverage assessment** — follow `agents/coverage-assessor.md` to map obligations to clauses and compute the baseline coverage score.
6. **Initialise run tracking** — create `runs/run-001/` and `blog.md` if they don't exist.
7. **Confirm with the user** — report the baseline coverage score and any notable findings. Await go-ahead.

## Key concepts

### Pass (atomic unit of work)
One clause + one dimension + one improvement attempt. The smallest reviewable unit.

### Run (batch of 5 passes)
Five passes executed in sequence, then stop. The user reviews before the next run.

### Plan
Which 5 (clause, dimension) pairs to target in this run. Generated autonomously based on coverage gaps and weakest scores.

## The plan

At the start of each run, select 5 (clause, dimension) pairs. Log the plan to `runs/run-NNN/PLAN.md`.

### Selection logic (priority order)

1. **Coverage gaps** — clauses nearest to unaddressed or partially addressed in-scope regulatory obligations. Pair with whichever dimension is weakest for that clause.
2. **Weakest scores** — clauses with the lowest composite score, paired with their weakest dimension.
3. **Lessons from previous runs** — avoid repeating failed (clause, dimension) combinations from prior runs unless the approach is materially different. Check `runs/run-*/results.tsv` for discard history.

### Plan format

```markdown
# Run Plan — Run NNN — YYYY-MM-DD

## Coverage baseline
coverage_score: 0.XXX
in_scope_gaps: N (X not_addressed, Y partially_addressed)

## Planned passes

| Pass | Clause  | Dimension              | Rationale |
|------|---------|------------------------|-----------|
| 1    | RKS-X.X | [dimension]            | [why]     |
| 2    | RKS-X.X | [dimension]            | [why]     |
| 3    | RKS-X.X | [dimension]            | [why]     |
| 4    | RKS-X.X | [dimension]            | [why]     |
| 5    | RKS-X.X | [dimension]            | [why]     |

## Flagged (no matching clause — human decision needed)
- [citation]: [description] — recommend creating clause RKS-X.X
```

## The pass (atomic workflow)

Each pass follows this exact sequence:

### Step 1: Score baseline

Read the agent behaviour files for ALL FIVE scorers. Score the current clause text across all five dimensions. For comparative scorers (ambiguity, readability), use 0.5 as the baseline since there's no "before" version yet.

Record all five scores plus the composite (weighted sum — see `constraints.md` for weights).

### Step 2: Draft improvement

Read `agents/rewriter.md`. Provide it with:
- The clause ID and current text
- The target dimension
- The baseline scores
- The rationale from the plan

The rewriter produces a proposed rewrite with a change log and obligation preservation checklist.

### Step 3: Score the rewrite

Run ALL FIVE scorers on the rewritten clause, comparing against the original:

1. **Obligation preservation** (`agents/scorer-obligation-preservation.md`): Compare old vs new → score must be >= 1.0
2. **Ambiguity** (`agents/scorer-ambiguity.md`): Count issues in old and new → compute reduction score
3. **Readability** (`agents/scorer-readability.md`): Compute FK grade and Flesch ease for old and new → compute improvement score
4. **Testability** (`agents/scorer-testability.md`): Score new clause on 6 checks → score as fraction
5. **Operational executability** (`agents/scorer-operational-executability.md`): Score new clause on 5 checks → score as fraction

Compute the new composite score (weighted sum).

### Step 4: Run parity tests

Read `agents/parity-tester.md`. Find all scenario packs relevant to this clause. Run each scenario against both old and new clause text. All scenarios must produce the same outcome.

### Step 5: Keep or discard

Apply the keep/discard criteria from `constraints.md`:

**KEEP** if ALL of the following:
- obligation_preservation >= 1.0
- The targeted dimension improved
- No other dimension degraded materially (> 0.05 drop)
- All parity tests pass

**DISCARD** if ANY criterion fails.

If KEPT:
- Apply the rewrite to the standard file
- Record in results.tsv as "keep"

If DISCARDED:
- Do not modify the standard file
- Record in results.tsv as "discard"
- Note what went wrong for future planning

### Step 6: Log

Append the result to `runs/run-NNN/results.tsv`.

## Results log format

Tab-separated values (TSV). Header row:

```
clause_id	target_dimension	baseline_composite	new_composite	obligation_preserved	parity_pass	status	description
```

Example rows:

```
RKS-5.4	operational_executability	0.625	0.777	true	true	keep	added Head of Finance role and quarterly reconciliation
RKS-2.3	testability	0.500	0.503	true	true	discard	readability degraded from 0.5 to 0.165
—	—	0.185	—	—	—	coverage	end-of-run coverage assessment
```

## The run (batch of 5 passes)

Execute in this order:

1. **Generate plan** — select 5 passes, log to `runs/run-NNN/PLAN.md`
2. **Execute passes 1-5** — follow the atomic pass workflow for each
3. **Re-assess coverage** — follow `agents/coverage-assessor.md` to compute updated coverage
4. **Write run summary** to `runs/run-NNN/RUN_SUMMARY.md`
5. **Update blog.md** — append a 2-4 paragraph narrative entry
6. **STOP** — do not start another run automatically

### Run summary format

```markdown
# Run Summary — Run NNN — YYYY-MM-DD

## Results

| Pass | Clause  | Dimension | Baseline | After | Status | Notes |
|------|---------|-----------|----------|-------|--------|-------|
| 1    | RKS-X.X | ...       | 0.XXX    | 0.XXX | keep   | ...   |
| ...  | ...     | ...       | ...      | ...   | ...    | ...   |

**Kept: X/5 passes**

## Coverage
- Before: X.XXX → After: Y.YYY
- Fully addressed: A → B
- Partially addressed: C → D
- Not addressed: E → F

## Flagged issues
- [any obligations needing new clauses]
- [any systematic problems observed]

## Lessons learned
- [what worked, what didn't, what to try differently next run]

## Recommended next-run targets
1. [clause + dimension + rationale]
2. [clause + dimension + rationale]
3. [clause + dimension + rationale]
4. [clause + dimension + rationale]
5. [clause + dimension + rationale]
```

### Blog entry format

Append to `blog.md`:

```markdown
---

## Run NNN — YYYY-MM-DD

[2-4 paragraph narrative: what was the plan, what happened, what improved,
what was discarded, any surprises or flags. Include the coverage score
before and after. Keep it honest — if nothing improved, say so.]

**Passes:** X/5 kept | **Coverage:** X.XXX → Y.YYY
```

## Directives

**ONE RUN, THEN STOP.** Execute 5 passes, write the summary, then stop. The human reviews and kicks off the next run.

**ONE DIMENSION PER PASS.** Each pass targets exactly one dimension on exactly one clause. No multi-targeting.

**SIMPLICITY.** Prefer clear, simple clause language over complex legalese. Adding ugly complexity for a marginal score gain is not worth it.

**FLAG, DON'T INVENT.** If a regulatory obligation has no corresponding clause, flag it in the run summary. Do not create new clauses or sections.

**SCORE BEFORE REWRITE.** Always establish a baseline before attempting improvement.

**READ THE AGENT FILES.** When scoring, rewriting, or testing, read the relevant agent behaviour file and follow its procedure exactly. The rubrics and output formats are there for consistency and auditability.

**LEARN FROM HISTORY.** Before planning, read previous run results to avoid repeating failed approaches. If a (clause, dimension) pair was discarded, understand why before retrying it.

## Improvement guidance (for the rewriter)

When rewriting clauses, aim for these qualities in priority order:

1. **Preserve all obligations** — never lose a mandatory requirement
2. **Use RFC 2119 language** — MUST, SHALL, SHOULD, MAY matched to actual obligation strength
3. **Quantify thresholds** — replace vague terms with specific values where justified
4. **Clarify evidence requirements** — make compliance provable
5. **Map to controls** — link clauses to roles, systems, and processes
6. **Improve structure** — bullets for multiple requirements, shorter sentences
7. **Keep it simple** — fewer words for the same meaning is always better
