# Why ContextBoundary Exists

**The problem is not that organisations are using AI. The problem is that they do not know where their data goes when they do.**

---

## The Sovereignty Gap

Most enterprise AI deployments in 2026 share a common architecture: data flows from the organisation into a managed LLM API, inference happens at a vendor's infrastructure, and results return. The organisation retains the output. The vendor retains — for varying durations, under varying contractual terms — everything that crossed the boundary.

This is not a vendor trust problem. It is a boundary visibility problem. Organisations cannot govern what they cannot see, and most have no systematic account of what crosses their AI boundary, under what conditions, or against which regulatory regime.

Hyperscaler solutions offer a partial answer. AWS Cross-Region Inference, Azure EU Data Boundary, and Google Sovereign Cloud each provide geographic data residency — a guarantee that data at rest sits within a defined geography. What they cannot provide is legal data sovereignty. US-domiciled corporations are subject to the CLOUD Act regardless of where their servers sit. A subpoena issued under US federal authority travels to the data, not the data center.

For organisations subject to GDPR, India's DPDP Act, RBI sectoral guidelines, or the EU AI Act, geographic residency is necessary but not sufficient. The question is not just where the data is stored. It is who holds ultimate legal authority over it.

ContextBoundary was built to answer that question — systematically, at the architecture layer, before any data crosses any boundary.

---

## Three Forces Converging in 2026

**Regulatory hardening.** GDPR enforcement has moved from guidance to prosecution. In 2025 alone, European regulators issued €1.2 billion in GDPR fines — a 22% year-over-year increase. Regulation (EU) 2025/2518, in force since January 2026, streamlines cross-border enforcement with mandatory 15-day investigation deadlines across all Member State Data Protection Authorities. India's DPDP Act operationalised its Data Protection Board in November 2025. The EU AI Act's comprehensive obligations for High-Risk AI systems take effect August 2, 2026, with penalties up to €35 million or 7% of global annual turnover.

**Vendor chain complexity.** Enterprise AI does not flow directly from organisation to LLM. It passes through service vendors (AMS providers, system integrators), product vendors (AI platform companies, vertical SaaS), and compute vendors before reaching the model. Each tier sees different data. Each tier holds different contractual accountability. No standard framework named this chain explicitly or defined where accountability transfers at each crossing.

**Legacy infrastructure reality.** Most mid-market enterprises and a large share of regulated organisations operate on existing infrastructure investments. Reference architectures that assume new GPU clusters and new cloud regions do not address the question of how to govern AI workloads running on what already exists.

ContextBoundary addresses all three with a single canonical diagram and a small set of supporting artefacts.

---

## What the Gap Actually Looks Like

There is no standard, open-source specification that dynamically tags a network payload at the egress boundary with its specific compliance posture. Enterprise architects are forced to build complex custom middleware to route traffic based on overlapping and sometimes contradictory international laws.

Existing open-source AI firewalls (Pipelock, LlamaFirewall, Trylon Gateway, WonderwallAi) handle injection defence and PII redaction at the application layer. They do not map flows to specific regulatory jurisdictions. They do not produce an auditable record of which crossing was made under which regulatory basis. They are point defences, not boundary specifications.

ContextBoundary is not a replacement for these tools. It is the specification layer that tells those tools what the boundary is, what data is permitted to cross it, and what must be recorded when it does.

---

## Design Decisions

**Deployment-agnostic by necessity.** The egress boundary is a property of the data and the regulatory regime, not of the infrastructure. An organisation running AI on-prem, on a sovereign regional cloud, or on a hyperscaler all face the same question: what is permitted to leave, and under what conditions? The specification must work regardless of deployment model. Sthala is one reference implementation that consumes ContextBoundary's egress contract. It is not the reason the specification exists.

**Egress-centric tier numbering.** Tiers are numbered by restriction, not by data sensitivity category. Tier I is the most restrictive: data never leaves the system. Tier III is the least restrictive: data may egress via explicit per-call API escalation. This numbering is intentional — lower number, higher protection — and consistent with the principle that protective boundaries are the baseline, not the exception.

**Specification over implementation.** ContextBoundary defines what must be true at the boundary. Runtime enforcement is the responsibility of the infrastructure — firewalls, gateways, proxies, and audit pipelines that implement the specification. An existing AI gateway becomes ContextBoundary-compliant when it tags every egress with the tier and regulatory profile, enforces the crossing rules, and emits a verifiable audit trail.

**Regulatory profiles as overlays.** Different regulations define data categories differently. GDPR's special category data, DPDP's sensitive personal data, and HIPAA's PHI are not identical sets. The framework does not attempt to unify these definitions. Instead, Audit Profiles overlay each regulation's specific requirements onto the five-zone canonical diagram. The intersection of the privacy tier, the jurisdiction, and the vendor zone is the complete picture. v0.1 ships the GDPR Audit Profile. v0.2 adds DPDP, RBI, HIPAA, SOC2, and EU AI Act.

**Observability as a boundary concern.** Centralised logging pipelines are a common, systematically ignored data residency violation. Model outputs and tool-call traces streamed to a global storage bucket constitute cross-border data transfer even when the inference itself is region-pinned. ContextBoundary treats the observability pipeline as a boundary concern, not an engineering afterthought. Regional log sinks and edge prompt redaction are v0.2 targets.

---

## What ContextBoundary Does Not Claim

ContextBoundary does not claim to solve the hardware sovereignty problem. For most organisations, true hardware sovereignty — control over silicon, firmware, and physical custody — is not achievable in the near term. The framework governs data flows, not chip supply chains.

ContextBoundary does not claim to guarantee compliance. Adopting the framework supports a defensible audit posture. It does not certify any deployment or substitute for legal counsel, DPA obligations, or regulatory engagement.

ContextBoundary does not claim that any AI system is safe because it implements the specification. The specification governs where data goes. What happens to data inside an LLM during inference is a separate, active area of research.

---

## The Framework Family

ContextBoundary answers one question in a family of three:

| Project | Question |
|---------|----------|
| [ContextOps](https://github.com/kannanokannan/ContextOps) | How does an organisation govern its AI context? |
| ContextBoundary | Where is data allowed to go? |
| [Sthala](https://github.com/kannanokannan/Sthala) | Where does the AI actually run? |

The three frameworks are designed to be adopted together but can be adopted independently. ContextBoundary reaches full operational potential when paired with ContextOps' role taxonomy and Context Inventory practices, and when the organisation has answered the infrastructure question that Sthala addresses.

---

*ContextBoundary v0.1. Apache 2.0. Feedback via GitHub Issues.*
