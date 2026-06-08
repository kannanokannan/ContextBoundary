# CLAUDE.md — ContextBoundary Agent Briefing

This file briefs any Claude agent working on the ContextBoundary project.
Read this before touching any file in this repo.

---

## Canonical Reference

Before introducing any new term: check https://github.com/kannanokannan/context-stack/blob/main/GLOSSARY.md

Before making any cross-project decision: check https://github.com/kannanokannan/context-stack/blob/main/DECISIONS.md

Terminology defined in GLOSSARY.md overrides any local usage in this repo.

---

## What This Project Is

**ContextBoundary** is an open-source governing specification that defines where AI-processed data is permitted to flow. It is not a tool, not a SaaS product, not a runtime enforcer. It is a specification. Runtime products (firewalls, gateways, proxies) implement it.

- **License:** Apache 2.0
- **Repo:** https://github.com/kannanokannan/ContextBoundary
- **Owner:** kannanokannan
- **Status:** Active. v0.1 published.
- **Current version:** 0.1 (not Draft — never add "Draft" to the version label)

---

## The One-Line Answer

> ContextBoundary answers: **where is data allowed to go?**

---

## The Framework Family

ContextBoundary is one of three sibling open-source projects. Do not conflate them.

| Project | Answers | Repo |
|---------|---------|------|
| **ContextOps** | How does an org govern its AI context? | https://github.com/kannanokannan/ContextOps |
| **ContextBoundary** | Where is data allowed to go? | https://github.com/kannanokannan/ContextBoundary |
| **Sthala** | Where does the AI actually run? | https://github.com/kannanokannan/Sthala |

**Key relationship rules:**
- ContextBoundary is the **horizontal governing layer** — deployment-agnostic
- Sthala is **one vertical implementation** that consumes ContextBoundary's egress contract
- ContextOps governs the organisational layer; ContextBoundary governs the egress layer
- All three are Apache 2.0, enterprise-architecture-grade docs
- Do not create `CapabilityOps`; capability governance is split between ContextOps accountability and ContextBoundary enforcement

---

## Scope (Locked)

ContextBoundary is **deployment-agnostic**. It governs egress from any AI deployment — on-prem, cloud, hybrid. It does not care where the AI runs. It cares where data is allowed to go and under what conditions.

Do NOT anchor ContextBoundary language to:
- "local AI" or "on-prem AI" — too narrow
- Sthala specifically — Sthala is a consumer, not the scope
- Any specific cloud provider or hardware vendor

---

## The Core Model

### Five-Zone Canonical Diagram

```
┌───────────┬───────────┬───────────┬───────────┬───────────┐
│ CUSTOMER  │  AMS /    │ PRODUCT   │ COMPUTE   │   LLM     │
│ SOVEREIGN │  SERVICE  │ VENDOR    │ VENDOR    │   VENDOR  │
│  ZONE     │  VENDOR   │           │           │           │
├───────────┼───────────┼───────────┼───────────┼───────────┤
│ Tier I    │ Tier II   │ Tier II   │ Tier II/III│ Tier II/III│
└───────────┴───────────┴───────────┴───────────┴───────────┘
```

### Three Egress Tiers (CANONICAL — DO NOT INVERT)

| Tier | Rule | Applies To |
|------|------|------------|
| **Tier I** | Never leaves the system | Sovereign data, PII, regulated records |
| **Tier II** | Anonymised aggregates only, with consent | Behavioural metrics, derived insights |
| **Tier III** | Explicit per-call API escalation | Public data, sanctioned external lookups |

**Critical:** Lower number = higher protection. Tier I is most restrictive. Tier III is least restrictive. Do not flip this.

---

## Regulatory Coverage

v0.1 ships with GDPR Audit Profile.
v0.2 roadmap: DPDP Act, RBI sectoral guidelines, HIPAA, SOC2, EU AI Act, LGPD.

---

## Current Repo Structure

```
contextboundary/
├── README.md
├── FRAMEWORK.md
├── RATIONALE.md
├── CLAUDE.md
├── LICENSE
├── CONTRIBUTING.md
├── llms.txt
├── llms-full.txt
├── tier-classification.md
├── boundary-diagram.md
├── vendor-tier-matrix.md
├── gdpr.md
├── vendors.md
└── contextops-mapping.md
```

---

## Agent and MCP Boundary Rules

MCP is an integration layer, not a governance or authority layer.

Discovery is a boundary concern. For agents, seeing a capability is already partial access to that capability.

ContextOps owns:
- ownership
- roles
- trust classification
- lifecycle
- governance metadata
- context source accountability

ContextBoundary owns:
- discovery control
- capability visibility
- MCP filtering
- invocation control
- approval workflows
- egress control
- auditing

Use this positioning carefully: ContextBoundary is a policy boundary for AI agents. Do not make it sound like a runtime product.

---

## Language Rules

**Use:**
- "egress governance specification"
- "deployment-agnostic"
- "enforceable at the network layer"
- "egress tier" (not "privacy tier")
- "Tier I / Tier II / Tier III" (Roman numerals, capitalised)

**Do not use:**
- "local AI" or "on-prem AI" as scope anchors
- "Sovereign AI"
- "Draft" as a version qualifier
- "privacy tier" as the primary label

---

## What ContextBoundary Is NOT

- Not a SaaS product, gateway, or tool
- Not a replacement for ITIL, TOGAF, COBIT, NIST AI RMF, or any regulation
- Not tied to any cloud provider, hardware vendor, or model family
- Not a definition of organisational AI governance (that is ContextOps)
- Not a hardware sovereignty claim
- Not legal advice
- Not Sthala — do not conflate

---

## Commit Style

Single-topic commits. Descriptive message with bullet breakdown.

---

*ContextBoundary v0.1 · Apache 2.0*
