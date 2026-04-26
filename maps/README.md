# Persistent Maps

These files are reusable context for the v4 rewrite-wave programme.

They are not a second source of truth. The standard, regulatory reference, and
scenario packs remain authoritative. The maps are compact indexes that let each
wave build a focused packet without re-reading the whole repository.

The maps support the top-level planning artifacts:

- `policy-architecture-strategy.md`
- `structural-backlog.md`
- `rewrite-wave-queue.md`

Update only the entries affected by a wave unless the structural programme asks
for a full rebuild.

## Files

| File | Purpose |
|------|---------|
| `policy-map.md` | Human-readable map of standards, sections, clause purposes, and topic clusters |
| `obligation-register.yaml` | Normalised obligation cards and scope notes |
| `clause-obligation-map.yaml` | Traceability from clauses to obligation cards |
| `scenario-index.yaml` | Scenario-to-clause and scenario-to-topic index |
| `quality-ledger.tsv` | Latest known quality/risk state used for wave selection |

## Map discipline

- Keep entries compact.
- Use stable IDs.
- Prefer `TODO` for uncertain mappings instead of inventing precision.
- Mark out-of-scope obligations explicitly.
- Do not paste large regulatory extracts here.
