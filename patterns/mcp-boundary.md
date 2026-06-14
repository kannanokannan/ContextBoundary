# MCP Boundary Pattern

MCP is an integration layer. It standardizes how tools and context sources are exposed to agents. It is not the governance authority.

MCP exposes or connects capabilities; ContextBoundary governs whether those capabilities are visible, callable, approval-required, denied, or constrained by Egress Tier and Audit Profile policy.

ContextBoundary can sit between an MCP registry and an agent, filtering discovery before the agent sees available tools.

```text
MCP registry
    |
    v
ContextBoundary policy boundary
    |
    v
Filtered MCP discovery
    |
    v
Agent
```

## Why Discovery Is Governed

For an agent, seeing a tool is already partial access to the surrounding system. A tool name, schema, argument list, or device endpoint tells the agent what actions exist.

Discovery therefore belongs inside the boundary model.

## Policy Questions

At discovery time, ContextBoundary asks:

- Can this agent see this MCP server?
- Can this agent see this tool?
- Can this schema be exposed?
- Does this capability match the agent role?
- Does the active Audit Profile constrain this capability?

At invocation time, ContextBoundary asks:

- Can this agent invoke this tool?
- Is approval required?
- Should the invocation be denied?
- What Egress Tier applies to the payload?
- Is the crossing permitted under the active Audit Profile?
- What audit event must be emitted?

## Example Flow

```text
Registry exposes 12 tools
    |
    v
ContextBoundary evaluates role, trust, tier, jurisdiction
    |
    v
Agent sees 4 tools
    |
    v
2 tools require approval before invocation
    |
    v
All invocations emit audit events
```

## Responsibility Split

ContextOps owns capability accountability: ownership, roles, trust classification, lifecycle, governance metadata, and context source accountability.

ContextBoundary owns boundary enforcement: discovery control, capability visibility, MCP filtering, invocation control, approval and denial workflows, egress control, and auditing.

Do not create a separate CapabilityOps framework for this split.

