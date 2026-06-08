# CLOUD Act and Vendor Contract Checklist

This checklist helps consultants review whether vendor contracts support the technical boundary described by ContextBoundary. It is a technical and architectural risk tool, not legal advice.

Use it with legal counsel, procurement, security, privacy, and enterprise architecture.

## Core Question

```text
Does the contract support the boundary the architecture claims to enforce?
```

## Contract Set

Review the relevant documents:

- Master Services Agreement (MSA)
- Data Processing Agreement (DPA)
- Business Associate Agreement (BAA), where applicable
- Standard Contractual Clauses (SCCs), where applicable
- Transfer Impact Assessment (TIA), where applicable
- subprocessor list
- support and telemetry terms
- retention and deletion schedule
- audit-rights provisions
- model-training and product-improvement terms

## Review Prompts

### Boundary Zone Clarity

- Does the contract identify whether the vendor is AMS-Service Vendor, Product Vendor, Compute Vendor, or LLM Vendor?
- Does the contract distinguish infrastructure custody from model operation?
- Does the contract identify subprocessors and their role in the chain?
- Are support, telemetry, and observability vendors listed?

### Data Scope

- Does protected data include prompts, completions, embeddings, RAG chunks, files, logs, tool outputs, and metadata?
- Are semantic derivatives such as embeddings treated as governed data?
- Are audit logs and support traces governed by the same restrictions as primary payloads?
- Are diagnostic snapshots and crash reports covered?

### Egress Tier Alignment

- Does the contract support Tier I containment where required?
- Does it require irreversible anonymization or aggregation for Tier II flows?
- Does it define when Tier III per-call escalation is allowed?
- Does it require declared purpose, named endpoint, and audit logging for outbound calls?

### Model Training Exclusion

- Does the contract prohibit use of customer prompts, files, embeddings, tool outputs, logs, and metadata for foundation-model training?
- Does it prohibit fine-tuning, calibration, evaluation, or product improvement without explicit written permission?
- Does the exclusion apply to subprocessors?

### Telemetry and Logging

- Does vendor telemetry contain prompts, completions, identifiers, embeddings, paths, filenames, or tool outputs?
- Can telemetry be disabled, region-locked, redacted, or customer-routed?
- Are support logs subject to retention limits?
- Are logs available for audit evidence?

### Data Residency and Legal Sovereignty

- Does the vendor distinguish geographic residency from legal sovereignty?
- Is the vendor subject to foreign legal compulsion such as the US CLOUD Act?
- Are SCCs, TIAs, or adequacy mechanisms present where required?
- Does the technical routing match the contractual transfer mechanism?

### Subprocessors

- Are all subprocessors listed?
- Are subprocessors bound to the same Egress Tier restrictions?
- Are changes to subprocessors subject to notice and objection rights?
- Can the customer audit or review subprocessor controls?

### Retention and Deletion

- Are prompts, files, embeddings, logs, and tool outputs deleted after the agreed period?
- Is zero-retention or short-retention available for Tier III calls?
- Is deletion verified and evidenced?
- Are backups and disaster-recovery copies covered?

### Audit Rights

- Does the customer have the right to request evidence of boundary controls?
- Are audit logs tamper-evident?
- Can the vendor produce records by endpoint, region, subprocessor, and time window?
- Are independent attestations available?

## Output

The consultant deliverable is a Contract Remediation Matrix:

| Issue | Affected Vendor Zone | Egress Tier Impact | Contract Document | Required Remediation | Owner |
|---|---|---|---|---|---|

The matrix should be reviewed by legal counsel before contract changes are negotiated.

