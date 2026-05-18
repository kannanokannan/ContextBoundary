# Vendor Tier Responsibility Matrix

The Vendor Tier Responsibility Matrix is the architectural answer to the question: *which vendor tier operates which AI pipeline stage, and under what conditions?*

The matrix has rows for pipeline stages and columns for the five Boundary Zones defined in the canonical diagram. Each cell records whether the stage may occur in that zone and what conditions apply.

## The Matrix

| Pipeline Stage | Customer | AMS / Service | Product Vendor | Compute Vendor | LLM Vendor |
|---|---|---|---|---|---|
| Data Capture | ✅ Owns | ❌ | ❌ | ❌ | ❌ |
| PII / PHI Detection | ✅ Owns | 🟡 Controlled | ❌ | ❌ | ❌ |
| Tokenization / Redaction | ✅ Owns | 🟡 Controlled | ❌ | ❌ | ❌ |
| Identity / RBAC | ✅ Owns | 🟡 Federated | ❌ | ❌ | ❌ |
| Data Lake / Source Storage | ✅ Owns | 🟡 Controlled | ❌ | 🟡 Custody | ❌ |
| ETL / Ingestion Pipelines | 🟡 | ✅ Operates | ✅ Operates | ❌ | ❌ |
| Embedding Generation | 🟡 | ✅ Operates | ✅ Operates | 🟡 Tier I/II | 🟡 Tier I only |
| Vector Storage | 🟡 | ✅ Operates | ✅ Operates | 🟡 Custody | ❌ |
| Retrieval / RAG | 🟡 | ✅ Operates | ✅ Operates | ❌ | ❌ |
| Reranking | 🟡 | ✅ Operates | ✅ Operates | ❌ | 🟡 Tier I/II |
| Prompt Assembly | 🟡 | ✅ Operates | ✅ Operates | ❌ | ❌ |
| Inference (forward pass) | 🟡 Local | ❌ | 🟡 Proxy only | ✅ Operates | ✅ Operates |
| Fine-tuning | 🟡 Local | ❌ | 🟡 Proxy only | ✅ Operates | ✅ Operates |
| Output Sanitization / DLP | ✅ Owns | 🟡 Controlled | 🟡 Controlled | ❌ | ❌ |
| De-tokenization | ✅ Owns | ❌ | ❌ | ❌ | ❌ |
| Orchestration / Routing | 🟡 | ✅ Operates | 🟡 Embedded | ❌ | ❌ |
| Monitoring / Telemetry | 🟡 | ✅ Operates | 🟡 Embedded | 🟡 Infra-level | 🟡 Service-level |
| Audit Log (WORM) | ✅ Owns | ❌ | ❌ | ❌ | ❌ |
| Caching | 🟡 | ✅ Operates | ✅ Operates | 🟡 | ❌ |
| Guardrails / Output Filtering | ✅ Owns | 🟡 Controlled | 🟡 Controlled | ❌ | 🟡 Vendor-side |

## Cell Notation

- **✅ Owns** — This zone is the accountable owner of the stage. The stage occurs here by default and cannot be delegated without remediation.
- **✅ Operates** — This zone may operate the stage under appropriate contractual controls. Operation here is common and acceptable.
- **🟡 Conditional** — This zone may operate the stage only under specific conditions defined in the matrix notes below or the active Audit Profile.
- **❌ Not Permitted** — This zone may not operate the stage by default. Where a deployment requires it, a documented exception with compensating controls is required.

## Conditional Cell Notes

**Customer — Embedding / Vector / Retrieval / Orchestration / Caching / Monitoring (🟡).** The Customer Sovereign Zone may operate these stages when the organization runs its own embedding model, vector database, gateway, and monitoring stack. This is the common configuration for Tier III-heavy deployments and air-gapped environments. The cell is yellow rather than green because most organizations delegate these to AMS or Product Vendors for operational reasons.

**AMS — PII Detection / Tokenization / Identity / Data Lake / Output Sanitization / Guardrails (🟡 Controlled).** AMS providers may perform these activities under explicit DPA terms where the AMS has signed processor obligations under applicable regulation. Tier III data should not be visible to the AMS in plaintext at any point unless the AMS is operating within a customer-controlled enclave.

**AMS — Identity (🟡 Federated).** The AMS does not operate the customer's identity provider. The AMS authenticates against the customer's IdP via federated identity (OIDC, SAML). Local AMS user directories may exist for operational staff but must not be the source of truth for customer-data access.

**Product Vendor — Inference / Fine-tuning (🟡 Proxy only).** A product vendor may proxy inference calls to an LLM Vendor on the customer's behalf but should not operate the inference itself unless the vendor's product is a self-hosted model. Where a product vendor advertises "AI capabilities," the underlying inference is almost always operated by an LLM Vendor; the product vendor is a Compute Vendor or pass-through.

**Compute Vendor — Embedding (🟡 Tier I/II).** A compute vendor running customer-provided embedding models may process Tier I or Tier II data. Tier III embedding requires an enclave configuration with documented isolation guarantees.

**Compute Vendor — Vector Storage / Data Lake (🟡 Custody).** Customer data residing on Compute Vendor infrastructure is in custody, not in operation. Custody arrangements require explicit residency, encryption, and key management terms.

**LLM Vendor — Embedding / Reranking (🟡 Tier I).** LLM Vendors typically expose embedding and reranking APIs. These may be used for Tier I data without restriction. Tier II data should not transit to the LLM Vendor's embedding API unless under a configuration that constitutes a Compute Vendor zone (BYOK, region-locked, dedicated tenant) and is governed by an appropriate DPA.

**LLM Vendor — Monitoring (🟡 Service-level).** LLM Vendors will produce service-level telemetry for their own operations. This is acceptable provided the telemetry does not include customer prompts or responses in plaintext form.

**LLM Vendor — Guardrails (🟡 Vendor-side).** LLM Vendors operate content safety filters on their side as a default. Customer-controlled guardrails remain a Customer Sovereign Zone responsibility. The two layers compose; vendor-side filtering does not substitute for customer-side filtering for compliance purposes.

## How Audit Profiles Modify the Matrix

Audit Profiles do not add new zones or new stages. They modify cell values based on the regulation's specific constraints. Examples:

- The GDPR Profile changes the Compute Vendor and LLM Vendor cells from "🟡 Conditional" to "❌ Not Permitted" for any personal data crossing into a non-adequacy jurisdiction without SCCs.
- The DPDP Profile restricts certain Tier III financial data to the Customer Sovereign Zone, regardless of contractual mitigations.
- The HIPAA Profile requires that any cell marked ✅ Operates for Tier III PHI carry a Business Associate Agreement.
- The EU AI Act Profile adds documentation and oversight requirements to cells operating high-risk AI use cases, particularly those touching biometric data or critical infrastructure.

The base matrix above represents the default configuration with no Audit Profile applied. The base matrix is the starting point; Audit Profiles restrict it.

## How to Use the Matrix

For each workload the organization intends to deploy:

1. **List the pipeline stages** the workload uses.
2. **For each stage, identify which zone currently operates it** in the deployment.
3. **Cross-reference the cell.** If the cell is ✅, the placement is permitted by default. If 🟡, verify the condition is met. If ❌, the placement requires an exception or remediation.
4. **Apply the relevant Audit Profile** (or profiles for multi-jurisdictional cases). Note any cells that change.
5. **Document each cell choice** as Context Asset metadata if ContextOps is in use, or as architecture documentation otherwise.

The matrix is the architectural ground truth. Where reality and the matrix disagree, either the matrix needs updating with a documented exception, or the reality needs remediation. Silent disagreement is the failure mode.

## What the Matrix Does Not Cover

The matrix covers AI-specific pipeline stages. It does not cover:

- Underlying network architecture (VPC design, subnet segmentation, routing)
- Storage encryption at rest (assumed; controlled by separate policy)
- Disaster recovery and backup (assumed; controlled by existing BC/DR policy)
- Generic application security controls (authentication, session management, etc.)

These remain the responsibility of the organization's existing security architecture program. ContextBoundary overlays them with AI-specific routing decisions.
