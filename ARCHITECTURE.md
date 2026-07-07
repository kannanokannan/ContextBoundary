# ContextBoundary — Architecture Overview

## The Boundary Model

ContextBoundary models AI data movement as a five-zone egress chain. The question is not where the software runs. The question is which boundary a payload crosses, which vendor zone receives it, and which Egress Tier permits that crossing.

The framework is deployment-agnostic. The same boundary model applies across customer-operated, managed-service, cloud, hybrid, edge, and regional deployments.

---

## Where ContextBoundary Sits

ContextBoundary is the technical-layer contract between an AI system and the external services it touches.

It answers three questions at every outbound boundary:

1. **Which vendor zone receives the payload?** — Boundary Zone classification
2. **What egress permission applies?** — Egress Tier I / II / III
3. **Which obligations govern the crossing?** — Jurisdictional Audit Profile

For agent and MCP-connected systems, the same contract applies before discovery and invocation. A capability may be visible, callable, approval-required, denied, or constrained by Egress Tier and Audit Profile policy. MCP exposes or connects capabilities; ContextBoundary governs the boundary decision around visibility, invocation, approval, denial, egress, and audit.

Agent-authority enforcement extends that contract to action crossings. Identity at invocation, autonomy-tier gating, and tool supply-chain filtering are specified in [agent-authority-enforcement.md](agent-authority-enforcement.md).

Without this contract, vendor substitutions and architecture changes silently alter compliance assumptions. With it, every crossing has a declared receiving zone, a permitted Egress Tier, and an auditable obligation set.

---

## Five Boundary Zones

ContextBoundary maps AI data flows across five zones:

| Zone | Role |
|------|------|
| Customer Sovereign | Source systems, identity, redaction, audit/WORM evidence |
| AMS-Service Vendor | Integration, orchestration, managed-service operation |
| Product Vendor | Application layer, vector store, embedding, retrieval, reranking |
| Compute Vendor | Infrastructure, GPU/NPU hosting, storage custody |
| LLM Vendor | Model inference and generation |

Each vendor in the [Vendor Tier Matrix](vendor-tier-matrix.md) is placed into one of these zones. Each crossing between zones is governed by an Egress Tier, a legal instrument, and any applicable Audit Profile constraints.

Vendor-continuity controls extend this model to availability risk. LLM and Compute Vendor endpoints can carry continuity classification, fallback pre-classification, and suspension-handling evidence as described in [vendor-continuity.md](vendor-continuity.md).

---

## Egress Tier Classification

| Tier | Egress Rule | Typical Payload |
|------|-------------|-----------------|
| I | Never leaves the system | Sovereign data, PII, regulated records |
| II | Anonymised aggregates only, with consent | Behavioural metrics, derived insights |
| III | Explicit per-call API escalation | Public data, sanctioned external lookups |

Tier I is the most restrictive. Tier III is the least restrictive. Lower number means higher protection.

See [tier-classification.md](tier-classification.md) for the full routing rules, inheritance behavior, and failure modes.

---

## Audit Profiles

Audit Profiles overlay jurisdiction-specific obligations onto the five-zone model. They do not replace the underlying regulation. They translate regulatory constraints into boundary constraints, metadata requirements, and evidence requirements.

v0.1 ships GDPR as the reference Audit Profile. Additional profiles will be added in future versions as the framework matures and field validation informs scope.

---

## Relationship to ContextOps

ContextBoundary defines the technical egress contract. ContextOps governs the organisational layer around that contract.

- ContextBoundary = the specification layer: what is allowed, what is classified, what must be audited
- ContextOps = the governance layer: who owns the decision, how it is reviewed, how drift is handled

See [contextops-mapping.md](contextops-mapping.md) for the full connector spec.

---

*ContextBoundary v0.1 — Apache 2.0*
