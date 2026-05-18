# The Canonical Boundary Diagram

The boundary diagram is ContextBoundary's hero artifact. A single visual that answers, in seconds, where organizational accountability ends and the vendor ecosystem begins.

## The Diagram

```
┌───────────┬───────────┬───────────┬───────────┬───────────┐
│ CUSTOMER  │  AMS /    │ PRODUCT   │ COMPUTE   │   LLM     │
│ SOVEREIGN │  SERVICE  │ VENDOR    │ VENDOR    │   VENDOR  │
│  ZONE     │  VENDOR   │           │           │           │
├───────────┼───────────┼───────────┼───────────┼───────────┤
│ Capture   │ Ingestion │ Vector DB │ GPU/NPU   │ Inference │
│ PII redact│ ETL       │ Embedding │ hosting   │ Generation│
│ Audit/WORM│ Orchestr. │ Reranking │ Storage   │           │
│ Identity  │ Monitoring│ App layer │ custody   │           │
├───────────┼───────────┼───────────┼───────────┼───────────┤
│ Tier III  │ Tier II   │ Tier II   │ Tier I/II │ Tier I/II │
├───────────┼───────────┼───────────┼───────────┼───────────┤
│ Customer  │ MSA + DPA │ License + │ Cloud     │ BAA/DPA + │
│accountable│           │ DPA       │ agreement │ contract  │
└───────────┴───────────┴───────────┴───────────┴───────────┘
   ▲                                                   ▲
   │                                                   │
GDPR / DPDP / RBI                            BAA, SCC, DPA
jurisdiction-bound                            contract-bound
```

## How to Read the Diagram

The diagram is read left to right. Data originates in the Customer Sovereign Zone and flows rightward through the vendor ecosystem to reach the LLM Vendor. Response data flows back leftward. Every crossing between zones is a Crossing Point, and every Crossing Point is governed by an explicit legal instrument and an explicit Privacy Tier ceiling.

The rows of the diagram answer four questions for each zone:

- **Row 1 (zone name)** — Who operates this part of the pipeline.
- **Row 2 (pipeline stages)** — Which AI pipeline activities happen here by default.
- **Row 3 (Privacy Tier ceiling)** — The maximum Privacy Tier of data this zone may process.
- **Row 4 (governing instrument)** — The legal contract type that defines the boundary between this zone and the adjacent ones.

The two arrows below the diagram mark the regulatory perimeter. Jurisdictional regulation (GDPR, DPDP, RBI) binds the Customer Sovereign Zone and constrains all egress. Contract instruments (BAA, SCC, DPA) bind the relationships with the rightmost vendor zones.

## Zone Definitions

### Customer Sovereign Zone

The zone the adopting organization owns and operates directly. This zone is the root of accountability — the organization is legally responsible for everything that originates here, regardless of where the data eventually flows.

Default pipeline stages: data capture from source systems, PII and PHI detection and redaction, identity and access control (federated identity provider integration), policy and routing decisions, audit log writing to WORM storage, output reconciliation and de-tokenization.

Default Privacy Tier ceiling: Tier III. The Customer Sovereign Zone is the only zone that may process Tier III data by default.

Governing instrument: customer accountability. No contract governs the boundary of this zone from the outside, because there is no outside party operating within it. Internal accountability is governed by the organization's own policies, ContextOps role assignments, and internal audit.

### AMS / Service Vendor Zone

The zone operated by a Managed Application Services provider, system integrator, or services partner. In enterprise AI delivery, this zone typically owns ingestion pipelines, ETL, orchestration logic, monitoring, and operational support.

Default pipeline stages: data ingestion from customer source systems, ETL transformations, prompt and response orchestration, monitoring and alerting, operational dashboards.

Default Privacy Tier ceiling: Tier II. AMS vendors may process restricted data under contractual controls but should not by default see Tier III data. Where Tier III data must transit through the AMS zone, it must be redacted, tokenized, or encrypted such that the AMS provider cannot reconstruct it.

Governing instrument: Master Service Agreement (MSA) plus a Data Processing Agreement (DPA). The DPA defines the AMS provider's processor obligations under GDPR, DPDP, or other applicable regulation.

### Product Vendor Zone

The zone operated by a product vendor providing a packaged AI capability — vector database, embedding service, reranking engine, application framework, AI agent platform. The product vendor may be deployed inside the customer's infrastructure (self-hosted), inside a managed cloud (SaaS), or inside a dedicated tenant (single-tenant managed).

Default pipeline stages: vector database storage and search, embedding generation, retrieval and reranking, application-layer logic, prompt assembly.

Default Privacy Tier ceiling: Tier II. Product vendors typically should not see Tier III data unless explicitly contracted and architecturally enabled (for example, a self-hosted product deployed entirely within the Customer Sovereign Zone).

Governing instrument: software license plus a Data Processing Agreement. The license defines product usage; the DPA defines data handling.

### Compute Vendor Zone

The zone operated by a compute infrastructure provider. This zone exists because customer-owned model weights or customer-classified data may reside on infrastructure that the customer does not own. Examples include managed cloud GPU services running customer model weights, on-premises co-located GPU clusters operated by a third party, and hybrid edge appliances installed in customer data centers.

The Compute Vendor zone is distinct from the LLM Vendor zone because the compute vendor provides infrastructure without operating the model. The customer or another vendor brings the model.

Default pipeline stages: GPU and NPU hosting, model weight storage, ephemeral inference compute, infrastructure-level monitoring.

Default Privacy Tier ceiling: Tier I/II for general workloads. Tier III workloads on a Compute Vendor zone require either an air-gapped enclave configuration or a fully isolated tenant with explicit jurisdictional residency guarantees.

Governing instrument: cloud agreement or hardware-as-a-service agreement. Where customer data resides on the vendor's infrastructure, an additional DPA applies.

### LLM Vendor Zone

The zone operated by the model provider. The LLM Vendor zone executes the inference forward pass — taking a prompt as input and returning generated tokens as output.

Default pipeline stages: model inference, generation, optional fine-tuning, model lifecycle management on the vendor's side.

Default Privacy Tier ceiling: Tier I/II. Tier III data should not cross to the LLM Vendor zone except in cases where the LLM Vendor zone is collapsed into the Compute Vendor or Customer Sovereign zones — for example, a fully on-premises model deployment, or a model running inside a customer-controlled enclave on Compute Vendor infrastructure.

Governing instrument: model service contract, optionally including a Business Associate Agreement (BAA) for HIPAA-covered workloads, Standard Contractual Clauses (SCCs) for cross-border GDPR-covered data, and a Data Processing Agreement.

## Crossing Points

The diagram has four Crossing Points between its five zones. Each Crossing Point must be governed by:

1. An explicit legal instrument (named above).
2. An explicit Privacy Tier policy (what tiers may cross, under what redaction or tokenization).
3. An explicit logging policy (every crossing logs to WORM storage in the Customer Sovereign Zone).
4. An explicit jurisdictional check (the active Audit Profile defines which crossings are permitted in which directions).

Silent crossings — flows that occur without classification, contract review, or logging — are the primary failure mode the framework prevents.

## Reading the Diagram in Practice

A CIO, security architect, or compliance officer using the diagram for assessment walks through it in this order:

1. **Identify which zones currently exist** in the organization's AI deployment. Some organizations have only Customer Sovereign and LLM Vendor (direct API consumption). Others have all five (AMS-led delivery with product vendor on managed cloud GPU running a frontier LLM).
2. **For each Crossing Point that exists**, name the legal instrument that governs it.
3. **For each Crossing Point**, name the Privacy Tier ceiling and the redaction policy.
4. **For each Crossing Point**, confirm logging is active and captured in WORM storage.
5. **Overlay the applicable Audit Profile** (or profiles, for multi-jurisdictional deployments). Each profile constrains the diagram further.

The output of this walk is either a clean boundary diagram with all crossings governed, or a list of silent or under-governed crossings that require remediation. That list is the work the framework produces.

## When the Diagram Collapses

Not every deployment uses all five zones. Common collapsed configurations:

- **Direct consumption.** Customer Sovereign → LLM Vendor only. The customer calls the LLM API directly. AMS, Product, and Compute zones are absent. Common in small teams and initial pilots.
- **Self-hosted full stack.** All zones collapse into the Customer Sovereign Zone. The organization runs its own embedding model, vector database, gateway, and inference. Common in defense, classified government work, and Tier III financial services.
- **AMS-managed cloud AI.** Customer Sovereign → AMS → LLM Vendor. The AMS handles orchestration and routes to a managed LLM. Product Vendor and Compute Vendor zones are absent or collapsed into AMS scope.

For collapsed configurations, the framework still applies: every Crossing Point that exists must be governed, classified, and logged. Absent zones simply have no Crossing Points to govern.

## Related Documents

- [Privacy Tier Classification](tier-classification.md) — defines Tier I/II/III in detail
- [Vendor Tier Responsibility Matrix](vendor-tier-matrix.md) — defines which pipeline stages may occur in which zones
- [Audit Profiles](../audit-profiles/) — regulation-specific overlays that constrain the diagram
- [Endpoint Atlas](../endpoint-atlas/) — registry of specific AI vendors and their default zones
- [ContextOps Mapping](../connectors/contextops-mapping.md) — how the diagram connects to organizational governance
