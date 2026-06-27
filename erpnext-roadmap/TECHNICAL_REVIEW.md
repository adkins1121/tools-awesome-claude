# Technical Review — Functional & Deployment diagrams vs. Hannah's OmniSync docs

**Trigger:** Hannah flagged slop. This review checks every design element I incorporated
against the authoritative sources and corrects what doesn't hold up.

**Authoritative sources (Box · AMDG InfraDesign):** `omnisync-readme.md`,
`docs/omnisync-plan.md` (§1–§7, 2026-05-28), `agentic-harness-north-star.md`,
`omnisync-gated-staged-architecture` (README mermaid), `omnisync-gated-staged-architecture.png`.
**Secondary (interpretation only):** Fathom call "Hannah x Mike // Connect on Architecture" 2026-06-24.

**Provenance rule going forward:**
- **CANONICAL** = stated in Hannah's OmniSync docs. Drawn plain.
- **△ OVERLAY** = from the 6/24 call (business systems / hosting), *not* in the OmniSync docs. Drawn dashed, quarantined in a labelled overlay band, marked "confirm".

The root cause of the slop was mixing the two without marking which is which.

## Findings & corrections

| # | Slop / error | Why it's wrong (source) | Correction |
|---|---|---|---|
| 1 | **pgvector bundled into the MinIO node** (functional v0.2) | plan §3 storage stack: vectors = **pgvector on Postgres** (passage chunks, bge-m3, 1024-dim); **MinIO** = object store for **recordings** only | Split into two nodes: `Postgres + pgvector` and `MinIO — object store (recordings)` |
| 2 | **Metabase labelled as the OmniSync "telephony/billing" lane** | OmniSync anchor lane = **Zoom Phone telephony** (webhook→NATS→workers→custody→graph). The **TIC/Cacheware C++ → CSV → Metabase** stack is a *separate existing analytics pipeline in AWS* (6/24 call), not the OmniSync substrate | Move Metabase to the △ overlay; label it "existing AWS TIC/Cacheware BI"; stop implying it consumes the OmniSync API |
| 3 | **ERPNext / Rethink / QuickBooks placed inside Zone 1 inputs** | Documented OmniSync sources are email, transcripts, calls, calendar, documents/notes. ERPNext/Rethink/QuickBooks are business systems (call / ERPNext plan), **not** named OmniSync ingestion sources | Move to the △ overlay band, "placement vs substrate TBD" |
| 4 | **Substrate-host decision omitted the on-prem target** (functional + deployment) | plan §3: the sovereign **target is on-prem** — MSI Titan host + Hyper-V AI-zone VM + Unifi VLANs, AI zone **no WAN** | State on-prem/Unifi as the documented target; list Hetzner(today)/Azure/hybrid as the open *placement* alternatives from the call |
| 5 | **No provenance distinction** (both) | — (this is the slop root cause) | Added the △ overlay convention + legend; quarantined call-derived nodes |
| 6 | **Deployment build error** — `linkStyle` indices 20/21 referenced; only 0–19 exist | mermaid edge indices | Re-indexed (bridge 6; escalation 14,15; access 16,17; identity 18; placement 19) |
| 7 | **"Plane / Notion" asserted in VLAN 10 Trusted** (deployment) | §3 topology shows **Obsidian** in VLAN 10 and **AppFlowy/AFFiNE** in VLAN 20; Plane is a call-derived PM candidate | Tagged △ overlay (confirm), not asserted as topology |
| 8 | **JumpCloud shown as canonical identity** (deployment) | Not in §3 topology; from the call. **Tailscale** *is* in her topology | JumpCloud → △ overlay; Tailscale stays canonical |
| 9 | **EA / personal-assistant lane missing** (functional) | plan §BLUF (2026-06-01): the **EA lane — email-parse + transcript-parse for Bryan — is the first MVP/PoC**; telephony is the anchor/proving lane | Added lane tags: ★ anchor (telephony), ◆ MVP/EA (email + transcript) |

## Verified correct — no change

These were checked against the docs and are accurate:
- Control plane **reason→orchestrate→execute = Local AI → Hermes → OpenClaw** (today: Claude Desktop/Code → command queue → Notion AI/Claude Code). *(north-star, readme, plan §3)*
- Workers: **metadata · recording · transcript · parser**; **NATS — intake.raw**; **Ollama local 8B/14B + bge-m3, cloud escalation**. *(readme mermaid, plan §1)*
- Gates chain: per-task/dependency-layer → **human gate (BAA, key ceremony, compliance, go/no-go)** → runtime → promote-vs-stay-local (**default-deny**). *(readme, north-star, plan §3 mermaid)*
- Canonical stores: **Postgres append-only custody**, **Neo4j CE + Graphiti**, **Postgres + pgvector**, **MinIO**; **OmniSync API**. *(plan §3 storage stack)*
- Staged projections: **Stage 1 Notion → Stage 3 AppFlowy (control) + Affine (content)**, Notion demoted to a **sanitized edge**; all surfaces are **projections**. *(plan §3, readme)*
- Deployment zoning: **VLAN 10 Trusted (MSI Titan) / VLAN 20 AI zone — no WAN (Hyper-V) / VLAN 30 DMZ — only WAN**; **NATS = sole inter-zone bridge**; cloud-LLM proxy **de-identifies + logs**, escalation only. *(plan §3 topology mermaid)*

## Open reconciliation items (not slop — genuine decisions for the team)

- **Plane vs Notion/AppFlowy** as the human/PM projection surface.
- **ERPNext's role** vs the canonical graph (system-of-record that is both a source and a sanctioned store; PHI separate).
- **Hosting placement** — Hannah's documented target is on-prem sovereign; the 6/24 call kept Hetzner/Azure/hybrid open. The VLAN *zoning* is the invariant; *where each zone lands* is the decision.
- **Two lanes** — anchor (ABES Zoom-Phone telephony, HIPAA proving ground) vs MVP/EA (Bryan email+transcript). Confirm which the team is funding first.

## Build thesis context (so the diagrams aren't read as greenfield)

Per plan §5–§6, the engine is **not** being built from scratch: it cherry-picks **KGM's sovereign
skeleton** (already Neo4j + Postgres custody) and ports **AI-memory's subsystems** (BAML, learning
machine, UTCP, transcript/task) onto it. `deploy/` (docker-compose) is **not yet stood up** (stage S1).
Readiness colors reflect that: most substrate nodes are *planned/target*, not *running*.
