# Policy Architecture Strategy

Last updated: 2026-04-26

This file is the top-level strategy for the policy estate. It sits above the
structural backlog and the rewrite-wave queue.

The strategy answers:

- What should the policy estate become?
- Which standards are authoritative for which topics?
- Should overlapping standards be kept separate, consolidated, or converted into
  a hub-and-spoke model?
- Which strategic decisions need human approval before drafting starts?

## Current estate

### Population boundary

| Field | Value |
|-------|-------|
| Population name | Current RKS pilot corpus |
| Included standards | Record Keeping Standard |
| Explicitly excluded standards/domains | Other bank or SSRO policy standards not loaded into this repo |
| Owner/domain | Information management / record keeping |
| Rationale for inclusion | Current pilot standard for validating rewrite-wave architecture |
| Known missing standards | Privacy, data governance, data quality, technology, and other standards may exist but are not in the current corpus |
| Completeness confidence | Low for enterprise coverage; sufficient for single-standard pilot |

| Standard | Role | Notes |
|----------|------|-------|
| RKS - Information Management: Retention and Disposal Policy | Current authoritative standard for SSRO retention and disposal rules | Single standard currently loaded |

## Current strategic posture

**Recommended posture:** keep the current single-standard architecture, but
prepare the maps and backlog so future standards can be assessed against an
authority model.

Rationale:

- The corpus currently has one standard, so full consolidation decisions are
  premature.
- The Record Keeping Standard is already the natural authoritative home for
  retention periods, trigger points, disposal actions, and record-location rules.
- The most valuable near-term work is to improve coherent policy capabilities
  through rewrite waves while preserving traceability.

## Authority model

| Topic | Current authoritative home | Strategy |
|-------|----------------------------|----------|
| Retention tiers, triggers, and expiry actions | RKS | Keep in RKS; improve decision-rule clarity |
| Statutory-function records | RKS | Keep in RKS; protect DRA/SSCR retention logic |
| Personal data retention | RKS | Keep operative retention rule in RKS-5.8; avoid duplicating storage-limitation wording elsewhere |
| HR and finance retention schedules | RKS Appendix A | Keep schedules in RKS; align narrative clauses with table rows |
| Official record location | RKS | Keep in RKS; clarify SharePoint, email, and personal-drive workflow |

## Strategy options for a multi-standard estate

When a second standard is added, the outer loop should compare the estate using
these options before queueing rewrite waves.

### Option A - Keep Separate

Use this when standards have distinct audiences, domains, or operational owners.

- Define clear boundaries.
- Keep one authoritative home for each obligation.
- Replace duplicate text with cross-references.
- Use rewrite waves to clarify reader journeys inside each standard.

### Option B - Consolidate

Use this when two standards materially serve the same purpose or duplicate the
same obligations throughout.

- Merge overlapping clauses into one standard.
- Preserve all obligations and scenarios.
- Retire duplicated sections only after human approval.
- Queue high-risk retained clauses for surgical verification.

### Option C - Hub And Spoke

Use this when several standards share core rules but need specialist procedures.

- Create or designate a core standard as the authoritative hub.
- Move shared definitions, retention rules, and role/accountability language into
  the hub.
- Convert specialist standards into spokes that cross-reference the hub and keep
  only domain-specific requirements.

## Example strategy decision

If the estate contains both:

- Record Keeping Standard
- Information Governance Standard

and both contain personal-data retention clauses, the outer loop should not
immediately rewrite either clause. It should first decide the architecture:

```markdown
Recommendation: Hub and spoke

RKS remains the authoritative home for retention periods and disposal actions.
The Information Governance Standard keeps privacy governance responsibilities
but cross-references RKS-5.8 for storage-limitation retention rules.

Human decision needed:
- approve RKS as authoritative home for personal-data retention wording
- approve replacement of duplicate IGS retention text with a cross-reference
```

That decision then becomes backlog items and rewrite waves.

## Human decision gates

Human approval is required before:

- consolidating two standards into one
- creating or retiring a standard
- moving an authoritative obligation from one standard to another
- changing obligation strength
- changing retention periods or statutory scope
- replacing substantive clause text with a cross-reference

## Next strategic actions

| Priority | Action | Output |
|----------|--------|--------|
| 1 | Improve current single-standard policy capabilities | `rewrite-wave-queue.md` |
| 2 | Keep maps current after each wave | `maps/` |
| 3 | Re-run strategy mode when another standard is added | Updated strategy and backlog |
