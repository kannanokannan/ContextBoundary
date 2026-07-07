# Agent Authority Enforcement

**A ContextBoundary runtime control set for agentic AI.**

Version: 0.1 (2026-07-06) · License: Apache 2.0

---

## Scope

ContextOps defines agent-authority governance (`ContextOps/agent-authority.md`): accountable ownership, Autonomy Tier, Least Agency, Tool Supply Policy. This document specifies the runtime controls that enforce those decisions at the boundary. It does not restate the governance rationale — it translates governance into enforcement, consistent with the doctrine that discovery is itself a boundary concern.

The base ContextBoundary framework governs *data egress*. Agent authority adds a second enforcement question: **not only may this data leave, but may this agent discover and invoke this capability at all.**

---

## Three controls

**E1 — Identity at invocation.** Every tool or agent-to-agent invocation is bound to the accountable identity of the calling agent (governance A1). The audit record captures the agent, its accountable owner, the Autonomy Tier in force, and the invocation. An invocation that cannot be bound to an accountable identity is blocked. This extends the existing Sovereign Egress logging obligation from data crossings to action crossings.

**E2 — Autonomy-tier gating.** Discovery, invocation, and approval requirements are gated on the agent's Autonomy Tier (governance A2). A higher tier does not widen discovery by default — capability visibility remains a separate grant. Actions above the agent's tier require the approval path defined for that crossing. This applies the framework's existing approval-workflow and invocation-control posture to agent autonomy.

**E3 — Tool supply-chain filtering.** Only tools and MCP sources whose source carries an acceptable trust classification (governance A4) are made discoverable to an agent. Untrusted-source capabilities are filtered *at discovery*, before invocation is possible — because, per the boundary doctrine, exposing an available capability already reveals information and increases risk.

---

## Mapping to the existing model

| Base ContextBoundary concept | Agent-authority extension |
|------------------------------|---------------------------|
| Sovereign Egress logging | Extended from data crossings to action/invocation crossings (E1) |
| Crossing Point + approval workflow | Gated additionally on Autonomy Tier (E2) |
| Discovery as a boundary concern | Applied to agent tool/MCP discovery by source trust (E3) |
| Endpoint Atlas | Gains an agent-tool source-trust attribute per endpoint |

No new axis is introduced. Agent authority is enforced through the three controls above, layered on the existing Egress Tier / Geography / Vendor Tier axes.

---

## What this is NOT

- Not the governance definition — that is ContextOps `agent-authority.md`.
- Not an orchestration engine or policy language. It is a specification of the controls a boundary implementation must provide.
- Not a replacement for the data-egress controls. It runs alongside them.

---

## Changelog

- **2026-07-06** — v0.1 created. Three runtime controls (E1 identity-at-invocation, E2 autonomy-tier gating, E3 tool supply-chain filtering) specified and mapped to existing ContextBoundary concepts. Authored by Chanakya; registered by Hanuman in `README.md`, `llms.txt`, `llms-full.txt`, and `ARCHITECTURE.md`.
