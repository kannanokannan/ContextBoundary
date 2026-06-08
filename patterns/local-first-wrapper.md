# Local-First Wrapper Pattern

A local-first wrapper is a developer adoption pattern for running ContextBoundary near the agent.

It is inspired by the placement of local proxy tools, but the purpose is different: ContextBoundary governs capability access; it does not compress context.

```text
Agent CLI / IDE / App
    |
    v
Local ContextBoundary wrapper
    |
    v
MCP tools / APIs / context sources / devices
```

## Possible Developer Commands

These commands are illustrative, not product commitments.

```bash
contextboundary wrap claude
contextboundary wrap codex
contextboundary wrap cursor
contextboundary proxy --mcp-config ./mcp.json
contextboundary audit tail
```

## Wrapper Responsibilities

A wrapper can:

- load the active policy file
- filter MCP discovery
- hide denied tools from the agent
- mark approval-required tools
- inspect payload Egress Tier
- block forbidden crossings
- emit audit events

## 60-Second Demo Shape

Before ContextBoundary:

```text
Agent sees 12 MCP tools, including finance.transfer_funds and device.unlock.
```

After ContextBoundary:

```text
Agent sees 4 approved tools.
2 tools require approval.
Tier I data cannot egress.
All invocations emit audit events.
```

This is the adoption story: tool first, doctrine second.

