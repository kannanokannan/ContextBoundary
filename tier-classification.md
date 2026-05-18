# Privacy Tier Classification

ContextBoundary adopts the established Tier I/II/III privacy taxonomy from enterprise AI literature. The classification is descriptive of data sensitivity, not of regulatory category. Specific regulations map their own categories onto these tiers through Audit Profiles.

## The Three Tiers

### Tier I — Public / Non-Sensitive

**Data sensitivity profile.** Information that is publicly available, intentionally published, or otherwise without confidentiality or regulatory constraint. Generic queries, public documentation, marketing copy, unstructured external communication, public knowledge base content.

**Target infrastructure boundary.** Public cloud SaaS endpoints. Standard managed AI APIs over the public internet.

**Technical connectivity.** Direct HTTPS connections. Managed via standard API keys and rate-limited access.

**Examples (illustrative, vendor-neutral).** A generic question about a public technology. A request to summarize a publicly available document. A marketing copy generation task with no embedded customer data. A coding query that contains no proprietary code.

**Crossing rules.** Tier I data may cross to any zone in the canonical diagram. Standard logging applies. No special redaction is required.

### Tier II — Restricted / Proprietary

**Data sensitivity profile.** Internal company information, non-public business data, customer metadata without direct personal identifiers, operational logs, non-PHI healthcare administrative data, non-financial customer interactions. Information that the organization treats as confidential but that is not subject to the strictest regulatory regimes.

**Target infrastructure boundary.** Secure Virtual Private Cloud (VPC), Bring Your Own Cloud (BYOC) enclave, or dedicated tenant on a managed AI platform. Self-hosted gateways with DLP integration.

**Technical connectivity.** Site-to-site VPN, dedicated network interconnects, or managed private connectivity. Federated identity from the customer's identity provider. No shared service accounts.

**Examples (illustrative).** Internal Standard Operating Procedures (SOPs). Customer support transcripts with PII redacted. Internal financial planning documents (non-disclosed). Product roadmap documents. Anonymized usage analytics.

**Crossing rules.** Tier II data may cross to AMS, Product, Compute, and LLM Vendor zones under contractual controls (DPA, MSA, BAA where applicable). Crossings must be logged. Redaction or tokenization may be required at specific crossings depending on the applicable Audit Profile.

### Tier III — Critical / Sovereign

**Data sensitivity profile.** Highly regulated, classified, or otherwise legally constrained data. Personal Health Information (PHI) covered under HIPAA. Personally Identifiable Information (PII) covered under GDPR or DPDP special categories. Classified defense data. ITAR-restricted materials. Specific categories of financial data under RBI or other sovereign banking regulators. Trade secrets and intellectual property where exposure is an existential risk.

**Target infrastructure boundary.** On-premises physical clusters, sovereign enclaves operated entirely within customer jurisdiction, or fully air-gapped deployments. No public internet path.

**Technical connectivity.** Physical fiber connections, hardware diodes for one-way transfer, internal-only network paths. Internal Certificate Authorities. Identity providers operating entirely within the customer's network perimeter.

**Examples (illustrative).** Patient clinical notes. Identity documents and biometric data. Classified defense intelligence. Financial records subject to mandatory localization. Privileged legal communications. Source code containing trade secrets.

**Crossing rules.** Tier III data may not cross out of the Customer Sovereign Zone by default. Exceptions require either: (a) full redaction and tokenization rendering the data Tier I or Tier II equivalent before crossing, (b) deployment of the receiving zone entirely within the customer's jurisdictional perimeter (for example, a managed AI service deployed inside a customer-controlled enclave), or (c) explicit regulatory permission with documented evidence.

## Tier Assignment Responsibility

Privacy Tier classification is assigned per Context Asset by the Context Owner (in ContextOps terminology). Where ContextOps is not in use, the classification is assigned by the data owner or the responsible business unit. The assignment is metadata on the asset and is reviewed during the asset's Freshness Score refresh cycle.

Tier assignments must be defensible. The framework does not enforce a specific classification scheme — different organizations and different regulators define data categories differently. The framework requires that the assignment be explicit, recorded, and traceable. Implicit or assumed classification is the same as no classification.

## Tier Inheritance

When two pieces of data are combined into a single payload, the combined payload inherits the highest applicable Tier. A query containing both a Tier I public reference and a Tier III patient identifier is a Tier III payload until the Tier III component is removed.

Inheritance is a one-way operation. Removing the Tier III component from the payload returns the payload to its base Tier only if removal is complete and irreversible. Partial redaction that retains identifying patterns does not reduce the Tier.

## What Tier Classification Does Not Replace

Tier classification is a routing and architecture signal. It does not replace:

- Regulatory classification (PHI, PII, special category data, etc.)
- Data Loss Prevention (DLP) policy
- Encryption requirements
- Retention and deletion policy
- Cross-border transfer documentation

These remain the responsibility of the organization's existing data protection program. Tier classification overlays them at the architecture layer. The framework's contribution is making the routing implication of each regulatory classification explicit and auditable.

## Failure Mode

The primary failure mode this classification prevents is silent Tier escalation — data flowing to a zone that exceeds its Tier ceiling without the organization knowing. This typically happens when:

- A Tier III payload is bundled with Tier I traffic and routed through a shared crossing
- A Tier II asset is reclassified as Tier I after partial redaction that did not actually remove identifying patterns
- A new pipeline stage is added that processes a higher Tier than its predecessors, without re-evaluating the crossing policy

Explicit classification metadata, recorded on every Context Asset and re-verified on every crossing, is what makes silent escalation detectable.
