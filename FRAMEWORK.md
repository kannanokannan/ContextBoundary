# ContextBoundary

**AI Data Egress Governance Specification**

Version: 0.1 · Read time: ~12 minutes · License: Apache 2.0

---

## What is ContextBoundary

ContextBoundary is a vendor-neutral, open-source specification for enterprise AI data egress governance. It gives organizations a structured way to classify outbound data flows, assign boundary zones, distribute vendor responsibility, and produce defensible audit evidence across the AI delivery chain.

The framework operates at the technical layer. It overlays existing infrastructure, gateway, and identity stacks rather than replacing them. It is the technical companion to [ContextOps](https://github.com/kannanokannan/contextops), which operates at the organizational layer.

ContextBoundary fills a specific gap. For runtime action authorization, the open standard is AARM v1.0 (Cloud Security Alliance) - ContextBoundary aligns with it as a Core-partial strict-determinism profile and maps to its Protocol Gateway architecture. But AARM is action-authorization-centric and specifies no data-sovereignty or vendor-continuity model. Those extensions - Egress Tiers, vendor/jurisdiction zones and Audit Profiles, vendor continuity - are ContextBoundary's contribution.

North Star: the boundary diagram a CIO can read in seconds.

## Why Now

Enterprise AI no longer flows directly from customer to LLM provider. It passes through service vendors, product vendors, and compute vendors before reaching the model. Each tier sees different data. Each tier holds different liabilities. Each crossing is governed by a different legal instrument.

Three regulatory pressures compound this complexity:

- GDPR enforcement has intensified after the invalidation of Privacy Shield. Standard Contractual Clauses face renewed scrutiny. The EU AI Act entered staged enforcement through 2025-26; the Digital Omnibus amendment (formally adopted June 2026) deferred high-risk obligations to December 2, 2027 for standalone Annex III systems and August 2, 2028 for AI embedded in Annex I regulated products.
- India's Digital Personal Data Protection Act is in phased enforcement: DPDP Rules notified November 2025 legally constituted the Data Protection Board, though chair and member appointments were still in progress as of mid-2026; most fiduciary obligations phase in through mid-2027. Sectoral guidelines from RBI on financial data localization have tightened.
- US healthcare and financial services continue to operate under HIPAA, SOC2, and emerging state-level legislation including Texas TRAIGA, NYDFS Part 500, and Colorado Insurance Law.

Most regulated enterprises now answer to two or three regulators simultaneously. Vendor whitepapers cover their own products. Academic frameworks remain unimplemented. Compliance documents specify obligations but not architecture. ContextBoundary fills the methodology gap between these.

## Core Vocabulary

Precise vocabulary is itself a deliverable. Buzzword drift kills shared understanding between architecture, security, procurement, and compliance teams. Shared understanding is the foundation for defensible AI deployment.

**Boundary Zone** — One of five defined zones in the canonical diagram: Customer Sovereign, AMS/Service Vendor, Product Vendor, Compute Vendor, LLM Vendor. Each zone has a default Egress Tier ceiling and a default contract type.

**Egress Tier** — The classification of a payload by permitted outbound flow: Tier I never leaves the system, Tier II may leave only as anonymised aggregates with consent, and Tier III may leave through explicit per-call API escalation.

**Crossing Point** — A defined seam between two Boundary Zones. Every Crossing Point is governed by an explicit legal instrument (MSA, DPA, license, BAA, SCC, or cloud agreement) and an explicit data redaction or tokenization policy.

**Audit Profile** — A regulation-specific overlay on the canonical boundary diagram. Each profile constrains specific zones and crossings, defines required asset metadata, and lists evidence required for regulator audit.

**Endpoint** — A specific AI service, model, or infrastructure component classified by which Boundary Zone it operates in. Endpoints are maintained in the Endpoint Atlas.

**Vendor Tier Responsibility** — The assignment of specific pipeline stages to specific Boundary Zones. Pipeline stages include data capture, redaction, embedding, retrieval, inference, output sanitization, and audit logging.

**Sovereign Egress** — Any data flow that leaves the Customer Sovereign Zone. All Sovereign Egress events must be classified, contracted, and logged. Silent Sovereign Egress is the primary failure mode the framework prevents.

**Custody Boundary** — The seam between Compute Vendor and any other zone. Custody boundaries handle the case where customer-owned model weights or data reside on infrastructure operated by another party (Outposts, BYOC GPU, hosted by service vendor).

## The Three Axes

ContextBoundary operates across three independent axes. The intersection of all three is the complete boundary picture for any given workload.

| Axis | What it classifies | Defines |
|---|---|---|
| Egress Tier | Egress permission | Which crossings are permitted |
| Geography / Jurisdiction | Which regulatory regime governs | Which crossings are permitted, restricted, or prohibited |
| Vendor Tier Responsibility | Which vendor tier operates the stage | Which contract type governs the crossing |

## The Five Boundary Zones

```
┌───────────┬───────────┬───────────┬───────────┬───────────┐
│ CUSTOMER  │  AMS /    │ PRODUCT   │ COMPUTE   │   LLM     │
│ SOVEREIGN │  SERVICE  │ VENDOR    │ VENDOR    │   VENDOR  │
│  ZONE     │  VENDOR   │           │           │           │
├───────────┼───────────┼───────────┼───────────┼───────────┤
│ Capture   │ Ingestion │ Vector DB │ GPU/NPU   │ Inference │
│ PII redact│ ETL       │ Embedding │ hosting   │ Generation│
│ Audit/WORM│ Orchestr. │ Reranking │ Storage   │           │
│ Identity  │ Monitoring│ App layer │ custody   │           │
├───────────┼───────────┼───────────┼───────────┼───────────┤
│ Tier I    │ Tier II   │ Tier II   │ Tier II/  │ Tier II/  │
│           │           │           │ Tier III  │ Tier III  │
├───────────┼───────────┼───────────┼───────────┼───────────┤
│ Customer  │ MSA + DPA │ License + │ Cloud     │ BAA/DPA + │
│accountable│           │ DPA       │ agreement │ contract  │
└───────────┴───────────┴───────────┴───────────┴───────────┘
```

**Zone 1 — Customer Sovereign Zone**
Data that never leaves the customer boundary. Tier I ceiling. PII, regulated records, sovereign data. Customer is fully accountable. No vendor contract governs this zone — customer policy does.

**Zone 2 — AMS / Service Vendor Zone**
Managed service providers operating within the customer's extended boundary. Tier II ceiling. Governed by MSA and DPA. Includes ingestion pipelines, ETL, orchestration, and monitoring operated by system integrators or AMS providers.

**Zone 3 — Product Vendor Zone**
Software vendors with scoped data access. Tier II ceiling. Governed by license agreement and DPA. Includes vector databases, embedding services, reranking layers, and AI application platforms.

**Zone 4 — Compute Vendor Zone**
Infrastructure and cloud compute providers. Tier II or Tier III depending on data classification. Governed by cloud agreement. Includes GPU hosting, storage, and custody of customer-owned model weights on third-party infrastructure.

**Zone 5 — LLM Vendor Zone**
Model inference providers. Tier II or Tier III depending on data classification and contract. Governed by BAA, DPA, or contract. Includes hosted inference, generation, and model API access.

## The Three Egress Tiers

| Tier | Egress Rule | Applies To |
|------|-------------|------------|
| **Tier I** | Never leaves the system | Sovereign data, PII, regulated records |
| **Tier II** | Anonymised aggregates only, with consent | Behavioural metrics, derived insights |
| **Tier III** | Explicit per-call API escalation | Public data, sanctioned external lookups |

Tier numbering is egress-centric. Lower number = higher protection. This is locked and must not be inverted.

Every outbound request is tagged before it crosses the boundary. The boundary enforces. The audit trail records.

## Crossing Points and Legal Instruments

Every seam between zones is a Crossing Point. Each Crossing Point must be covered by:

1. A named legal instrument (MSA, DPA, license agreement, BAA, SCC, or cloud agreement)
2. An explicit data handling obligation in that instrument covering AI workloads
3. A defined redaction or tokenization policy for data crossing at Tier II or above
4. An audit log entry for every Sovereign Egress event

Silent crossings — data flowing between zones without a named instrument — are the primary compliance failure mode ContextBoundary is designed to prevent.

## Audit Profiles

An Audit Profile is a regulation-specific overlay on the canonical boundary diagram. Each profile constrains specific zones and crossings, defines required asset metadata, and lists evidence required for regulator audit.

v0.1 ships the GDPR Audit Profile as the reference implementation. Additional profiles will be added in future versions as the framework matures and field validation informs scope.

Each Audit Profile answers:

- Which zones are constrained by this regulation?
- Which crossings require explicit legal instruments?
- What data minimisation obligations apply at each crossing?
- What audit evidence must exist before or after a crossing?
- What is the regulator's expected audit posture?

Audit Profiles do not replace legal advice. They provide architectural responses to regulatory obligations. Legal interpretation remains the responsibility of the adopting organisation.

## Endpoint Atlas

The Endpoint Atlas is a registry of specific AI services, models, and infrastructure components classified by which Boundary Zone they operate in.

The Atlas answers: when we send data to this specific vendor or service, which zone does it enter, which Egress Tier applies, and which contract type must be in place?

v0.1 ships a starter set of vendor entries. The Atlas is designed to be extended by adopters for their specific vendor portfolios.

## Adoption Path

A CIO reading the canonical diagram against the organisation's current state lands in one of three diagnostic positions:

**No defined boundary.** Workloads cross zones without classification or contract review. Intervention: full boundary engagement, starting with Egress Tier classification of existing data flows.

**Boundary defined but leaking.** Some zones classified, but specific crossings are silent — data flows between zones without explicit instruments. Intervention: targeted audit using the relevant Audit Profile, followed by contract remediation.

**Boundary defined and enforced.** All zones documented, all crossings governed by named instruments, all jurisdictional constraints active. Intervention: maturity certification and ongoing drift monitoring.

## What ContextBoundary Is Not

- Not a SaaS product, gateway, or tool. Existing gateways and routers implement parts of the framework.
- Not a replacement for ITIL, TOGAF, COBIT, NIST AI RMF, or any regulation. It overlays them at the technical layer.
- Not tied to any cloud provider, hardware vendor, or model family.
- Not a definition of organizational AI governance. That is ContextOps.
- Not legal advice. Audit Profiles describe architectural responses to regulation. Legal interpretation remains the responsibility of the adopting organization.
- Not a guarantee of compliance. Adopting the framework does not certify any deployment. Adoption supports defensible audit posture; it does not produce it automatically.
- Not a hardware sovereignty claim. ContextBoundary governs data flows, not silicon.

## Relationship to ContextOps

ContextOps owns the organizational layer: roles, practices, maturity model, context governance. ContextBoundary owns the technical layer: physical placement, jurisdictional boundary, vendor responsibility split.

The two frameworks are designed to be adopted together but can be adopted independently. ContextBoundary reaches full operational potential when paired with ContextOps role taxonomy and Context Inventory practices.

## Relationship to Sthala

Sthala is a governed runtime placement reference that consumes ContextBoundary's egress contract. Sthala decides where the AI runs. ContextBoundary decides where data is permitted to go. They are complementary, not overlapping.

Do not conflate runtime placement decisions (Sthala) with egress governance decisions (ContextBoundary).
