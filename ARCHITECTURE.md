# ContextBoundary — Architecture Overview

## The Three-Layer Model

ContextBoundary operates within a three-layer ownership stack.
Each layer has a distinct owner, a distinct responsibility, and a defined boundary.

```
┌─────────────────────────────────────────────────────────┐
│            Layer 3 — Client Solution Overlay             │
│          Per-account configuration. Client-owned.        │
│                                                          │
│  Business rules  │  Configuration scope  │  Output prefs │
└─────────────────────────────────────────────────────────┘
                          depends on ↓
┌─────────────────────────────────────────────────────────┐
│              Layer 2 — Domain Knowledge                  │
│        Proprietary methodology. Framework-owned.         │
│                   This is the moat.                      │
│                                                          │
│   Agent logic   │   Knowledge library   │  Evaluation fw │
└─────────────────────────────────────────────────────────┘
                          depends on ↓
┌─────────────────────────────────────────────────────────┐
│                 Layer 1 — Vendor LLM                     │
│            Rented capability. Substitutable.             │
│                                                          │
│  LLM client adapter  │  Model & API  │  Cost / tokens   │
└─────────────────────────────────────────────────────────┘
```

---

## Where ContextBoundary Sits

ContextBoundary is the **contract at the Layer 1 / Layer 2 boundary.**

It answers three questions at that boundary:

1. **What can the vendor touch?** — Zone classification
2. **What tier of data crosses the line?** — Privacy Tier I / II / III
3. **What are the obligations when it does?** — Jurisdictional Audit Profile (GDPR, DPDP, HIPAA...)

Without this contract, swapping a vendor at Layer 1 breaks assumptions in Layer 2.
With it, Layer 1 is genuinely substitutable — and Layer 2 is genuinely protected.

---

## Layer Ownership Summary

| Layer | Name                    | Owned By  | Substitutable?        |
|-------|-------------------------|-----------|-----------------------|
| 3     | Client Solution Overlay | Client    | Per account           |
| 2     | Domain Knowledge        | Framework | No — this is the moat |
| 1     | Vendor LLM              | Vendor    | Yes — by design       |

---

## Five-Zone Boundary Model

ContextBoundary maps all vendor interactions across five zones:

| Zone | Description                                          |
|------|------------------------------------------------------|
| Z1   | Public inference — no PII                            |
| Z2   | Authenticated inference — session-bound              |
| Z3   | Enriched context — structured personal data          |
| Z4   | Regulated data — compliance-scoped                   |
| Z5   | Restricted / sovereign — jurisdiction-locked         |

Each vendor in the [Vendor Tier Matrix](vendor-tier-matrix.md) is placed into a zone.
Each zone carries a Privacy Tier assignment.

---

## Privacy Tier Classification

| Tier | Risk Level | Typical Data                          |
|------|------------|---------------------------------------|
| I    | Low        | Anonymised, aggregated, public        |
| II   | Medium     | Pseudonymised, session-scoped         |
| III  | High       | Identified, regulated, cross-border   |

---

## Relationship to ContextOps

ContextBoundary defines the boundary.
ContextOps enforces it operationally.

- ContextBoundary = the **specification layer** (what is allowed, what is classified, what must be audited)
- ContextOps = the **execution layer** (how agents behave within those boundaries at runtime)

See [contextops-mapping.md](contextops-mapping.md) for the full connector spec.

---

*ContextBoundary v0.1 — Apache 2.0*
