# ContextBoundary

**Enterprise AI Technical Boundary Architecture**

Version: 0.1 (Draft) · Read time: ~6 minutes · License: Apache 2.0

---

## What is ContextBoundary

ContextBoundary is a vendor-neutral, open-source reference architecture for defining where AI workloads physically sit and which boundaries data crosses in enterprise deployments. It maps three independent axes — data sensitivity, jurisdictional regulation, and vendor tier responsibility — into a single canonical diagram a CIO can read in seconds.

ContextBoundary is the technical-layer companion to [**ContextOps**](https://github.com/kannanokannan/contextops), the methodology layer above it. They are co-equal peers: ContextOps governs *how* context flows; ContextBoundary defines *where* it lives.

CIOs typically enter through ContextBoundary — driven by regulatory pressure (GDPR enforcement, India's DPDP Act, RBI guidelines, the EU AI Act) — and transform through ContextOps.

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

Five zones. Four boundary crossings. Three axes of constraint. One diagram that answers: *where does our accountability end and the vendor ecosystem begin?*

## Why Now

Three forces compound simultaneously:

- **Regulatory hardening.** GDPR enforcement has tightened after Schrems II. India's Digital Personal Data Protection (DPDP) Act is now in active enforcement with the Data Protection Board operational. RBI has expanded data localization requirements for financial services. The EU AI Act came into force in staged phases through 2025-26. Most regulated enterprises now answer to two or three regulators simultaneously.

- **Vendor chain complexity.** Enterprise AI rarely flows directly from customer to LLM provider. It passes through service vendors (AMS providers, system integrators) and product vendors (AI platform companies, vertical SaaS) before reaching the model. Each tier sees different data. Each tier holds different liabilities. No current framework names this chain explicitly.

- **Legacy hardware reality.** Existing reference architectures assume new GPU clusters, new cloud regions, new accreditation runways. Most mid-market enterprises and a large share of regulated firms operate on existing infrastructure investments. The question of how to use what is already there — as Tier II or Tier III substrate — is unaddressed in the literature.

ContextBoundary addresses these three pressures with one diagram and a small set of supporting artifacts.

## The Three Axes

| Axis | What it classifies | Established in literature |
|---|---|---|
| **Privacy Tier** | Data sensitivity: Tier I (public), Tier II (restricted), Tier III (sovereign) | Yes — adopted from industry research, not reinvented |
| **Geography / Jurisdiction** | Which regulatory regime governs the data: GDPR, DPDP, RBI, HIPAA, EU AI Act, SOC2 | Partially — codified here as Audit Profiles |
| **Vendor Tier Responsibility** | Which vendor tier operates the workload: Customer, AMS, Product, Compute, LLM | No — this axis is ContextBoundary's specific contribution |

The intersection of all three is the complete boundary picture.

## Hero Artifacts in v0.1

1. **The Boundary Diagram** — the five-zone canonical view above
2. **Privacy Tier Classification** — Tier I/II/III definitions and routing rules
3. **Vendor Tier Responsibility Matrix** — pipeline stages mapped to vendor zones
4. **Audit Profiles** — regulation-specific overlays on the boundary diagram (v0.1 ships with the GDPR profile)
5. **Endpoint Atlas** — registry classifying common AI vendors and services by which zone they operate in
6. **ContextOps Mapping** — the bridge document that connects ContextBoundary to the methodology layer

## How to Use ContextBoundary

A CIO reading the canonical diagram against the organization's current state lands in one of three diagnostic positions:

1. **No defined boundary.** Workloads cross zones without classification or contract review. Intervention: full boundary engagement, starting with Privacy Tier classification of existing Context Assets.
2. **Boundary defined but leaking.** Some zones classified, but specific crossings are silent — data flows from Customer to LLM Vendor without explicit AMS or Product Vendor scope. Intervention: targeted audit using the relevant Audit Profile, followed by contract remediation.
3. **Boundary defined and enforced.** All zones documented, all crossings governed by named instruments, all jurisdictional constraints active. Intervention: maturity certification and ongoing drift monitoring.

All three are buyable outcomes. ContextBoundary makes the diagnosis legible.

## What is in v0.1

- Core framework specification ([FRAMEWORK.md](FRAMEWORK.md))
- Architecture documentation: boundary diagram, tier classification, vendor tier matrix
- One complete Audit Profile: GDPR
- Endpoint Atlas with starter vendor entries
- ContextOps connector mapping

## Roadmap

🚧 v0.2 will add:

- Audit Profiles for India DPDP, RBI sectoral guidelines, HIPAA, SOC2, and the EU AI Act
- Expanded Endpoint Atlas covering the top 50 enterprise AI vendors and self-hosted equivalents
- The legacy hardware substrate guidance (existing infrastructure as Tier II/III platform)
- Worked examples for SMB clinic, mid-market cooperative bank, and Indian IT services firm

## What ContextBoundary is NOT

- Not a SaaS product, gateway, or tool. Existing gateways and routers implement parts of the framework.
- Not a replacement for ITIL, TOGAF, COBIT, NIST AI RMF, or any regulation. It overlays them at the technical layer.
- Not tied to any cloud provider, hardware vendor, or model family.
- Not a definition of organizational AI governance. That is ContextOps.
- Not legal advice. Audit Profiles describe architectural responses to regulation. Legal interpretation remains the responsibility of the adopting organization.
- Not a guarantee of compliance. Adopting the framework does not certify any deployment. Adoption supports defensible audit posture; it does not produce it automatically.

## Relationship to ContextOps

ContextOps owns the organizational layer: roles, practices, maturity model, context governance. ContextBoundary owns the technical layer: physical placement, jurisdictional boundary, vendor responsibility split.

The two frameworks are designed to be adopted together but can be adopted independently. ContextBoundary reaches full operational potential when paired with ContextOps' role taxonomy and Context Inventory practices.

See [`connectors/contextops-mapping.md`](connectors/contextops-mapping.md) for the explicit cross-mapping.

## Repository Structure

```
contextboundary/
├── README.md                          # This document
├── FRAMEWORK.md                       # Core specification
├── LICENSE                            # Apache 2.0
├── CONTRIBUTING.md                    # Contribution guidelines
├── architecture/
│   ├── boundary-diagram.md            # Canonical diagram with annotations
│   ├── tier-classification.md         # Privacy Tier I/II/III definitions
│   └── vendor-tier-matrix.md          # Five-zone responsibility matrix
├── audit-profiles/
│   ├── README.md                      # Audit Profile pattern
│   └── gdpr.md                        # GDPR profile (v0.1 reference)
├── endpoint-atlas/
│   ├── README.md                      # Endpoint Atlas pattern
│   └── vendors.md                     # Starter vendor registry
└── connectors/
    └── contextops-mapping.md          # Bridge to ContextOps methodology
```

## Framework Disclosure

ContextBoundary is itself a framework, and its adoption is subject to the same caution every framework deserves. Implement it selectively. Apply one Audit Profile, one boundary clarification, one vendor reclassification at a time. A reference architecture applied dogmatically replaces one form of confusion with another.

Use ContextBoundary to make the boundary visible. The decisions about what to do with that visibility belong to the organization.

---

ContextBoundary v0.1 — Draft. Licensed under Apache 2.0. Feedback welcome via GitHub Issues.
