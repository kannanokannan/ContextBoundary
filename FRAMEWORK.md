# ContextBoundary

**Enterprise AI Technical Boundary Architecture**

Version: 0.1 (Draft) · Read time: ~12 minutes · License: Apache 2.0

---

## What is ContextBoundary

ContextBoundary is a vendor-neutral, open-source reference architecture for enterprise AI boundary design. It gives organizations a structured way to classify data, place compute, distribute vendor responsibility, and produce defensible audit evidence across the AI delivery chain.

The framework operates at the technical layer. It overlays existing infrastructure, gateway, and identity stacks rather than replacing them. It is the technical companion to [ContextOps](https://github.com/kannanokannan/contextops), which operates at the organizational layer.

ContextBoundary fills a specific gap. Industry literature describes hybrid AI deployment patterns, privacy-preserving inference frameworks, AI gateway implementations, and compliance obligations — but no single vendor-neutral framework ties data sensitivity, jurisdictional regulation, and multi-vendor responsibility together into one architecture an enterprise can adopt.

North Star: the boundary diagram a CIO can read in seconds.

## Why Now

Enterprise AI no longer flows directly from customer to LLM provider. It passes through service vendors, product vendors, and compute vendors before reaching the model. Each tier sees different data. Each tier holds different liabilities. Each crossing is governed by a different legal instrument.

Three regulatory pressures compound this complexity:

- GDPR enforcement has intensified after the invalidation of Privacy Shield. Standard Contractual Clauses face renewed scrutiny. The EU AI Act came into staged enforcement through 2025-26.
- India's Digital Personal Data Protection Act is now in active enforcement with the Data Protection Board operational. Sectoral guidelines from RBI on financial data localization have tightened.
- US healthcare and financial services continue to operate under HIPAA, SOC2, and emerging state-level legislation including Texas TRAIGA, NYDFS Part 500, and Colorado Insurance Law.

Most regulated enterprises now answer to two or three regulators simultaneously. Vendor whitepapers cover their own products. Academic frameworks remain unimplemented. Compliance documents specify obligations but not architecture. ContextBoundary fills the methodology gap between these.

## Core Vocabulary

Precise vocabulary is itself a deliverable. Buzzword drift kills shared understanding between architecture, security, procurement, and compliance teams. Shared understanding is the foundation for defensible AI deployment.

**Boundary Zone** — One of five defined zones in the canonical diagram: Customer Sovereign, AMS/Service Vendor, Product Vendor, Compute Vendor, LLM Vendor. Each zone has a default Privacy Tier ceiling and a default contract type.

**Privacy Tier** — The classification of a data payload by sensitivity: Tier I (public/non-sensitive), Tier II (restricted/proprietary), Tier III (critical/sovereign). Adopted from established industry research.

**Crossing Point** — A defined seam between two Boundary Zones. Every Crossing Point is governed by an explicit legal instrument (MSA, DPA, license, BAA, SCC, or cloud agreement) and an explicit data redaction or tokenization policy.

**Audit Profile** — A regulation-specific overlay on the canonical boundary diagram. Each profile constrains specific zones and crossings, defines required asset metadata, and lists evidence required for regulator audit.

**Endpoint** — A specific AI service, model, or infrastructure component classified by which Boundary Zone it operates in. Endpoints are maintained in the Endpoint Atlas.

**Vendor Tier Responsibility** — The assignment of specific pipeline stages to specific Boundary Zones. Pipeline stages include data capture, redaction, embedding, retrieval, inference, output sanitization, and audit logging.

**Sovereign Egress** — Any data flow that leaves the Customer Sovereign Zone. All Sovereign Egress events must be classified, contracted, and logged. Silent Sovereign Egress is the primary failure mode the framework prevents.

**Custody Boundary** — The seam between Compute Vendor and any other zone. Custody boundaries handle the case where customer-owned model weights or data reside on infrastructure operated by another party (Outposts, BYOC GPU, on-prem hosted by service vendor).

## The Three Axes

ContextBoundary operates across three independent axes. The intersection of all three is the complete boundary picture for any given workload.

| Axis | What it classifies | Defines |
|---|---|---|
| Privacy Tier | Data sensitivity | Which zones may process the data |
| Geography / Jurisdiction | Which regulatory regime governs | Which crossings are permitted, restricted, or prohibited |
| Vendor Tier Responsibility | Which vendor tier operates the stage | Which contract type governs the crossing |

A single workload classification answers four questions:

1. **WHO** operates this pipeline stage? (Vendor Tier Responsibility)
2. **WHAT** Privacy Tier data may flow into it?
3. **WHICH** jurisdictional rules apply at every crossing? (Audit Profile)
4. **WHAT** legal instrument governs each crossing?

The four-question matrix is the unit of analysis the framework produces. Every Context Asset, every agent, every workload runs through it.

## The Canonical Boundary Diagram

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
│ Tier III  │ Tier II   │ Tier II   │ Tier I/II │ Tier I/II │
├───────────┼───────────┼───────────┼───────────┼───────────┤
│ Customer  │ MSA + DPA │ License + │ Cloud     │ BAA/DPA + │
│accountable│           │ DPA       │ agreement │ contract  │
└───────────┴───────────┴───────────┴───────────┴───────────┘
   ▲                                                   ▲
   │                                                   │
GDPR / DPDP / RBI                            BAA, SCC, DPA
jurisdiction-bound                            contract-bound
```

Five zones. Four crossings. The diagram is the framework's hero artifact. Detailed annotations are in [`architecture/boundary-diagram.md`](architecture/boundary-diagram.md).

## Reference Topology

A canonical use case walks through the diagram to illustrate the framework in operation: enterprise document RAG with selective frontier inference.

A user query enters the Customer Sovereign Zone. The local PII detection layer classifies the payload and assigns a Privacy Tier. Tier III content is identified and either redacted or held for local-only processing. The cleaned query and its retrieval context are passed through the AMS/Service Vendor zone for orchestration. The Product Vendor zone hosts the vector database and reranking layer that returns enterprise document chunks. For complex reasoning, the orchestrator routes a redacted prompt through the Compute Vendor zone to the LLM Vendor for inference. The response returns through the same chain. The output is reattached to original tokens in the Customer Sovereign Zone before delivery to the user. Every crossing logs to WORM storage in the Customer Sovereign Zone, with timestamp, payload hash, classification decision, and vendor tier identifier.

The same workload, under a different Audit Profile, may have different permissible crossings. Under a GDPR Profile, the LLM Vendor crossing requires SCC verification. Under a DPDP Profile, the same crossing may be permitted directly but with localized data residency. Under an RBI Profile applied to financial customer data, the LLM Vendor crossing may be prohibited entirely, forcing local inference.

## Audit Profile Pattern

An Audit Profile is a regulation-specific overlay on the canonical boundary diagram. Each profile contains the same set of sections:

1. **Regulation summary** — short, factual restatement of what the regulation covers and what triggers its application.
2. **Mandatory boundary constraints** — which zones are restricted, which crossings are prohibited, and under what conditions exceptions are permitted.
3. **Privacy Tier mapping** — how the regulation's data categories map to Tier I/II/III. Where the regulation defines its own taxonomy (special categories, financial records, health data), the mapping is explicit.
4. **Required asset metadata** — what each Context Asset must record to support audit. This typically includes lawful basis, controller or processor designation, retention period, and cross-border transfer mechanism where applicable.
5. **Evidence requirements** — what artifacts the regulator may demand. DPIAs, ROPAs, transfer impact assessments, audit logs, breach notification records, attestation reports.
6. **Conflict with other profiles** — explicit notes where this profile conflicts with another (for example, GDPR data minimization vs. industry-specific retention mandates).

v0.1 ships with the GDPR profile as the reference example. v0.2 will add DPDP, RBI, HIPAA, SOC2, and the EU AI Act.

## Endpoint Atlas Pattern

The Endpoint Atlas is a registry classifying common AI services and infrastructure by their default Boundary Zone. Each entry contains:

- Endpoint name (vendor-neutral category followed by representative implementation where helpful)
- Default Boundary Zone
- Conditional zones — under what configurations the endpoint shifts zones (for example, a managed LLM API may behave as LLM Vendor by default but as Compute Vendor under a BYOC arrangement)
- Sovereignty notes — short, factual notes on cross-border data flow, residency options, certifications held
- Audit Profile compatibility — flags indicating which profiles the endpoint is compatible with by default

The Atlas is descriptive, not prescriptive. It does not endorse vendors. It classifies them.

## Connection to ContextOps

ContextOps governs the organizational layer of enterprise AI. ContextBoundary governs the technical layer. The two frameworks are designed to interoperate.

The bridge is bidirectional:

- ContextBoundary's Privacy Tier classification is metadata attached to ContextOps' Context Assets.
- ContextBoundary's Audit Profile selection is a Curate-stage decision in the ContextOps spine.
- ContextBoundary's Vendor Tier Responsibility matrix is the technical realization of ContextOps' AI Amplifier Assessment for compute boundaries.
- ContextBoundary's Sovereign Egress logging feeds ContextOps' Renew-stage drift monitoring and Non-Deterministic Triage Protocol.

ContextOps without ContextBoundary leaves technical placement decisions implicit. ContextBoundary without ContextOps leaves organizational ownership of those decisions implicit. Either can be adopted alone. Both gain operational potential when paired.

See [`connectors/contextops-mapping.md`](connectors/contextops-mapping.md) for the complete cross-mapping.

## What ContextBoundary is NOT

- Not a SaaS product, gateway, or tool. Specific gateways and routers implement portions of the framework; the framework itself remains vendor-neutral.
- Not a replacement for ITIL, TOGAF, COBIT, NIST AI RMF, ISO 27001, or any regulation. It overlays them at the technical layer.
- Not tied to any cloud provider, hardware vendor, or model family.
- Not a definition of organizational AI governance. That is ContextOps.
- Not legal advice. Audit Profiles describe architectural responses to regulatory obligations. Legal interpretation remains with the adopting organization and its counsel.
- Not a certification scheme. Adoption supports defensible audit posture; it does not constitute compliance by itself.
- Not a guarantee that any deployment is secure or compliant. The framework makes the boundary visible; the organization makes the boundary real.

ContextBoundary does not own model selection, application design, vendor procurement decisions, or the political dynamics that shape any of those. Being explicit about what the framework does not own makes the claim on what it does own credible.

## Roots

The patterns named in this framework — tiered privacy classification, hybrid on-prem and cloud deployment, gateway-based egress control, air-gapped sovereign enclaves, federated identity — are documented across enterprise architecture practice, academic research, and vendor blueprints from the past five years.

The contribution of ContextBoundary is the synthesis of those patterns into a vendor-neutral framework that integrates with organizational governance methodology, treats multi-vendor responsibility as a first-class axis, and remains open and extensible.

ContextBoundary sits on top of:

- Hybrid Privacy-Preserving Large Language Model (HPP-LLM) academic framework
- Established industry hybrid deployment patterns (cloud-train on-prem-serve, on-prem-anonymize cloud-fine-tune, fully sovereign on-prem)
- Existing AI gateway implementations including open-source and commercial products
- NIST AI RMF 1.0 risk taxonomy
- Privacy Tier I/II/III classification as documented across regulated industry literature

It does not replace any of them.

---

ContextBoundary v0.1 — Draft. Stubs marked 🚧. Feedback welcome via GitHub Issues.

Licensed under Apache 2.0.
