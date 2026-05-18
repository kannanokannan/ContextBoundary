# Endpoint Atlas — Starter Registry

A starter set of representative AI endpoint categories, classified by their default Boundary Zone. Entries are descriptive, not promotional. Specific vendor products within each category may diverge from the defaults documented here; consult vendor documentation for current state.

## Frontier LLM APIs (Commercial Chat Completion Services)

**Default Boundary Zone:** LLM Vendor

**Conditional Zones:**
- Compute Vendor — when accessed via a hyperscaler's managed deployment with BYOK, customer-region pinning, and customer-controlled data isolation (for example, a major cloud's enterprise-grade managed LLM offering)
- Compute Vendor — when the LLM provider offers dedicated single-tenant deployments with customer-controlled networking

**Sovereignty Notes:** Public API endpoints typically operate from US or global infrastructure. Standard API consumption transmits prompts and responses to the vendor's infrastructure. Some providers offer EU or other regional endpoints. Data retention policies vary; default is often 30 days for abuse monitoring, with zero-retention options available under enterprise contracts.

**Audit Profile Compatibility:**
- GDPR: Requires SCCs and TIA for non-EU endpoints. Some providers offer EU-resident endpoints with established transfer mechanisms.
- DPDP: Cross-border transfer governance applies; localization restrictions may rule out direct API consumption for specific data categories.
- HIPAA: BAA-eligible offerings exist; standard public API consumption is not.
- SOC2: Most major providers maintain SOC2 Type II reports.

---

## Managed Cloud AI Services (Hyperscaler-Hosted Models)

**Default Boundary Zone:** Compute Vendor (when running customer-selected models in a customer-controlled VPC) / LLM Vendor (when running first-party hyperscaler models)

**Conditional Zones:**
- LLM Vendor — when consuming first-party models the hyperscaler operates as a service
- Compute Vendor — when running open-source or third-party models on hyperscaler infrastructure under customer control

**Sovereignty Notes:** Hyperscaler managed AI services typically offer region selection, VPC isolation, and customer-managed encryption keys. Data residency commitments are contractual. Service-level telemetry may transit to the hyperscaler's global infrastructure independently of customer workload data.

**Audit Profile Compatibility:**
- GDPR: EU-region deployments with established DPA and SCCs framework available from major hyperscalers.
- HIPAA: BAA available from major hyperscalers for in-scope services.
- DPDP: India-region deployments available from major hyperscalers; specific service availability varies.

---

## Open-Source LLMs (Self-Hostable Model Families)

**Default Boundary Zone:** Customer Sovereign (when deployed on-premises) / Compute Vendor (when deployed on third-party infrastructure)

**Conditional Zones:**
- All zones, depending on deployment topology. The model itself does not determine the zone; the infrastructure running it does.

**Sovereignty Notes:** Self-hosted models eliminate the LLM Vendor zone for the inference stage. The trade-off is operational responsibility for model serving, scaling, and security shifts to the deploying organization. Model license terms vary; commercial use restrictions exist on some otherwise-open weights.

**Audit Profile Compatibility:**
- All profiles — compatibility depends on infrastructure, not the model. Self-hosted deployments in compliant infrastructure are typically the most regulatorily flexible configuration.

---

## Vector Databases (Managed)

**Default Boundary Zone:** Product Vendor

**Conditional Zones:**
- Customer Sovereign — when deployed self-hosted as open-source software within customer infrastructure
- Compute Vendor — when deployed in a dedicated single-tenant configuration on customer-controlled infrastructure

**Sovereignty Notes:** Managed vector databases store embeddings, which under most regulatory regimes are personal data if derived from personal data. Residency, encryption-at-rest, and customer-managed keys availability varies by provider. Embedding inversion attacks make the embeddings sensitivity-equivalent to the underlying source data for most practical purposes.

**Audit Profile Compatibility:**
- GDPR: EU-region offerings with SCCs framework available from major providers. Embeddings derived from personal data are personal data.
- HIPAA: BAA-eligible offerings exist among major providers.

---

## Vector Databases (Self-Hosted, Open Source)

**Default Boundary Zone:** Customer Sovereign (when deployed within customer infrastructure)

**Conditional Zones:**
- Product Vendor — when deployed under a managed services arrangement with an AMS partner
- Compute Vendor — when deployed on third-party infrastructure with customer-controlled software

**Sovereignty Notes:** Self-hosted open-source vector databases give the customer full control over data residency and access. Operational responsibility shifts to the customer. Common in Tier III deployments and air-gapped environments.

**Audit Profile Compatibility:** All profiles. Compatibility depends on the infrastructure hosting the database.

---

## AI Gateways (Commercial Hosted)

**Default Boundary Zone:** Product Vendor (typically) / AMS Vendor (where integrated into an AMS-managed pipeline)

**Conditional Zones:**
- Customer Sovereign — when deployed as a self-hosted instance under customer control
- Compute Vendor — when deployed in a single-tenant configuration on customer infrastructure

**Sovereignty Notes:** Hosted AI gateways act as a proxy between customer applications and downstream LLM endpoints. Prompts and responses transit the gateway by design. Logging and observability are gateway features; log retention and residency vary by provider. Streaming responses may be buffered for DLP scanning, introducing latency considerations.

**Audit Profile Compatibility:**
- GDPR: EU-region offerings or self-hosted deployments preferred for personal data routing.
- HIPAA: Some providers offer BAA; many do not by default.
- DPDP: Data residency considerations apply for personal data routing.

---

## AI Gateways (Open-Source, Self-Hosted)

**Default Boundary Zone:** Customer Sovereign

**Conditional Zones:**
- AMS Vendor — when operated by an AMS as part of managed services
- Product Vendor — when operated as a managed service by a vendor offering the open-source software

**Sovereignty Notes:** Self-hosted open-source gateways keep all prompt and response data within customer infrastructure. Operational responsibility for upgrades, patching, and scale management is the customer's. Common in regulated industries where SaaS gateway adoption is restricted.

**Audit Profile Compatibility:** All profiles. The most flexible configuration for multi-profile deployments.

---

## On-Premises Inference Appliances

**Default Boundary Zone:** Customer Sovereign

**Conditional Zones:**
- Compute Vendor — when the appliance is operated by a vendor within customer premises under a managed services arrangement
- Compute Vendor — when the appliance is hosted in a colocation facility under shared operational responsibility

**Sovereignty Notes:** On-premises inference eliminates network egress for inference workloads. Hardware lifecycle, firmware updates, and physical security are customer responsibilities. Telemetry from the appliance to the vendor for support purposes may exist; this must be reviewed and either accepted or disabled depending on profile requirements.

**Audit Profile Compatibility:** All profiles. Common in Tier III deployments, classified workloads, and air-gapped configurations.

---

## Embedding Services (API-Based)

**Default Boundary Zone:** LLM Vendor

**Conditional Zones:**
- Compute Vendor — when accessed via a managed cloud's hosted offering with customer region pinning
- Customer Sovereign — when running self-hosted embedding models

**Sovereignty Notes:** Embedding APIs transmit source text to the vendor's infrastructure to produce vector representations. The source text is subject to the same residency and retention considerations as any LLM API consumption. Embeddings derived from personal data remain personal data and inherit the same regulatory treatment as the source.

**Audit Profile Compatibility:** Same considerations as Frontier LLM APIs above. EU-region or self-hosted alternatives preferred for personal data embedding.

---

## Confidential Computing / Trusted Execution Environments

**Default Boundary Zone:** Compute Vendor (with isolation guarantees that partially substitute for Customer Sovereign Zone properties)

**Sovereignty Notes:** Trusted Execution Environments (TEEs) provide cryptographic isolation of workloads from the underlying infrastructure operator. Customer data and model weights inside a TEE are not accessible to the cloud provider's infrastructure operators. TEE attestation provides cryptographic evidence of the configuration. TEEs do not eliminate the underlying jurisdictional considerations; they reduce the trust scope for certain controls.

**Audit Profile Compatibility:**
- GDPR: TEE deployment in non-adequacy jurisdictions can be a supplementary measure supporting transfer mechanisms but does not by itself substitute for adequacy or SCCs.
- HIPAA: TEE deployments may satisfy specific encryption-in-use considerations.
- High-sensitivity workloads: TEEs are increasingly considered for cross-border processing with reduced trust scope.

---

🚧 This is a starter set. The v0.2 roadmap expands the Atlas to cover approximately fifty representative endpoints across these categories and adds new categories including agent frameworks, observability platforms, and synthetic data services.

Contributions of new entries follow the structure defined in the [Atlas README](README.md) and the voice requirements in [CONTRIBUTING.md](../CONTRIBUTING.md).
