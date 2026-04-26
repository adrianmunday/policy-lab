# Policy Map

Last initialised: 2026-04-25

## Standards loaded

Population boundary: Current RKS pilot corpus. This is not an enterprise-wide or
whole-bank policy inventory.

| Standard | File | Status | Notes |
|----------|------|--------|-------|
| RKS - Information Management: Retention and Disposal Policy | `standards/record-keeping-standard.md` | draft | SSRO-specific retention and disposal policy |

## RKS sections

| Section | Clauses | Capability |
|---------|---------|------------|
| RKS-1 Purpose | RKS-1.1 to RKS-1.4 | States policy purpose, legal basis, accountable owners, and personal data cross-reference |
| RKS-2 What is a record? | RKS-2.1 to RKS-2.3 | Defines records and quality characteristics |
| RKS-3 Retention periods | RKS-3.1 to RKS-3.6 | Defines retention periods, tiers, trigger points, expiry actions, exceptions, and final/draft handling |
| RKS-4 Scope and application | RKS-4.1 to RKS-4.3 | Defines covered workers and official storage expectations for email, SharePoint, and personal drives |
| RKS-5 Legal requirements | RKS-5.1 to RKS-5.8 | Covers statutory records, HR and finance records, default retention, and personal data retention |
| RKS-A Appendix A | RKS-A.1 to RKS-A.9 | Retention and disposal schedule by record type |

## Topic clusters

| Topic | Primary clauses | Related scenarios | Notes |
|-------|-----------------|-------------------|-------|
| retention_tiers_and_triggers | RKS-3.1, RKS-3.2, RKS-3.3, RKS-3.5, RKS-5.7, RKS-A.2 | SC-001, SC-002, SC-003 | High leverage for clarity and decision rules |
| statutory_function_records | RKS-5.1, RKS-5.2, RKS-A.3 | SC-004, SC-005, SC-006 | High risk where retention periods or statutory scope change |
| hr_and_finance_records | RKS-5.3, RKS-5.4, RKS-A.7, RKS-A.8 | SC-007, SC-008, SC-009, SC-010 | Good candidate for operational executability and table consistency |
| official_record_location | RKS-4.2, RKS-4.3, RKS-5.7 | SC-011, SC-014 | Medium risk; likely benefits from workflow clarification |
| personal_data_retention | RKS-1.2, RKS-1.3, RKS-5.8, RKS-A.7 | SC-008, SC-012 | High risk if changing legal basis or storage-limitation wording |
| draft_and_evidence_records | RKS-2.1, RKS-3.6, RKS-A.4 | SC-013 | Good candidate for clarification of evidence-of-decision-making test |

## Known architecture notes

- The repo currently contains one standard, so cross-standard structural analysis
  has limited value until more standards are added.
- The regulatory workbook is banking-oriented and multi-jurisdictional. The RKS
  standard is SSRO-specific, so out-of-scope filtering is essential.
- Legacy inner-loop scorer files remain useful for surgical checks on high-risk
  clauses but should not be the default workflow.
