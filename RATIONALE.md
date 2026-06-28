# ContextBoundary Rationale

This document explains the design decisions behind ContextBoundary. It is intended for practitioners who want to understand why the framework is structured as it is, not just what it contains.

---

## Why a separate specification

AI data egress is treated as an infrastructure concern in most enterprise deployments. Network policies, cloud region settings, and vendor whitepapers handle individual products. No vendor-neutral specification maps data sensitivity, jurisdictional regulation, and vendor chain responsibility into a single auditable framework.

ContextBoundary fills that gap. It is not a product. It is a specification that sits above existing infrastructure and overlays existing governance frameworks.

---

## Why five zones

The five-zone model reflects the actual vendor chain in enterprise AI deployments. Data does not flow directly from customer to LLM. It passes through service vendors (AMS providers, system integrators) and product vendors (AI platform companies, vertical SaaS) before reaching the model.

Each zone sees different data. Each zone holds different liabilities. Each crossing is governed by a different legal instrument. Naming the zones explicitly makes the chain legible to architects, procurement teams, legal counsel, and regulators.

A three-zone model (customer, cloud, model) loses the AMS/service vendor distinction that matters most in regulated enterprise delivery. A six-zone model adds complexity without adding clarity.

---

## Why three Egress Tiers

Tier I / II / III maps to three genuinely different governance postures:

- Tier I data must never leave. No contract, no consent, no business case overrides this.
- Tier II data may leave only as anonymised aggregates with explicit consent. The transformation (anonymisation) is the control.
- Tier III data may leave through explicit per-call escalation. The approval mechanism is the control.

More tiers add precision but reduce adoption. Fewer tiers lose meaningful distinctions. Three tiers cover the full range of enterprise AI data postures without requiring practitioners to memorise a complex taxonomy.

The numbering is egress-centric: lower number = higher protection. This is locked in `context-stack/DECISIONS.md` and must not be inverted.

---

## Why deployment-agnostic

ContextBoundary governs data flows, not hardware. Whether the AI runs in a hyperscaler region, a colocation facility, or on-site infrastructure, the same boundary zones and egress tiers apply.

Anchoring the framework to a specific deployment model (cloud-only, on-prem-only) would exclude the majority of enterprise deployments, which are hybrid. It would also conflate the egress governance question (where may data go?) with the runtime placement question (where does the AI run?), which belongs to Sthala.

---

## Why Audit Profiles as overlays

Different regulations define data categories differently. GDPR special category data, DPDP sensitive personal data, and HIPAA PHI are not identical sets. The framework does not attempt to unify these definitions.

Instead, Audit Profiles overlay each regulation's specific requirements onto the five-zone canonical diagram. The intersection of the Egress Tier, the jurisdiction, and the vendor zone is the complete picture. v0.1 ships the GDPR Audit Profile as the reference implementation.

---

## Why observability is a boundary concern

Centralised logging pipelines are a common, systematically ignored data residency issue. Model outputs and tool-call traces streamed to a global storage bucket constitute cross-border data transfer even when the inference itself is region-pinned.

ContextBoundary treats the observability pipeline as a boundary concern, not an engineering afterthought. Regional log sinks and edge prompt redaction belong in the boundary model, not in a separate operations runbook.

---

## Why not a SaaS product

Existing gateways and routers implement parts of this specification. A SaaS product would compete with those implementations rather than enabling them. ContextBoundary is a specification: adopters implement it using the infrastructure they already operate.

---

## Relationship to ContextOps

ContextOps governs the organisational layer: what context an AI agent may use, who owns it, how fresh it must be. ContextBoundary governs the technical layer: where that context is permitted to flow once the AI system starts processing it.

The two frameworks are designed to be adopted together but can be adopted independently. ContextBoundary reaches full operational potential when paired with ContextOps role taxonomy and Context Inventory practices.

---

## Relationship to Sthala

Sthala is a runtime placement reference that consumes ContextBoundary's egress contract. Sthala decides where the AI runs. ContextBoundary decides what data is permitted to leave. They are complementary, not overlapping.

Do not conflate runtime placement decisions (Sthala) with egress governance decisions (ContextBoundary).
