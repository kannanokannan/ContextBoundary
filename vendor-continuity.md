# Vendor Continuity

**A ContextBoundary control for model and vendor availability risk.**

Version: 0.1 (2026-07-06) · License: Apache 2.0

---

## The gap

The base framework governs *where data is permitted to go* across five Boundary Zones. It assumes each zone's vendor is available. It does not govern the case where an LLM Vendor or Compute Vendor becomes **unavailable** — suspended, export-restricted, or withdrawn — while workloads depend on it.

June 2026 made this concrete: a government export directive suspended access to two frontier models overnight, on nationality-of-provider grounds, with no notice to dependent enterprises. Most AI programs had no fallback plan, no nationality-based access classification, and no export-review step in vendor assessment. This is a Boundary Zone continuity failure, and it belongs here — not in Sthala (which places runtime) and not in ContextOps (which owns organizational policy, though it owns the *decision* to keep a fallback).

---

## Core vocabulary

**Continuity Class** — A classification of each LLM/Compute Vendor endpoint by availability risk: whether access can be withdrawn by a party other than the vendor or customer (e.g. an export regulator), and how fast. Recorded as an attribute in the Endpoint Atlas.

**Fallback Endpoint** — A pre-approved alternate endpoint for a given workload, already classified for Egress Tier, Geography, and Vendor Tier, so that a reroute does not itself create a silent crossing. A fallback that has not been boundary-classified is not a fallback.

**Suspension Event** — Any loss of access to an endpoint not initiated by the customer. Every Suspension Event is an audit event, and any reroute it triggers is a governed crossing.

---

## The control

**V1 — Continuity classification.** Every LLM and Compute Vendor endpoint in the Atlas carries a Continuity Class, including provider nationality and known export/regulatory exposure.

**V2 — Fallback pre-classification.** Any workload above a defined criticality has at least one Fallback Endpoint, pre-classified against all three axes. Reroute to an unclassified endpoint is prohibited — the same silent-crossing rule the framework already enforces.

**V3 — Suspension handling.** A Suspension Event triggers reroute to the Fallback Endpoint through a governed crossing, with an audit entry. No workload silently fails over to an unapproved zone.

---

## Mapping to the existing model

| Base concept | Continuity extension |
|--------------|----------------------|
| Endpoint Atlas | Gains Continuity Class + provider nationality attributes (V1) |
| Vendor Tier Responsibility | Extended to include vendor *availability*, not only data handling |
| Silent Sovereign Egress prohibition | Extended: no silent failover to an unclassified endpoint (V2, V3) |
| Audit Profile | Suspension Events and reroutes become required audit evidence |

## Regulatory note

Export and availability exposure is a moving target. As of mid-2026 the EU AI Act's staged obligations are themselves subject to a provisional Digital Omnibus timeline revision; treat all regulatory dates in this repo as provisional until the corresponding instrument is formally adopted. Continuity Class captures *regulatory availability risk* generally and does not depend on any one jurisdiction's calendar.

---

## What this is NOT

- Not a runtime placement or failover engine — that consumes this control; Sthala owns placement.
- Not investment, legal, or export-compliance advice. It is an architectural control for classifying and auditing availability risk.
- Not a new axis — it extends the Vendor Tier axis and the Endpoint Atlas.

---

## Changelog

- **2026-07-06** — v0.1 created. Vendor/model availability control (V1 continuity classification, V2 fallback pre-classification, V3 suspension handling) specified, prompted by the June 2026 frontier-model export suspension, and mapped to the Endpoint Atlas and Vendor Tier axis. EU AI Act timing note updated to reflect the provisional Digital Omnibus revision. Authored by Chanakya; registered by Hanuman in the public index files.
