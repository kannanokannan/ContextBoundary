# Endpoint Atlas

The Endpoint Atlas is a vendor-neutral registry classifying common AI services, models, gateways, and infrastructure components by which Boundary Zone they operate in by default. It is the practical complement to the abstract boundary diagram — where the diagram defines zone categories, the Atlas places actual endpoints into those categories.

The Atlas is descriptive, not promotional. Inclusion of an endpoint does not constitute endorsement. Exclusion of an endpoint does not constitute disapproval.

## Why the Atlas Exists

The canonical boundary diagram defines five zones abstractly. In practice, organizations face dozens of AI vendors, each making its own claims about where data resides and which controls apply. Mapping each vendor to a zone requires reading the vendor's documentation, contract terms, and certifications — work that is typically done by each organization independently, with results that are inconsistent and often outdated.

The Endpoint Atlas externalizes this work. Each entry classifies a representative endpoint or service category and notes the conditions under which the classification might shift.

## Atlas Entry Structure

Every entry in the Atlas contains:

### Endpoint Name
The vendor-neutral service category, followed by a specific representative product where helpful for identification. For example: "Frontier LLM API (e.g., commercial chat completion service)" or "Self-hosted gateway (e.g., open-source AI gateway)."

### Default Boundary Zone
The zone in which the endpoint operates under its standard configuration. One of: Customer Sovereign, AMS / Service Vendor, Product Vendor, Compute Vendor, LLM Vendor.

### Conditional Zones
Configurations under which the endpoint operates in a different zone than the default. For example, a frontier LLM API may default to LLM Vendor but operate as Compute Vendor when accessed via a managed cloud's BYOC arrangement.

### Sovereignty Notes
Factual notes on the endpoint's residency options, encryption posture, certifications held, and cross-border data flow behavior. Notes describe capabilities, not endorsements.

### Audit Profile Compatibility
Flags indicating which Audit Profiles the endpoint is compatible with by default and which require additional configuration. The entry does not assert compliance; it describes architectural fit.

## What the Atlas is Not

- Not a procurement guide. Vendor selection involves commercial, support, and operational considerations beyond architectural fit.
- Not a compliance certification. Architectural compatibility is necessary but not sufficient for regulatory compliance.
- Not exhaustive. The Atlas covers categories and representative examples. Specific vendor products may diverge from the defaults documented here.
- Not always current. Vendor offerings change. Entries must be reviewed for currency before being relied upon for live procurement decisions.

## How to Use the Atlas

For each AI endpoint in a current or planned deployment:

1. **Locate the endpoint in the Atlas** by service category.
2. **Confirm the actual configuration matches the default**, or identify which conditional zone applies.
3. **Cross-reference the endpoint's Boundary Zone** against the Vendor Tier Responsibility Matrix to confirm the pipeline stages it operates are permitted in that zone.
4. **Apply the relevant Audit Profile** to identify any further constraints.
5. **Document the classification** as architecture metadata.

Where an endpoint in use does not appear in the Atlas, the organization classifies it independently and ideally contributes the classification back to the Atlas via PR.

## Roadmap

🚧 v0.1 ships with a starter set of approximately a dozen representative entries in [`vendors.md`](vendors.md). v0.2 will expand the Atlas to cover the top 50 enterprise AI endpoints in active enterprise use, organized by service category.

Planned expansion categories:

- Frontier LLM APIs (commercial chat completion services)
- Managed cloud AI services (hyperscaler-hosted models)
- Open-source LLMs (self-hostable model families)
- Vector databases (managed and self-hosted)
- Embedding services (managed and self-hosted)
- AI gateways and routers (commercial and open-source)
- AI observability platforms
- AI agent frameworks
- On-premises inference appliances
- Privacy-preserving compute services (TEE, confidential VMs)

## Contributing Atlas Entries

New entries follow the structure defined above. See [`../CONTRIBUTING.md`](../CONTRIBUTING.md) for voice and style requirements. Entries are descriptive and vendor-neutral; promotional language will be rewritten before merge.
