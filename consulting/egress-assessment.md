# ContextBoundary Egress Assessment

The ContextBoundary Egress Assessment converts boundary visibility into a maturity score and remediation backlog. It is intended for consulting discovery, not certification.

## Maturity Levels

| Level | Name | Description |
|---|---|---|
| 1 | Ad Hoc | AI workloads cross boundaries without systematic classification, approval, or logging. |
| 2 | Aware | Teams understand boundary risk, but controls are informal, inconsistent, or manual. |
| 3 | Defined | Boundary Zones, Egress Tiers, and at least one Audit Profile are documented for major workloads. |
| 4 | Enforced | Discovery, invocation, egress, approval, and audit controls are implemented consistently. |
| 5 | Optimizing | Boundary telemetry drives continuous drift detection, vendor governance, and procurement decisions. |

## Scoring Categories

Score each category from 1 to 5.

| Category | Focus | Sample Question | Evidence to Request | Consultant Output |
|---|---|---|---|---|
| ContextOps Accountability | Ownership, roles, trust, lifecycle, governance metadata | Who owns a context source when an AI agent uses it across business domains? | RACI matrix, AI ownership policy, context inventory, role definitions | Accountability gap analysis |
| Boundary Architecture | Mapping workloads to the five zones | Can the team trace a prompt, embedding, tool output, and log event across zones? | Architecture diagrams, gateway config, vendor topology, data-flow diagrams | Zone topology map |
| Egress Enforcement | Tier I/II/III control behavior | What deterministic mechanism prevents Tier I data from reaching an external LLM Vendor? | DLP rules, redaction policy, proxy config, egress firewall policy | Egress control gap analysis |
| Integration Protocols | MCP, tools, schema exposure, invocation controls | Which MCP tools can an agent discover, and which require approval? | MCP server configs, tool schemas, service account scopes, approval rules | MCP risk assessment |
| Audit Evidence | Logs, WORM storage, evidence retrieval, drift monitoring | Can the SOC produce the last 30 days of AI boundary crossings by vendor zone? | SIEM queries, WORM logs, audit event schema, retention policy | Observability readiness scorecard |
| Contract Alignment | Vendor clauses supporting technical controls | Do contracts cover prompts, embeddings, tool outputs, telemetry, subprocessors, and model-training exclusions? | MSAs, DPAs, BAAs, SCCs, subprocessor lists, retention terms | Contract remediation matrix |

## Scoring Method

1. Score each category from 1 to 5.
2. Record the evidence supporting the score.
3. Flag categories where technical controls exist but ownership is unclear.
4. Flag categories where contracts exist but technical enforcement is missing.
5. Use the lowest-scoring category as the first remediation priority unless a regulator or business deadline requires otherwise.

## Assessment Outputs

A completed assessment should produce:

- maturity scorecard
- zone topology map
- current-state crossing inventory
- Egress Tier classification gaps
- missing Audit Profile mappings
- MCP discovery and invocation risks
- audit evidence gaps
- vendor contract remediation matrix
- 30/60/90-day remediation roadmap

## Interpretation

The maturity score is a planning instrument. It is not a compliance rating, security certification, or legal conclusion.

