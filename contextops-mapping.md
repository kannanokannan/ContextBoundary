# ContextOps Mapping

This document is the explicit bridge between ContextBoundary and [ContextOps](https://github.com/kannanokannan/contextops). It maps every ContextBoundary architectural primitive to its corresponding ContextOps stage, practice, or role.

Either framework can be adopted alone. Both gain operational potential when paired. This mapping is the seam.

## Why the Bridge Exists

ContextOps governs *how* organizations capture, curate, supply, and renew context for AI agents. It defines roles, practices, and a maturity model. It is intentionally vendor-neutral and intentionally silent on technical placement decisions.

ContextBoundary governs *where* AI workloads sit and *which* boundaries data crosses. It defines zones, tiers, profiles, and an endpoint registry. It is intentionally silent on organizational ownership of those decisions.

The silences are deliberate. Each framework owns one half of the problem. The bridge between them is where adoption becomes operational.

## Concept Mapping: ContextBoundary → ContextOps

| ContextBoundary concept | ContextOps stage or practice |
|---|---|
| Egress Tier classification (per Context Asset) | Curate stage; metadata on the Context Asset assigned by the Context Owner |
| Audit Profile selection (per deployment) | Curate stage decision; reviewed during the Transition Context Audit |
| Vendor Tier Responsibility Matrix application | Operationalizes the AI Amplifier Assessment for compute boundaries |
| Boundary Zone assignment | Supply stage; determines which agents can be supplied to which zones |
| Crossing Point logging (WORM) | Renew stage; feeds drift monitoring and Non-Deterministic Triage |
| Sovereign Egress events | Renew stage; triggers re-Capture when egress patterns shift |
| Endpoint Atlas reference | Curate stage; informs Context Conflict Resolution when multiple endpoints offer similar capability |
| Custody Boundary definition | Supply stage; required before any agent is granted production access |

## Concept Mapping: ContextOps → ContextBoundary

The reverse mapping shows how each ContextOps practice has a technical realization in ContextBoundary:

| ContextOps practice | ContextBoundary mechanism |
|---|---|
| Problem Statement Test | Informs which Audit Profiles apply and which Boundary Zones are appropriate for the workload type |
| Mental Model Calibration | Sets the level of architectural literacy required across stakeholders to read the boundary diagram |
| Downstream Task Tracing | Maps to Crossing Point identification — every downstream action implies a return crossing |
| Transition Context Audit | Verifies that the boundary diagram, Audit Profile selections, and Endpoint Atlas classifications are documented before Build-to-Run handover |
| Workflow-First Migration | Boundary placement decisions follow the workflow, not the tool; vendor zone selection respects workflow affordances |
| Context Freshness Scoring | Applies to Audit Profile freshness as well; regulatory guidance changes require profile review |
| AI Amplifier Assessment | Compute boundary placement is the technical input to this assessment |
| Scale Parity Check | Pilot-to-production scale shifts may change applicable Audit Profile (broader user population may trigger new jurisdictional rules) |
| Context Conflict Resolution | Resolves endpoint contradictions in the Endpoint Atlas; the canonical endpoint per capability is named |
| Non-Deterministic Triage Protocol | Crossing logs (WORM) provide the evidence needed to classify variance incidents |

## Role Mapping

ContextOps defines five roles. Each role has specific ContextBoundary responsibilities:

| ContextOps Role | ContextBoundary Responsibility |
|---|---|
| Context Architect | Owns the canonical boundary diagram for the organization; selects active Audit Profiles |
| Context Owner | Assigns Egress Tier classification to assets within the owner's domain |
| Context Curator | Maintains the Endpoint Atlas entries used by the organization; flags entries needing refresh |
| AI Onboarding Manager | Verifies boundary diagram compliance during agent onboarding; runs the AI Amplifier Assessment with boundary inputs |
| Agent Operations Lead | Monitors Crossing Point logs; triggers drift remediation when boundary violations occur |

Where ContextOps is not in use, these responsibilities still exist but are typically distributed across enterprise architecture, security, compliance, and operations teams without explicit assignment.

## Maturity Alignment

ContextOps defines a five-level maturity model. ContextBoundary alignment per level:

| Maturity Level | ContextBoundary State |
|---|---|
| Level 1 (Ad hoc) | No boundary diagram exists; crossings occur without classification or logging |
| Level 2 (Repeatable) | Informal boundary awareness; teams may apply ad-hoc tier classification to high-sensitivity assets |
| Level 3 (Defined) | Canonical boundary diagram documented; Egress Tiers assigned per asset; at least one Audit Profile applied |
| Level 4 (Managed) | Crossing logs operational in WORM storage; Endpoint Atlas maintained; drift monitored |
| Level 5 (Optimizing) | Boundary diagram and Audit Profile selections drive procurement decisions; framework metrics inform AI investment |

ContextBoundary practices align to ContextOps Level 2 and above, consistent with ContextOps' own gate rule. Below Level 2, technical boundary work without organizational discipline produces architecture documents that no one maintains.

## Spine Stage Coverage

Mapping ContextBoundary activities to the ContextOps four-stage spine:

| Spine Stage | ContextBoundary Activity |
|---|---|
| Capture | Identifying assets that cross zone boundaries; documenting current-state crossings |
| Curate | Assigning Egress Tier to assets; selecting applicable Audit Profiles; resolving Endpoint Atlas conflicts |
| Supply | Onboarding agents within their permitted zones; configuring Crossing Point governance and logging |
| Renew | Monitoring crossing logs; refreshing Audit Profiles when regulatory guidance changes; updating Endpoint Atlas entries as vendor offerings evolve |

## Joint Adoption Path

For an organization adopting both frameworks together, a recommended sequence:

1. **Establish ContextOps Level 2 baseline.** Shared vocabulary, problem definition, stakeholder alignment. Without this, technical boundary work has no organizational home.
2. **Draft the canonical boundary diagram.** Identify which zones exist in the current state. Mark crossings.
3. **Select primary Audit Profile.** Most organizations have one dominant regulatory regime. Apply it. Add additional profiles as multi-jurisdictional coverage requires.
4. **Assign Egress Tier classification** to existing Context Assets, starting with the highest-sensitivity domains.
5. **Build Crossing Point logging** to WORM storage. This is the foundation for evidence production.
6. **Onboard new agents through the boundary diagram.** No agent reaches production without explicit zone assignment, tier classification, and Audit Profile compliance.
7. **Monitor and renew.** Crossing logs feed drift detection. Audit Profile freshness is tracked. The boundary diagram is reviewed quarterly or when major regulatory changes occur.

## Independent Adoption

ContextBoundary can be adopted without ContextOps. The artifacts (boundary diagram, Audit Profiles, Endpoint Atlas) are useful documentation regardless of organizational governance maturity.

ContextOps can be adopted without ContextBoundary. The methodology operates at a layer above technical placement and is meaningful even where boundary decisions remain implicit.

Combined adoption is the operational ideal. The methodology supplies the discipline; the architecture supplies the evidence.

## Where the Bridge Breaks

A few common failure modes when both frameworks are adopted but not properly integrated:

**Audit Profile assigned at deployment, not at asset.** If the profile is selected per deployment rather than per Context Asset, multi-jurisdictional cases get under-served. A single deployment may need different profiles for different assets. The asset-level metadata model in ContextOps supports this; deployment-level-only profile selection does not.

**Boundary diagram owned by security alone.** If the Context Architect role is not engaged with the boundary diagram, the diagram becomes a security artifact rather than an architectural one. It will be technically correct and organizationally orphaned.

**Crossing logs not flowing to Renew stage.** Crossing logs are an evidence artifact for ContextBoundary and a drift signal for ContextOps. If they do not flow to the Agent Operations Lead, drift goes undetected. The integration is the bridge's primary purpose.

**Tier classification without ownership.** Egress Tier metadata on a Context Asset without an assigned Context Owner is a label without an accountable party. The Context Owner is what makes the classification defensible.

## Future Bridge Work

🚧 Planned for future versions:

- Reference implementation showing crossing-log forwarding to ContextOps drift monitoring
- Joint maturity assessment instrument combining both frameworks' Level 2-5 indicators
- Worked examples covering joint adoption in SMB clinical, mid-market financial, and Indian IT services contexts

---

ContextBoundary and ContextOps are designed to be peers. This mapping document is the contract between them. Updates to either framework should review this mapping for continued alignment.
