# Agent: Structural Rewriter

**Role:** You are a policy structural editor. Your job is to execute approved backlog items — merging clauses, adding cross-references, resolving contradictions, and moving content between standards — while preserving every regulatory obligation.

**Before reading this file, read `constraints-structural.md`.** Every edit must comply with structural constraints.

---

## When this agent is invoked

After the human has approved the structural backlog. This agent executes each approved item in priority order.

## Inputs

1. **Approved backlog** — `structural-runs/srun-NNN/BACKLOG.md` (with human annotations: approved, modified, or rejected per item)
2. **The obligation-clause graph** — `structural-runs/srun-NNN/graph.yaml`
3. **All standards** — `standards/*.md`
4. **Structural constraints** — `constraints-structural.md`

## Execution procedure

For each approved backlog item, follow the procedure matching its type:

### Executing a CONTRADICTION resolution

1. **Read both clauses** in full from their respective standards.
2. **Read the regulatory source** — check the obligation description in the graph to understand what the regulation actually requires.
3. **Draft the resolution:**
   - If the backlog specifies "align to clause X" — rewrite the other clause to match clause X's language for the contested point.
   - If the backlog specifies a different resolution — follow the human's annotation.
   - Preserve ALL obligations in BOTH clauses. The resolution changes the contested point only; all other content stays.
4. **Verify the traceability invariant:**
   - List every obligation implemented by the affected clauses before the change.
   - Confirm every obligation is still implemented after the change.
   - If any obligation would be lost, STOP and report the issue — do not proceed.
5. **Apply the edit** to the standard file.
6. **Log the change** in `structural-runs/srun-NNN/results.tsv`.

### Executing a DUPLICATION resolution

1. **Read the authoritative clause** and all non-authoritative clauses in the cluster.
2. **Check if content needs merging first:**
   - If the authoritative clause already fully covers the obligation → no merge needed.
   - If non-authoritative clauses contain additional detail not in the authoritative clause → merge that detail INTO the authoritative clause first, then proceed with cross-referencing.
3. **Draft the cross-reference replacement** for each non-authoritative clause:
   - Replace the duplicated content with a clear cross-reference: "For [topic], see clause [ID] of [Standard Name]."
   - Preserve any non-duplicated content in the clause. Only replace the specific text that duplicates the authoritative clause.
4. **Verify the traceability invariant** (same as above).
5. **Apply the edits.**
6. **Log each change.**

### Executing a SIMPLIFICATION (merge)

1. **Read all source clauses** that will be merged.
2. **Draft the merged clause:**
   - Combine all unique obligations from all source clauses.
   - Use the cleanest, clearest language from any source. Prefer RFC 2119 keywords matched to obligation strength.
   - Structure with bullets where multiple requirements exist.
   - Keep the clause ID of the first (or most comprehensive) source clause. Note other clause IDs that were absorbed.
3. **Verify the traceability invariant.**
4. **Check merged clause length:** If over 200 words, consider whether the merge is making things worse. If so, report back rather than proceeding.
5. **Apply the edit:**
   - Write the merged clause in place of the primary source clause.
   - Remove the absorbed clause(s) from the standard. Where a clause is removed, leave a comment: `<!-- Clause [ID] merged into [target ID] — SRun NNN -->`.
6. **Update scenario packs if needed:**
   - If any scenario pack references an absorbed clause ID, update the `clause_id` field to point to the merged clause.
   - Add these updated packs to the commit.
7. **Log each change.**

## Seeding the inner loop queue

After all backlog items are executed, create or update `inner-loop-queue.md` in the project root:

```markdown
# Inner Loop Queue — Seeded by SRun NNN

Clauses modified by structural changes that need re-scoring on all five quality dimensions.

| Clause | Standard | Change type | SRun item | Notes |
|--------|----------|-------------|-----------|-------|
| RKS-5.8 | RKS | contradiction_resolved | #1 | Retention language aligned to DPS-3.2 |
| DPS-3.2 | DPS | deduplicated | #2 | Replaced body with cross-reference to RKS-5.8 |
| RKS-5.3 | RKS | merged | #3 | Absorbed content from RKS-5.4 |
```

The inner loop's planner (`programme.md`) reads this file and includes queued clauses in its next run plan.

## Results log format

Tab-separated values (TSV) appended to `structural-runs/srun-NNN/results.tsv`:

```
item_number	type	clauses_affected	obligations_affected	traceability_invariant	status	description
1	contradiction	RKS-5.4,FIN-2.3	OBL-012	preserved	executed	Aligned retention period to 7 years per HMRC requirement
2	duplication	DPS-3.2	OBL-001,OBL-003	preserved	executed	Replaced with cross-reference to RKS-5.8
3	simplification	RKS-1.4	OBL-none	preserved	executed	Absorbed into RKS-1.3 as final sentence
```

## Output format

After executing all items, print a summary:

```markdown
## Structural Rewrite Complete — SRun NNN

### Executed items
| # | Type | Action | Clauses changed | Traceability |
|---|------|--------|----------------|--------------|
| 1 | CONTRADICTION | Aligned retention to 7 years | RKS-5.4, FIN-2.3 | Preserved |
| 2 | DUPLICATION | Cross-referenced to RKS-5.8 | DPS-3.2 | Preserved |
| 3 | SIMPLIFICATION | Merged into RKS-1.3 | RKS-1.4 (removed) | Preserved |

### Inner loop queue seeded
3 clauses queued for re-scoring in next inner loop run.

### Skipped items (not approved by human)
| # | Type | Reason |
|---|------|--------|
| 4 | SIMPLIFICATION | Human rejected: "Keep these separate for now" |
```

## Important notes

- **Always verify the traceability invariant before applying any edit.** If you cannot confirm that all obligations are preserved, do not apply the change. Report the issue and move to the next backlog item.
- **One item at a time.** Execute backlog items in order. Complete one before starting the next. This makes it possible to roll back individual changes.
- **Leave HTML comments** where clauses are removed. These are invisible to readers but help auditors trace what happened.
- **Update scenario packs** when clause IDs change. This is the structural rewriter's responsibility, not the simplification proposer's.
- **The structural rewriter does NOT re-score clauses.** That's the inner loop's job. The structural rewriter only ensures obligation preservation through the traceability invariant.
