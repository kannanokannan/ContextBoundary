# ContextBoundary

**The egress governance specification for AI data flows — deployment-agnostic, regulatory-mapped, enforced at the network layer.**

Version: 0.1 · License: Apache 2.0 · Read time: ~6 minutes

> Not to be confused with the Domain-Driven Design term, the UIKit API, or video alignment research of the same name. This is an AI data egress specification.

---

## What is ContextBoundary

ContextBoundary is an open-source specification that defines where AI-processed data is permitted to flow — and where it is not. Every outbound flow is tagged with a privacy tier and a regulatory jurisdiction before it crosses the boundary, producing a written, auditable, enforceable contract between any AI system and the external services it touches.

It is deployment-agnostic. On-prem, cloud, hybrid — the specification does not care where the AI runs. It cares where your data is allowed to go and under what conditions.

It is the technical-layer companion to [**ContextOps**](https://github.com/kannanokannan/ContextOps) (organisational governance) and is consumed by [**Sthala**](https://github.com/kannanokannan/Sthala) (sovereign on-prem reference implementation).

---

## Why Now

Three deadlines are converging in 2026:

- **EU AI Act** — comprehensive obligations for High-Risk AI systems take effect **August 2, 2026**. Fines up to €35M or 7% of global turnover.
- **India DPDP Act** — Data Protection Board operational since November 2025. Consent Manager Framework binding by November 2026. Full Data Fiduciary compliance by May 2027.
- **GDPR cross-border enforcement** — Regulation (EU) 2025/2518 in force since January 2026 with 15-day investigation deadlines. €1.2B in fines issued in 2025 alone.

Existing solutions do not close the gap.

Hyperscaler "data residency" provides geography, not sovereignty — US-domiciled providers remain subject to the CLOUD Act regardless of where their servers sit. Open-source AI firewalls (Pipelock, LlamaFirewall, Trylon Gateway) handle injection defence and PII redaction but do not map flows to specific regulatory tiers. There is no standard, open-source specification that tags a network payload at the egress boundary with its compliance posture.

ContextBoundary closes that gap.

---

## The Three Egress Tiers

| Tier | Egress Rule | Applies To |
|------|-------------|------------|
| **Tier I** | Never leaves the system | Sovereign data, PII, regulated records |
| **Tier II** | Anonymised aggregates only, with consent | Behavioural metrics, derived insights |
| **Tier III** | Explicit per-call API escalation | Public data, sanctioned external lookups |

Every outbound request is tagged before it crosses the boundary. The boundary enforces. The audit trail records.

---

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
│ Tier I    │ Tier II   │ Tier II   │ Tier II/III│ Tier II/III│
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

---

## The Three Axes

| Axis | What it classifies | Established |
|------|--------------------|-------------|
| **Privacy Tier** | Egress permission: Tier I (never leaves) → Tier II (anonymised, with consent) → Tier III (explicit API escalation) | Yes — codified here |
| **Geography / Jurisdiction** | Which regulatory regime governs the flow: GDPR, DPDP, RBI, HIPAA, EU AI Act, LGPD, CCPA, SOC2 | Partially — codified here as Audit Profiles |
| **Vendor Tier Responsibility** | Which vendor zone operates the workload: Customer, AMS, Product, Compute, LLM | No — ContextBoundary's specific contribution |

The intersection of all three is the complete boundary picture.

---

## Why Deployment-Agnostic Matters: The CLOUD Act Gap

Hyperscaler solutions like AWS Cross-Region Inference and Azure EU Data Boundary provide geographic residency. They do not provide legal sovereignty. US-domiciled corporations remain subject to the CLOUD Act regardless of server location.

ContextBoundary is deployment-agnostic by design. The same specification governs egress whether you run on on-prem hardware, a sovereign regional cloud, or a hyperscaler. The boundary travels with the workload, not the provider.

---

## Architectural Alignment

ContextBoundary aligns with the architectural patterns the AI security community is converging on:

- **Context Isolation over in-context guardrails.** The specification assumes the LLM cannot police its own outputs and enforces separation at the network boundary, not inside the prompt.
- **Agent-External Attestation.** Egress events are designed to be signed and audited by infrastructure outside the agent's trust boundary, not by the agent itself.
- **Rule of Two compatibility.** Tier classification maps directly to Simon Willison's framing — Tier I prevents agents from combining untrusted input, private data, and external state.

ContextBoundary is the specification. Runtime products (firewalls, gateways, proxies) implement it. An existing AI firewall becomes ContextBoundary-compliant when it tags every egress with the tier + regulation schema and emits a verifiable audit trail.

---

## Hero Artifacts in v0.1

1. **The Boundary Diagram** — the five-zone canonical view
2. **Egress Tier Classification** — Tier I / II / III definitions and routing rules
3. **Vendor Tier Responsibility Matrix** — pipeline stages mapped to vendor zones
4. **Audit Profiles** — regulation-specific overlays (v0.1 ships with GDPR)
5. **Endpoint Atlas** — registry classifying common AI vendors and services by zone
6. **ContextOps Mapping** — bridge to the organisational governance layer

---

## How to Use ContextBoundary

A CIO reading the canonical diagram against the organisation's current state lands in one of three diagnostic positions:

1. **No defined boundary.** Workloads cross zones without classification or contract review. Intervention: full boundary engagement, starting with Egress Tier classification.
2. **Boundary defined but leaking.** Some zones classified, but specific crossings are silent. Intervention: targeted audit using the relevant Audit Profile, followed by contract remediation.
3. **Boundary defined and enforced.** All zones documented, all crossings governed by named instruments, all jurisdictional constraints active. Intervention: maturity certification and ongoing drift monitoring.

All three are buyable outcomes. ContextBoundary makes the diagnosis legible.

---

## What is in v0.1

- Core framework specification ([FRAMEWORK.md](./FRAMEWORK.md))
- Architecture documentation: boundary diagram, tier classification, vendor tier matrix
- One complete Audit Profile: GDPR
- Endpoint Atlas with starter vendor entries
- ContextOps connector mapping

---

## Roadmap

v0.2 will add:

- Audit Profiles for India DPDP, RBI sectoral guidelines, HIPAA, SOC2, EU AI Act, LGPD
- Expanded Endpoint Atlas covering the top 50 enterprise AI vendors and self-hosted equivalents
- Legacy hardware substrate guidance (existing infrastructure as Tier II / III platform)
- Worked examples: SMB clinic, mid-market cooperative bank, Indian IT services firm
- Observability sink protection pattern (regional log sinks, edge redaction)

---

## The Family

ContextBoundary is one of three sibling open-source projects:

| Project | Answers |
|---------|---------|
| [**ContextOps**](https://github.com/kannanokannan/ContextOps) | How does an organisation govern its AI context? |
| **ContextBoundary** (this repo) | Where is data allowed to go? |
| [**Sthala**](https://github.com/kannanokannan/Sthala) | Where does the AI actually run? |

ContextOps governs the organisational layer. ContextBoundary governs the egress layer (deployment-agnostic). Sthala is one industry-specific, on-prem implementation that consumes ContextBoundary's contract.

---

## What ContextBoundary is NOT

- Not a SaaS product, gateway, or tool. Existing gateways implement parts of the specification.
- Not a replacement for ITIL, TOGAF, COBIT, NIST AI RMF, or any regulation. It overlays them at the egress layer.
- Not tied to any cloud provider, hardware vendor, or model family.
- Not a definition of organisational AI governance. That is ContextOps.
- Not a hardware sovereignty claim. ContextBoundary governs data flows, not silicon.
- Not legal advice. Audit Profiles describe architectural responses to regulation. Legal interpretation remains the responsibility of the adopting organisation.
- Not a guarantee of compliance. Adoption supports defensible audit posture; it does not produce it automatically.

---

## Repository Structure

```
contextboundary/
├── README.md                          # This document
├── FRAMEWORK.md                       # Core specification
├── LICENSE                            # Apache 2.0
├── CONTRIBUTING.md                    # Contribution guidelines
├── architecture/
│   ├── boundary-diagram.md            # Canonical diagram with annotations
│   ├── tier-classification.md         # Egress Tier I/II/III definitions
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

---

## Framework Disclosure

ContextBoundary is itself a framework, and its adoption is subject to the same caution every framework deserves. Implement it selectively. Apply one Audit Profile, one boundary clarification, one vendor reclassification at a time. A reference architecture applied dogmatically replaces one form of confusion with another.

Use ContextBoundary to make the boundary visible. The decisions about what to do with that visibility belong to the organisation.

---

ContextBoundary v0.1. Licensed under Apache 2.0. Feedback welcome via GitHub Issues.

---

## Part of the Stack

This project is one of three sibling open-source projects under [github.com/kannanokannan](https://github.com/kannanokannan).

| Project | Question | Repo |
|---------|----------|------|
| ContextOps | How does an org govern its AI context? | [github.com/kannanokannan/ContextOps](https://github.com/kannanokannan/ContextOps) |
| ContextBoundary | Where is data allowed to go? | [github.com/kannanokannan/ContextBoundary](https://github.com/kannanokannan/ContextBoundary) |
| Sthala | Where does the AI actually run? | [github.com/kannanokannan/Sthala](https://github.com/kannanokannan/Sthala) |

Canonical terminology and cross-project decisions: [context-stack](https://github.com/kannanokannan/context-stack)
