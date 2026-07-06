# Legal Research & Delivery Framework (LRDF)

A companion operating model for legal research that runs alongside a 2-week Scrum cadence, with a built-in mechanism for mapping legal requirements to product design specs.

## 1. Core idea: run legal research as a discovery track, one sprint ahead

Product teams that do continuous UX/tech discovery already solved this shape of problem with **Dual-Track Agile** (Cagan/Torres): a *discovery* track continuously explores what to build, feeding a validated, de-risked backlog into a *delivery* track that executes in fixed sprints. Legal research maps onto the discovery track almost exactly — the deliverable isn't code, it's a **de-risked, cited legal requirement** ready to attach to a story before engineering picks it up.

```
Sprint N-1 (Legal Research Track)          Sprint N (Product Delivery Track)
------------------------------------       ------------------------------------
Legal Research Planning                     Sprint Planning
  -> pick upcoming epics/features                 -> pulls in Legal Requirement
     from product roadmap 1 sprint ahead              Cards as acceptance criteria
Research & drafting                         Sprint execution
  -> statutes, case law, regs, precedent      -> engineers build against
  -> draft Legal Requirement Cards               attached legal AC
Legal Research Review                       Sprint Review / Demo
  -> sign-off, risk tier assigned              -> legal verifies implementation
     before cards enter product backlog           against signed-off AC
Legal Research Retro                        Sprint Retro
  -> what legal risk was missed/late,          -> joint retro item: legal-eng
     process fixes                               friction points
```

The one-sprint offset is the load-bearing element: it guarantees legal findings exist *before* planning, not during or after — so legal never becomes the sprint's blocker.

## 2. Ceremonies (mirrored, not duplicated)

| Scrum ceremony | Legal Research equivalent | Cadence | Purpose |
|---|---|---|---|
| Sprint Planning | Legal Research Planning | Start of Sprint N-1, 30-45 min | Triage which upcoming epics need legal research; assign owners; set risk tiers |
| Daily Standup | Async legal update (Slack/Jira comment) | Daily, async | Only escalate to live sync if a blocking finding emerges |
| Backlog Refinement | Joint Legal-Product Refinement | Mid-sprint, once per cycle | Legal presents findings so PM can write stories with legal AC already attached |
| Sprint Review/Demo | Legal Acceptance Check | End of Sprint N | Legal confirms shipped feature matches the Legal Requirement Card it was built against |
| Sprint Retrospective | Legal Research Retro (or a shared agenda item in the product retro) | End of cycle | Capture missed/late legal risks, feed into next cycle's triage |

Don't put a lawyer/researcher in every daily standup — that's the #1 way this kind of integration collapses under its own overhead. Use async updates by default, and only join synchronously when a card is flagged blocking.

## 3. The mapping mechanism: Legal Requirement Cards + a Traceability Matrix

This is the part that actually closes the "legal requirement -> product spec" gap. Borrow the **Requirements Traceability Matrix (RTM)** pattern used in regulated engineering (medical devices, aerospace, fintech) — it's designed for exactly this kind of cross-domain mapping.

### 3.1 Legal Requirement Card (atomic unit, one per finding)

Write these like user stories so engineers can consume them without translation:

```
ID: LRC-2026-014
Title: Age verification requirement for EU minors (DSA Art. 28)
Source: Digital Services Act, Art. 28(1); [citation/link]
Statement: The product MUST NOT profile minors for advertising purposes
           when the platform has actual knowledge of the user's age.
Applies to: Epic "Ad Targeting v2" (link to epic)
Risk tier: Blocking (P0) | Advisory (P1) | Monitor (P2)
Acceptance criteria:
  - AC1: Age-gate flag excludes users flagged <18 from ad-personalization pipeline
  - AC2: Audit log records exclusion decision per DSA Art. 28 recordkeeping expectation
Owner (legal): <name>
Owner (product/eng): <name>
Status: Draft -> Reviewed -> Attached to backlog -> Verified at demo -> Closed
Last reviewed: 2026-07-06
```

Treat "Risk tier" as the single field that decides how much ceremony a finding gets — P0 blocks sprint planning until addressed, P1 attaches as acceptance criteria, P2 is a watch-item logged in the risk register but doesn't gate anything.

### 3.2 Traceability Matrix (the living map)

One row per Legal Requirement Card, columns extend it forward into delivery artifacts:

| Legal Requirement (LRC-ID) | Source (statute/case/reg) | Risk Tier | Product Epic/Story | Design Spec Section | Acceptance Criteria | Test Case ID | Status | Legal Owner | Eng Owner |
|---|---|---|---|---|---|---|---|---|---|
| LRC-2026-014 | DSA Art. 28 | P0 | Ad Targeting v2 | Design Spec §4.2 Age Gate | AC1, AC2 | TC-331 | Verified | J. Fitter | A. Chen |

This single table *is* the integration layer between legal and product: every legal finding has a forward pointer into the spec/story/test that discharges it, and every regulated feature has a backward pointer to the law that shaped it. Put it where both teams already look — a Jira/Linear custom field + linked issue type is more durable than a spreadsheet, but a shared Notion/Airtable table works fine to start.

**Definition of Done, amended**: a story touching a regulated area isn't Done until its linked Legal Requirement Card status is "Verified," not just its own AC checked off. This is the enforcement hook — it costs one extra checkbox in your existing DoD.

## 4. Backlog structure

Run a **Legal Requirements Backlog (LRB)** as a sibling to the Product Backlog, not a merged one:
- Lets legal triage/prioritize independently (regulatory deadlines don't respect sprint boundaries)
- Cards graduate into the Product Backlog only once reviewed and risk-tiered — this is the "de-risking" gate from dual-track agile
- Unresolved P0 cards are a valid reason to block a story from entering sprint planning

## 5. Roles (lightweight RACI)

| Activity | Legal/You | PM | Eng | Data Science |
|---|---|---|---|---|
| Research & draft Legal Requirement Card | R/A | C | I | C (if model/data governance) |
| Risk tiering | R/A | C | I | I |
| Attach AC to story | C | R/A | C | C |
| Implementation | I | I | R/A | R/A |
| Verify at demo | R/A | C | I | I |
| Retro on misses | R | R | R | R |

## 6. Tooling notes

- If the team lives in Jira/Linear: add a `Legal Requirement` issue type, link it to epics/stories via "relates to" / "blocks," and mirror the RTM columns as custom fields. Query views become your traceability matrix for free.
- If lighter-weight: an Airtable/Notion database with the Section 3.2 schema, linked by URL to the PM tool's epics.
- For rules that are genuinely mechanical (e.g., "must not target users under 18," "must retain records for N days"), consider encoding them as automated checks in CI (policy-as-code, e.g. OPA/Rego, or just a linter rule) rather than relying on manual verification at demo — this is the same "shift-left" move security teams made with SAST gates.

## 7. Why the offset (not a merged single track)

Merging legal into the same sprint as delivery creates two failure modes: (1) legal becomes a same-sprint blocker when research takes longer than a story estimate, or (2) legal reviews happen after code is written, forcing rework. The one-sprint lead absorbs research variance without slowing delivery, and it's the same reason UX/tech discovery tracks run ahead rather than inline.
