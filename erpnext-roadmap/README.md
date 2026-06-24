# ERPNext Phase Roadmap + Dependency Map — AMDG / ABES

A phase roadmap and dependency map for the AMDG/ABES ERPNext program. It shows the
phases and sub-phases in execution order, the status of each, and the hard
dependencies (what blocks what), so an operator can see the critical path and the
open gating decisions at a glance.

Audience: **Mike** (engagement lead) and **Bryan** (founder).

## Deliverables

| File | What it is |
| --- | --- |
| `ERPNext_Phase_Roadmap.mmd` | Editable Mermaid `flowchart TB` source (statuses via `classDef`). Edit nodes/edges here. |
| `ERPNext_Phase_Roadmap.html` | Standalone viewer — open in any browser. Renders the Mermaid via CDN, with the legend, conflict callouts, and a **Download SVG** button. |
| `ERPNext_Phase_Roadmap.svg` | Static pre-rendered SVG (no internet needed). |

## Status legend (colors)

| Status | Fill |
| --- | --- |
| Not Started | `#F2F2F2` |
| In Progress | `#BDD7EE` |
| Blocked | `#F4B7B7` |
| On Hold | `#FCE4A6` |
| Deferred | `#D9D9D9` |

Diamonds = open **gating decisions**. Red arrows = **Blocked-by** dependencies.
Blue dashed = soft tie / informs.

## Dependency edges (Blocked By → blocked item)

- Hosting provider + BAA → **1.10 Deploy** and all **Phase 3** PHI/clinical work
- Infra blueprint (due ~6/25) → **informs** the hosting/BAA decision
- UAT approach decision (parallel vs cutover) → **1.8 BDD/Test Plan** and **1.9 E2E & UAT**
- Data-consultant onboarding → **1.11 Validate Migrated Data**
- Plaid feeds live (2.2) → **AP Reconciliation**
- AP parse + recon (2.3) → **2.5 AP BDD Tests**
- Document-management PRD review → **2.6–2.9 (Paperless / two-way sync)**
- Mail provider selection (cloud) → **2.10 Mailbox Ingestion**
- Ownership confirmation with Charlie → **2.11–2.13 (resume parsing / AI talent)**
- Financial-foundation priority + clinical-replace decision → all of **Phase 3**, downstream **Phase 4**
- Phase 1 financial cutover → **Phase 2 build** (2.3 AP Parse runs in parallel now)
- Phase 4 ↔ ERP: **AR reconciliation** is the only integration tie

**Critical path:** Infrastructure (Hosting + BAA) and Phase 1 Financial Foundation
gate almost everything downstream.

## Source of truth & conflicts flagged

Built from the `ERPNext_Work_Plan` workbook's **"Detailed Tasks"** sheet plus the
**v2 "Change Log"** (the 18 decisions from the June 23 Nexera/Mike call). Node
**labels** follow the actual sheet text; **statuses** follow the Change Log,
because the sheet has no status columns. Divergences (the sheet "wins" on labels;
the call record "wins" on status — and every gap is flagged rather than guessed):

1. **No `Status`/`Blocked`/`Blocked By` columns exist.** The brief assumes a v3
   schema with those columns; the real sheet has none. The only literal Status
   column (Phase Overview) reads **"Not Started" for all four phases** and was not
   updated after the call.
2. **1.7** in the sheet is "Document Migration Mappings Observations + Findings"
   (Russ handoff), not "Idempotency & Error Handling".
3. **1.10** still reads "Deploy to **Manus** Environment" though Change Log #4
   decided to drop Manus and deploy to a cloud. Shown as "Manus → Cloud".
4. **1.11 "Validate Migrated Data"** is not a sheet row; it is implied by Change
   Log #14 (data-consultant ownership). Marked with `*`.
5. **Phase 2A numbering:** the sheet has **two rows numbered 2.3** ("AP Parse +
   Extraction" and "AP Recon") and **no 2.4** (the old "Automatic Journal Entry"
   task was dropped/merged). Brief treats parse = 2.3, recon = 2.4.
6. **5.7 (Hosting + BAA)** and **5.8 (Infra blueprint)** are not numbered rows.
   Cross-Cutting in the sheet is only 5.1–5.6 (Security & HIPAA) plus one
   unnumbered "Infrastructure — Sample Azure/IaaS Environment" row. Phases 5 & 6
   appear only in the Phase Overview with no detailed tasks.
7. **2.10** appears twice (duplicate) in the sheet; collapsed to one node.
8. **2.1** is typo'd "Design *Plain* Bank Feed Architecture" (should be Plaid);
   corrected in the label.

## Editing

Change a node's status by swapping its `:::class` suffix in the `.mmd` (or in the
`<pre class="mermaid">` block of the HTML). Add a dependency by adding an edge
(`A ==> B` for blocking) and extending the `linkStyle` index list so it renders red.

## Re-rendering the SVG

```
echo '{"args":["--no-sandbox"]}' > /tmp/pptr.json
npx -y @mermaid-js/mermaid-cli -p /tmp/pptr.json -i ERPNext_Phase_Roadmap.mmd -o ERPNext_Phase_Roadmap.svg
```
