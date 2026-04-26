# Structural Backlog

Last updated: 2026-04-26

This file turns the policy architecture strategy into ranked structural work.
Backlog items are still planning items; they become executable only when they are
translated into `rewrite-wave-queue.md` or an approved structural edit.

## Strategy link

Current strategy: keep RKS as the authoritative retention and disposal standard,
use rewrite waves for high-value topic improvements, and reserve full
consolidation decisions for a future multi-standard estate.

See `policy-architecture-strategy.md`.

## Backlog status meanings

| Status | Meaning |
|--------|---------|
| queued_for_wave | Converted into `rewrite-wave-queue.md` |
| needs_strategy_decision | Requires human architectural decision first |
| ready_for_backlog_approval | Structural edit candidate needing human approval |
| parked | Known issue, not worth action yet |

## Backlog

### 1. retention_tiers_and_triggers - Improve decision-rule clarity

- **Type:** capability_rewrite
- **Priority:** high
- **Status:** queued_for_wave
- **Strategy rationale:** RKS should remain authoritative for retention tiers,
  triggers, expiry actions, and exceptions.
- **Likely clauses:** RKS-3.1, RKS-3.2, RKS-3.3, RKS-3.5, RKS-5.7, RKS-A.2
- **Obligations:** OBL-FOIA-HOLD, OBL-PUBLIC-RECORDS-TRANSFER
- **Expected wave:** `retention_tiers_and_triggers`

### 2. official_record_location - Align SharePoint, email, and personal-drive rules

- **Type:** capability_rewrite
- **Priority:** high
- **Status:** queued_for_wave
- **Strategy rationale:** Readers need one coherent workflow for where official
  records live.
- **Likely clauses:** RKS-4.2, RKS-4.3, RKS-5.7
- **Obligations:** none currently mapped; policy control topic
- **Expected wave:** `official_record_location`

### 3. personal_data_retention - Confirm authority model and improve wording

- **Type:** authority_model_and_rewrite
- **Priority:** high
- **Status:** queued_for_wave
- **Strategy rationale:** RKS-5.8 should be the operative home for
  storage-limitation retention language unless a future privacy standard becomes
  the authoritative home.
- **Likely clauses:** RKS-1.2, RKS-1.3, RKS-5.8, RKS-A.7
- **Obligations:** OBL-UKGDPR-STORAGE-LIMITATION
- **Expected wave:** `personal_data_retention`
- **Human decision needed if:** another standard also contains personal-data
  retention wording and consolidation or cross-reference replacement is proposed.

### 4. statutory_function_records - Protect and clarify statutory retention logic

- **Type:** high_risk_capability_rewrite
- **Priority:** medium
- **Status:** queued_for_wave
- **Strategy rationale:** RKS should remain authoritative for SSRO statutory
  function records, but the DRA/SSCR formula is complex and needs careful
  readability work.
- **Likely clauses:** RKS-5.1, RKS-5.2, RKS-A.3
- **Obligations:** OBL-DRA-QDC-QSC-REGISTER, OBL-SSCR-CONTRACTOR-RECORDS,
  OBL-PUBLIC-RECORDS-TRANSFER
- **Expected wave:** `statutory_function_records`

### 5. hr_and_finance_records - Align narrative and schedule rows

- **Type:** capability_rewrite
- **Priority:** medium
- **Status:** queued_for_wave
- **Strategy rationale:** RKS Appendix A is the detailed schedule; narrative
  clauses should introduce responsibilities and source basis without duplicating
  table detail inconsistently.
- **Likely clauses:** RKS-5.3, RKS-5.4, RKS-A.7, RKS-A.8
- **Obligations:** OBL-HMRC-FINANCE-RECORDS, OBL-CIPD-HR-GUIDANCE
- **Expected wave:** `hr_and_finance_records`

## Example multi-standard backlog item

Use this pattern when a second standard is added.

### X. personal_data_authority_model - Choose authoritative home

- **Type:** architecture_decision
- **Priority:** high
- **Status:** needs_strategy_decision
- **Issue:** RKS-5.8 and IGS-3.2 both state storage-limitation retention rules.
- **Options:**
  - Keep separate but align wording.
  - Make RKS authoritative and replace IGS detail with a cross-reference.
  - Make IGS authoritative and move RKS detail into a cross-reference.
- **Recommended strategy:** Make RKS authoritative for retention periods and IGS
  authoritative for privacy governance responsibilities.
- **Human decision needed:** approve the authority model before any rewrite wave.
- **Likely waves after approval:**
  - `personal_data_authority_model`
  - `personal_data_reader_flow`
