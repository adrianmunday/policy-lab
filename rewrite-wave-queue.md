# Rewrite Wave Queue

Last updated: 2026-04-26

This queue is produced by the outer loop and consumed by the inner rewrite-wave
programme in `programme.md`.

The queue is deliberately neutral: it is not "outer" or "inner" owned. The outer
loop decides what should be done next; the inner loop turns one queued item into
a packet, draft, editor review, and verification record.

## 1. retention_tiers_and_triggers - Make retention decisions easier to apply

- **Source backlog item:** `structural-backlog.md` item 1
- **Objective:** Clarify the relationship between retention tiers, trigger
  points, expiry actions, default retention, and FOI/litigation holds.
- **Risk tier:** medium
- **Likely clauses:** RKS-3.1, RKS-3.2, RKS-3.3, RKS-3.5, RKS-5.7, RKS-A.2
- **Obligations:** OBL-FOIA-HOLD, OBL-PUBLIC-RECORDS-TRANSFER
- **Scenarios:** SC-001, SC-002, SC-003
- **Why now:** This topic affects several scenarios and is central to user
  decision-making across the policy.
- **Surgical triggers:** Any change to FOI hold override, permanent preservation,
  National Archives transfer, or the one-year default retention rule.
- **Status:** queued

## 2. official_record_location - Align email, SharePoint, and personal-drive rules

- **Source backlog item:** `structural-backlog.md` item 2
- **Objective:** Make official record location rules operational and consistent,
  including Rights Management workarounds and unsaved email treatment.
- **Risk tier:** medium
- **Likely clauses:** RKS-4.2, RKS-4.3, RKS-5.7
- **Obligations:** none currently mapped; policy control topic
- **Scenarios:** SC-011, SC-014, SC-001
- **Why now:** The topic is practical, cross-clause, and likely to benefit from
  workflow-level rewriting.
- **Surgical triggers:** Any change to default email retention or official
  record-keeping system scope.
- **Status:** queued

## 3. personal_data_retention - Tighten lawful-basis and review language

- **Source backlog item:** `structural-backlog.md` item 3
- **Objective:** Improve clarity around storage limitation, lawful basis for
  extended retention, DPO review, and sensitive personal data schedule rows.
- **Risk tier:** high
- **Likely clauses:** RKS-1.2, RKS-1.3, RKS-5.8, RKS-A.7
- **Obligations:** OBL-UKGDPR-STORAGE-LIMITATION
- **Scenarios:** SC-008, SC-012
- **Why now:** This is a high-value area, but it needs stronger assurance because
  legal basis and personal-data retention language are sensitive.
- **Surgical triggers:** Any change to MUST NOT storage-limitation language,
  lawful-basis conditions, or deletion outcome.
- **Status:** queued

## 4. statutory_function_records - Preserve complex statutory retention logic

- **Source backlog item:** `structural-backlog.md` item 4
- **Objective:** Improve readability and testability of statutory-function
  retention without simplifying away the DRA/SSCR logic.
- **Risk tier:** high
- **Likely clauses:** RKS-5.1, RKS-5.2, RKS-A.3
- **Obligations:** OBL-DRA-QDC-QSC-REGISTER, OBL-SSCR-CONTRACTOR-RECORDS,
  OBL-PUBLIC-RECORDS-TRANSFER
- **Scenarios:** SC-004, SC-005, SC-006
- **Why now:** The current text is important and complex; a packeted wave can
  improve comprehensibility while surgical triggers protect the legal formula.
- **Surgical triggers:** Any change to QDC/QSC register duty, contractor retention
  formula, referral evidence period, or permanent referral-decision retention.
- **Status:** queued

## 5. hr_and_finance_records - Align narrative clauses with schedule rows

- **Source backlog item:** `structural-backlog.md` item 5
- **Objective:** Improve consistency between HR/finance narrative clauses and
  Appendix A rows, especially owner, evidence, trigger, and review language.
- **Risk tier:** medium
- **Likely clauses:** RKS-5.3, RKS-5.4, RKS-A.7, RKS-A.8
- **Obligations:** OBL-HMRC-FINANCE-RECORDS, OBL-CIPD-HR-GUIDANCE
- **Scenarios:** SC-007, SC-008, SC-009, SC-010
- **Why now:** Several scenarios depend on this area and the tables are good
  candidates for coherent editorial improvement.
- **Surgical triggers:** Any change to seven-year finance retention, six-month
  sensitive personal data retention, pension age-100 retention, or permanent
  safety-record retention.
- **Status:** queued
