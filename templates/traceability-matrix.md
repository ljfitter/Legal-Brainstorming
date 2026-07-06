# Traceability Matrix (reference copy)

Working copy should live wherever your team already tracks work (Jira/Linear custom fields, or the `traceability-matrix.csv` in this directory imported into Airtable/Sheets/Excel). This file is a quick-reference version of the same schema — see `frameworks/legal-research-delivery-framework.md` section 3.2 for how each column is used.

| LRC ID | Source (statute/case/reg) | Risk Tier | Product Epic/Story | Design Spec Section | Acceptance Criteria | Test Case ID | Status | Legal Owner | Eng Owner | Last Reviewed |
|---|---|---|---|---|---|---|---|---|---|---|
| LRC-2026-014 | DSA Art. 28(1) | P0 | Ad Targeting v2 | Design Spec §4.2 Age Gate | AC1, AC2 | TC-331 | Verified | | | 2026-07-06 |

## Column notes

- **LRC ID**: matches the Legal Requirement Card issue number/title (`[LRC] ...`).
- **Risk Tier**: P0 Blocking / P1 Advisory / P2 Monitor — see the framework doc for what each tier gates.
- **Status**: Draft -> Reviewed -> Attached to backlog -> Verified at demo -> Closed. A story isn't Done until its linked row hits Verified.
