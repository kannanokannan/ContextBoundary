# Egress Tier Classification

ContextBoundary defines three egress tiers. The classification governs where data is permitted to flow, not merely how sensitive it is. Tier I is the most restrictive (data never leaves the system). Tier III is the least restrictive (data may egress via explicit per-call API escalation). All tiers are deployment-agnostic.

Specific regulations map their own categories onto these tiers through Audit Profiles.

---

## The Three Tiers

### Tier I — Never Leaves the System

**Egress rule.** Data classified at Tier I does not cross any boundary out of the Customer Sovereign Zone under any default condition. No API call, no external inference endpoint, no external logging pipeline receives Tier I data in its raw or identifying form.

**Data profile.** Highly regulated, classified, or legally constrained data. Personally Identifiable Information (PII) covered under GDPR, DPDP, or LGPD special categories. Personal Health Information (PHI) covered under HIPAA. Classified defence data. ITAR-restricted materials. Specific categories of financial data under RBI or other sovereign banking regulators. Trade secrets and intellectual property where exposure is an existential risk. Biometric data. Identity documents.

**Target infrastructure boundary.** On-premises physical clusters, sovereign enclaves operated entirely within customer jurisdiction, or fully air-gapped deployments. No public internet path.

**Technical connectivity.** Physical fibre connections, hardware diodes for one-way transfer, internal-only network paths. Internal Certificate Authorities. Identity providers operating entirely within the customer's network perimeter.

**Examples (illustrative, vendor-neutral).** Patient clinical notes. Identity documents and biometric records. Classified defence intelligence. Financial records subject to mandatory localisation. Privileged legal communications. Source code containing trade secrets.

**Crossing rules.** Tier I data may not cross out of the Customer Sovereign Zone by default. Exceptions require one of:
- Full redaction and tokenisation rendering the payload Tier II or Tier III equivalent before crossing
- Deployment of the receiving zone entirely within the customer's jurisdictional perimeter
- Explicit regulatory permission with documented evidence and audit trail

---

### Tier II — Anonymised Aggregates Only, With Consent

**Egress rule.** Data classified at Tier II may cross zone boundaries only in anonymised or aggregated form, and only with documented consent or contractual basis. No direct personal identifiers. No data that can be re-identified by combining fields available to the receiving zone.

**Data profile.** Internal company information, non-public business data, customer metadata without direct personal identifiers, operational logs, anonymised usage analytics, non-PHI healthcare administrative data, non-financial customer interactions. Information treated as confidential but not subject to the strictest regulatory regimes.

**Target infrastructure boundary.** Secure Virtual Private Cloud (VPC), Bring Your Own Cloud (BYOC) enclave, or dedicated tenant on a managed AI platform. Self-hosted gateways with DLP integration.

**Technical connectivity.** Site-to-site VPN, dedicated network interconnects, or managed private connectivity. Federated identity from the customer's identity provider. No shared service accounts.

**Examples (illustrative).** Internal Standard Operating Procedures (SOPs). Customer support transcripts with PII redacted. Anonymised usage analytics. Internal financial planning documents (non-disclosed). Product roadmap documents.

**Crossing rules.** Tier II data may cross to AMS, Product, Compute, and LLM Vendor zones under contractual controls (DPA, MSA, BAA where applicable). Crossings must be logged. Redaction or tokenisation required at crossings as defined by the applicable Audit Profile. Re-identification risk must be assessed before any cross-zone transfer.

---

### Tier III — Explicit Per-Call API Escalation

**Egress rule.** Data classified at Tier III may egress to external zones but only via an explicit, per-call escalation. Each outbound request must carry a declared intent, a named receiving endpoint, and a log entry. No implicit or batch egress.

**Data profile.** Information that is publicly available, intentionally published, or without confidentiality or regulatory constraint. Generic queries, public documentation, marketing copy, unstructured external communication, public knowledge base content. Data where the organisation has determined that external processing carries no regulatory or reputational risk.

**Target infrastructure boundary.** Public cloud SaaS endpoints. Standard managed AI APIs over the public internet.

**Technical connectivity.** Direct HTTPS connections. Managed via standard API keys and rate-limited access.

**Examples (illustrative).** A generic question about a public technology. A request to summarise a publicly available document. A marketing copy generation task with no embedded customer data. A coding query containing no proprietary code.

**Crossing rules.** Tier III data may cross to any zone in the canonical diagram. Each crossing must be declared explicitly at call time — no implicit or inherited permission. Standard logging applies. No special redaction required, but the per-call declaration and log entry are mandatory.

---

## Tier Assignment Responsibility

Egress Tier classification is assigned per Context Asset by the Context Owner (in ContextOps terminology). Where ContextOps is not in use, the classification is assigned by the data owner or the responsible business unit. The assignment is metadata on the asset and is reviewed during the asset's Freshness Score refresh cycle.

Tier assignments must be defensible. The framework does not enforce a specific classification scheme — different organisations and different regulators define data categories differently. The framework requires that the assignment be explicit, recorded, and traceable. Implicit or assumed classification is the same as no classification.

---

## Tier Inheritance

When two pieces of data are combined into a single payload, the combined payload inherits the most restrictive applicable Tier. A payload containing both a Tier III public reference and a Tier I patient identifier is a Tier I payload until the Tier I component is removed.

Inheritance is a one-way operation. Removing a Tier I component from a payload returns it to its base Tier only if removal is complete and irreversible. Partial redaction that retains identifying patterns does not reduce the Tier.

---

## What Tier Classification Does Not Replace

Egress Tier classification is a routing and architecture signal. It does not replace:

- Regulatory classification (PHI, PII, special category data, etc.)
- Data Loss Prevention (DLP) policy
- Encryption requirements
- Retention and deletion policy
- Cross-border transfer documentation

These remain the responsibility of the organisation's existing data protection programme. Tier classification overlays them at the architecture layer. The framework's contribution is making the routing implication of each regulatory classification explicit and auditable.

---

## Failure Mode

The primary failure mode this classification prevents is silent Tier escalation — data flowing to a zone that exceeds its Tier ceiling without the organisation knowing. This typically happens when:

- A Tier I payload is bundled with Tier III traffic and routed through a shared crossing
- A Tier I asset is reclassified as Tier II after partial redaction that did not actually remove identifying patterns
- A new pipeline stage is added that processes a more restrictive Tier than its predecessors, without re-evaluating the crossing policy

Explicit classification metadata, recorded on every Context Asset and re-verified on every crossing, is what makes silent escalation detectable.
