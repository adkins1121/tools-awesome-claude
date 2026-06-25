# AMDG / ABES — Functional Architecture (v0.1 starting draft)

A **functional** architecture map — capabilities as boxes, color-coded by readiness —
meant as a single source of truth to **set vision and align the team**, and a starting
point to build from (Mike's "fifth-grader crayon" map: get the boxes on a page, then add detail).

**Source:** Fathom call **"Hannah x Mike // Connect on Architecture"**, 2026-06-24
(Mike Adkins, Hannah Schuele, Cissy Byrd) — https://fathom.video/calls/723282376.
The diagram is exactly the next-step Mike committed to on that call.

## Files
| File | What it is |
| --- | --- |
| `Functional_Architecture.mmd` | Editable Mermaid source (heat-map via `classDef`) |
| `Functional_Architecture.html` | Standalone browser viewer — legend, principles, decisions, owners, Download-SVG |
| `Functional_Architecture.svg` | Static pre-rendered diagram |
| `Functional_Architecture.pdf` | Diagram-only PDF |
| `Functional_Architecture_Brief.pdf` | Executive brief — diagram + principles, open decisions, owners, naming notes |

## Readiness heat-map (Mike's green/orange idea)
- **In place / operational** `#C6E0B4`
- **In progress / building** `#BDD7EE`
- **Planned / not started** `#F2F2F2`
- **Gap / at risk** `#F4B7B7`
- **Open decision** `#FFF2CC` (diamond)

Edge colors: **red** = HIPAA / PHI path · **purple** = access/identity · blue dashed = cloud-LLM escalation · grey dashed = collaboration.

## Functional layers (top → bottom value flow)
1. **Access & Identity** — JumpCloud (SSO, identity-based audit) + Tailscale (zero-trust tailnet). Gates everything.
2. **Sources & System of Record** — ERPNext (ops/financials/AP-AR), Rethink (clinical), QuickBooks (legacy), inbound email/docs.
3. **Sensitive / PHI Zone** — AIPS clinical PHI store, segmented (Azure candidate).
4. **Anonymization — Air-Gap DMZ** — OpenClaw (de-identify/execution) + Hermes (metadata-only orchestration). VLAN30 DMZ / VLAN20 AI.
5. **Data Pipeline & Warehouse** — TIC/Cacheware C++ hash-parse → packaging → **Data Warehouse (host TBD ★)**.
6. **Intelligence Layer** — analysis & agents; local inference (Ollama) escalating de-identified prompts to cloud LLMs (Anthropic/OpenAI).
7. **Business Intelligence** — Metabase (dashboards, MCP, CLI); ERPNext Insights (candidate).

**Side bands:** Internal Collaboration control planes (Plane / AppFlowy / Affine), Infrastructure & Hosting (Hetzner today; Azure/AWS/hybrid-local target), DevSecOps/SRE overlay.

## Guiding principles (the vision)
- Separate human-moderated work from AI-driven work to prevent schema corruption.
- PHI never crosses untreated — de-identify + anonymize in a DMZ; de-identified ≠ HIPAA-safe unless it can't be re-linked.
- Consolidate & simplify; proven over custom ("you're not that special" — ~80% off-the-shelf).
- Data governance & sovereignty (bet that SaaS is dying) — but don't become a security company.
- Decision rigor on shared infrastructure: does control gained justify risk/overhead vs speed?

## Open decisions
- Data-warehouse host (Hetzner vs Azure vs AWS; Snowflake too costly) — Cissy researching cost + BI fit.
- All-cloud vs hybrid-local inference (sovereignty vs ops overhead).
- Azure vs AWS vs mixed shop (Azure BAA out-of-box + tenant admin; AWS cheaper/BYO-model processing).
- JumpCloud go/no-go — Mike's business case to Brian within 48h.
- DevSecOps / SRE hire (HIPAA audit + reliability = full-time role; current gap).
- Config lenses: AMDG vs AIBs vs AMDG-for-AIBs.

## Owners / next steps
- **Mike** — this diagram; JumpCloud business case (48h).
- **Hannah** — notes/diagrams to Box `AMDG InfraDesign`; split into network / infra / data-flow lenses; keep chipping technical side.
- **Cissy** — warehouse options + cost + BI-tool fit (ERPNext Insights).
- **Team** — Brian to run a BI/pipeline workshop; resolve contractor access to IP/data.

## ⚠ Confirm these (transcription guesses)
`Affine` (AFFiNE?), `AppFlowy`, `OpenClaw`, `Hermes`, `AIPS` (clinical PHI source), `TIC / Cacheware` C++ pipeline, `Hetzner`. Readiness colors are a first read from the call — **edit freely** in the `.mmd`.

## Re-render
```
echo '{"args":["--no-sandbox"]}' > /tmp/pptr.json
npx -y @mermaid-js/mermaid-cli -p /tmp/pptr.json -i Functional_Architecture.mmd -o Functional_Architecture.svg
```
