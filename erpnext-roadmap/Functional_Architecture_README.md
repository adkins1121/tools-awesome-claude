# AMDG / ABES — Architecture (Functional v0.3 + Physical/Deployment v0.1)

> **Slop-reviewed.** Every design element was checked against Hannah's OmniSync docs; see
> [`TECHNICAL_REVIEW.md`](TECHNICAL_REVIEW.md) for the audit. **Provenance convention:**
> CANONICAL = stated in the OmniSync docs (drawn plain); **△ overlay** = from the 6/24 call
> (business systems / hosting), not in the docs, drawn dashed and marked "confirm".
>
> **Deliverables:** `Functional_Architecture.{mmd,svg,pdf,html}` ·
> `Deployment_Architecture.{mmd,svg,pdf,html}` · `Architecture_Pack_Brief.pdf` (both views +
> review table) · `TECHNICAL_REVIEW.md`.

---


A **functional** architecture map — capabilities as boxes, color-coded by readiness —
meant as a single source of truth to **set vision and align the team**.

**v0.2** re-grounds the map in Hannah's authoritative **OmniSync gated/staged architecture**
(dropped in Box · `AMDG InfraDesign`), keeping Mike's executive heat-map altitude and the
business overlays (ERPNext, BI, access, hosting, DevSecOps) from the call.

**Sources:**
- Fathom call **"Hannah x Mike // Connect on Architecture"**, 2026-06-24 (Mike, Hannah, Cissy) — https://fathom.video/calls/723282376
- Hannah's **OmniSync** repo (Box · AMDG InfraDesign): `omnisync-readme.md`, `agentic-harness-north-star.md`, `omnisync-gated-staged-architecture` — OmniSync structure is authoritative; business overlays come from the call.

## What changed v0.1 → v0.2
- **OpenClaw + Hermes are the CONTROL PLANE** (Execute + Orchestrate under a Local-AI "Reason" layer) — not a standalone de-identification DMZ as v0.1 drew. Today's stand-ins: Claude Desktop/Code → command queue → Notion AI/Claude Code.
- **The "air-gap" = default-deny INGESTION GATES** (per-task, dependency-layer, runtime, and a human gate for BAA / key ceremony / compliance / go-no-go). Default is *don't move*; data stays local unless promoted.
- **The "data warehouse" = the local-first CANONICAL SUBSTRATE** — Postgres append-only custody + Neo4j/Graphiti knowledge graph + MinIO + pgvector. Local-first by design → the open call is a *sovereign-substrate host*, not a cloud DW.
- **Canonical vs projection** is the core invariant: Notion / AppFlowy / Affine / Plane are all *projections*, never source of truth.
- **Staged migration by substitution:** Stage 1 Notion orchestrator (now) → Stage 2 Notion control plane → Stage 3 AppFlowy (control) + Affine (content); Notion ends as a sanitized egress edge.
- **Storage decisions (2026-04-17):** NATS (not Redis); Postgres custody → Neo4j CE + pgvector (not Directus); MinIO; bge-m3 + local 8B/14B with cloud escalation.
- **Names now confirmed:** OpenClaw, Hermes, Affine, AppFlowy, Ollama — plus NATS, Postgres custody, Neo4j/Graphiti, MinIO, FastAPI, bge-m3.

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

## Functional layers (OmniSync zones + business overlays)
- **Control Plane** (spans top) — Reason → Orchestrate → Execute = **Local AI → Hermes → OpenClaw fleet** (today: Claude Desktop/Code → command queue → Notion AI/Claude Code). Build-time agents (Claude Code) are distinct from run-time agents.
- **Zone 1 · Inputs** — Zoom Phone (★ ABES anchor lane), email, calendar, transcripts (Fireflies/Fathom), documents/notes; **business system-of-record** ERPNext / Rethink / QuickBooks.
- **Zone 2 · Gated sorting pipeline** — FastAPI intake → NATS queue → workers (metadata/recording/transcript/parser) → Ollama entity extraction (local 8B/14B + bge-m3, cloud escalation).
- **Ingestion gates (default-deny)** — per-task + dependency-layer gates, **human gates** (BAA / key ceremony / compliance / go-no-go), runtime data gates, promote-vs-stay-local. *This is the HIPAA boundary the call called the "air-gap."*
- **Zone 3 · Canonical substrate (local-first — the data you own)** — Postgres append-only custody + Neo4j/Graphiti knowledge graph + MinIO + pgvector → **OmniSync API**. **Substrate host ★** is the open "data warehouse" decision.
- **Zone 4 · Projection surfaces (derived, staged)** — Stage 1 Notion orchestrator (current) → Stage 2 Notion control plane → Stage 3 AppFlowy (control) + Affine (content); Plane as the team PM surface; Notion → sanitized edge.
- **Business Intelligence** — Metabase (dashboards, MCP, CLI; ABES telephony/billing = TIC pipeline); ERPNext Insights (candidate).

**Cross-cutting bands:** Access & Identity (JumpCloud + Tailscale), Compliance/HIPAA + sovereignty, Infrastructure/Hosting (Hetzner today; Azure/AWS/hybrid target), DevSecOps/SRE overlay.

## Guiding principles (north star — from `agentic-harness-north-star.md`)
- Maximum agent delegation; humans gate only the unavoidable (legal, compliance, key ceremony, go/no-go).
- Nothing advances without passing a gate; the default is **deny** (data stays local unless promoted).
- Own your canonical state; everything else is a **projection** (derived, regenerable, removable).
- Migrate by **substitution, not rebuild** — hold invariants constant so a component swap is not a re-architecture.
- Adversarial-first; verification is the unit of trust. Skeleton (gated plan) + muscle (tight iterative loops).
- An agent system is a **sorting machine** — staged/gated is choosing & measuring the right sort before scaling.

## Open decisions
- **Canonical-substrate host** (Hetzner vs Azure vs hybrid-local; Snowflake too costly) — Cissy researching cost + BI fit.
- All-cloud vs hybrid-local inference (sovereignty vs ops overhead).
- Azure vs AWS vs mixed shop (Azure BAA out-of-box + tenant admin; AWS cheaper/BYO-model processing).
- JumpCloud go/no-go — Mike's business case to Brian within 48h.
- DevSecOps / SRE hire (HIPAA audit + reliability = full-time role; current gap).
- **Plane vs Notion/AppFlowy** — reconcile the team PM surface with the OmniSync projection staging.
- **ERPNext's role** vs the canonical graph — system-of-record that is both an input source and a sanctioned store (PHI held separately); confirm placement.
- Config lenses: AMDG vs AIBs vs AMDG-for-AIBs.

## Owners / next steps
- **Mike** — this functional map (single source of truth); JumpCloud business case (48h).
- **Hannah** — OmniSync technical build; split network / infra / data-flow lenses; keep canonical-vs-projection discipline.
- **Cissy** — substrate/warehouse host options + cost + BI-tool fit (ERPNext Insights).
- **Team** — Brian to run a BI/pipeline workshop; resolve contractor access to IP/data.

## Notes
Readiness colors are a working read — **edit freely** in the `.mmd`. Open reconciliation items are
flagged above (Plane vs Notion/AppFlowy; ERPNext vs the canonical graph). The OmniSync source
diagram lives in Box (`omnisync-gated-staged-architecture`); this map is the executive/functional
projection of it with the business systems and readiness overlaid.

## Re-render
```
echo '{"args":["--no-sandbox"]}' > /tmp/pptr.json
npx -y @mermaid-js/mermaid-cli -p /tmp/pptr.json -i Functional_Architecture.mmd -o Functional_Architecture.svg
```
