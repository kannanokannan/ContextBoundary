# Audit Profiles

An Audit Profile is a regulation-specific overlay on the canonical ContextBoundary architecture. Each profile constrains specific Boundary Zones, specific Crossing Points, and specific cells in the Vendor Tier Responsibility Matrix based on the regulation's requirements.

Audit Profiles do not introduce new architectural primitives. They modify existing ones.

## Why Audit Profiles Exist

A single deployment may answer to multiple regulators. A European bank operating in India is bound by GDPR, DPDP, and RBI guidelines simultaneously. A US healthcare provider with international operations is bound by HIPAA, GDPR, and state-level legislation. Each regulator examines different parts of the boundary diagram and requires different evidence.

Without Audit Profiles, regulatory mapping is implicit. Security architects translate regulatory text into routing decisions on a per-deployment basis. The translation is rarely documented, rarely reviewed, and rarely transferable between teams. Audit Profiles externalize the translation as reusable artifacts.

## Profile Structure

Every Audit Profile contains the same sections, in the same order:

### 1. Regulation Summary
A short, factual restatement of what the regulation covers, who it applies to, and what triggers its application. This section is descriptive, not interpretive. Where the regulation is ambiguous, the profile states the ambiguity rather than resolving it.

### 2. Mandatory Boundary Constraints
Which zones are restricted under this regulation, which crossings are prohibited, and under what conditions exceptions are permitted. Constraints are stated as modifications to the canonical boundary diagram.

### 3. Privacy Tier Mapping
How the regulation's data categories map to Tier I, II, and III. Where the regulation defines its own categories (PHI, special categories, financial records, sensitive personal data, etc.), the mapping is explicit. Where a regulation's category does not align cleanly with a Tier, the profile states the misalignment and the conservative default.

### 4. Required Asset Metadata
What every Context Asset processed under this profile must record. Typical fields include lawful basis, controller/processor designation, retention period, cross-border transfer mechanism, and consent record where applicable.

### 5. Evidence Requirements
What artifacts the regulator may demand during audit. DPIAs, ROPAs, transfer impact assessments, audit logs, breach notification records, attestation reports. Each artifact is named with its regulatory source.

### 6. Conflict with Other Profiles
Explicit notes where this profile conflicts with another profile that may apply to the same deployment. Multi-jurisdictional deployments combine profiles, and conflicts must be surfaced rather than resolved silently.

### 7. Worked Application
A short walk-through of how the profile applies to a representative workload — what changes in the boundary diagram, what gets logged, what requires contract revision.

## What a Profile is Not

- Not legal advice. Audit Profiles describe architectural responses to regulation. Legal interpretation remains with the adopting organization and its counsel.
- Not a compliance certification. Following a profile supports defensible audit posture; it does not constitute compliance by itself.
- Not exhaustive. A profile names the most common architectural constraints under a regulation. Edge cases and recent guidance changes may require profile updates.

## Available Profiles

### v0.1
- [GDPR (EU)](gdpr.md) — General Data Protection Regulation, the reference profile for v0.1

### Roadmap (v0.2)

🚧 The following profiles are planned for v0.2:

- India DPDP — Digital Personal Data Protection Act, 2023
- India RBI — Reserve Bank of India sectoral guidelines for financial data
- US HIPAA — Health Insurance Portability and Accountability Act
- US SOC2 — Service Organization Control 2 Type II
- EU AI Act — Regulation on Artificial Intelligence
- ISO 27001 — Information Security Management System (overlay)

## Multi-Profile Adoption

Most enterprise deployments require more than one profile. To adopt multiple profiles simultaneously:

1. **Identify all applicable profiles** based on the data categories processed and the jurisdictions involved.
2. **Apply each profile's constraints to the canonical diagram.** Constraints accumulate; the final permitted boundary is the intersection of what every profile allows.
3. **Surface conflicts.** Where two profiles require incompatible behavior on the same cell, the conflict must be documented and escalated. Silent resolution is the failure mode.
4. **Document the combined profile.** The final operational boundary is often more restrictive than any single profile would require.

## Contributing a Profile

New profiles must follow the section structure above. See [`../CONTRIBUTING.md`](../CONTRIBUTING.md) for voice and style requirements. Open a GitHub Issue describing the proposed profile before submitting a PR.
