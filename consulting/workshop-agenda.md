# Discovery Workshop Agenda

The Discovery Workshop is the first consulting engagement in a ContextBoundary assessment. Its purpose is to make the current AI boundary visible before scoring, contract review, or remediation planning.

## Participants

Recommended participants:

- Business owner for the AI use case
- Enterprise architect
- Security architect
- Data protection or privacy lead
- Legal or procurement representative
- AI platform owner
- Agent or automation owner
- Operations or SOC representative

## Pre-Workshop Inputs

Request these artifacts before the workshop:

- AI workload inventory
- Known LLM, agent, RAG, and automation use cases
- MCP server and tool registry list, if present
- API gateway and egress proxy diagrams
- Cloud and SaaS architecture diagrams
- Current vendor list for AI-related systems
- Existing MSAs, DPAs, BAAs, SCCs, or subprocessors list
- Data classification policy
- Audit logging and SIEM coverage summary

## Agenda

### 1. Frame the Boundary

Explain the ContextBoundary question:

```text
Where is data allowed to go?
```

Confirm the five Boundary Zones:

- Customer Sovereign
- AMS-Service Vendor
- Product Vendor
- Compute Vendor
- LLM Vendor

### 2. Inventory AI Workloads

Map current and planned AI activity:

- chat assistants
- coding assistants
- RAG systems
- summarization tools
- agentic workflows
- MCP servers
- AI-enabled SaaS features
- observability and logging pipelines

### 3. Map Crossings

For each workload, identify:

- source data
- destination system
- vendor zone
- Egress Tier
- active Audit Profile
- approval path
- logging path

### 4. Surface Shadow AI and Silent Crossings

Ask:

- Which AI systems were adopted without architecture review?
- Which tools can agents discover by default?
- Which endpoints receive prompts, embeddings, logs, or tool outputs?
- Which vendors receive telemetry?
- Which data flows are not represented in contracts?

### 5. Capture Known Controls

Identify current controls:

- redaction
- tokenization
- DLP
- API gateway policy
- MCP allowlists
- human approval gates
- short-lived credentials
- WORM audit storage
- SIEM coverage

### 6. Confirm Assessment Scope

Define the scope for the next phase:

- workloads in scope
- vendors in scope
- jurisdictions in scope
- Audit Profiles in scope
- documents to review
- stakeholders to interview

## Workshop Output

The primary output is an AI Boundary Topology Map:

- systems and agents
- Boundary Zones
- Crossing Points
- Egress Tiers
- missing contracts
- missing logs
- immediate high-risk gaps

This output feeds the [Egress Assessment](egress-assessment.md).

