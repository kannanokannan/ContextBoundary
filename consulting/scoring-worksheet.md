# Scoring Worksheet

The working instrument for Phase 3 of the [Egress Assessment](egress-assessment.md). One block
per category: what to ask, what to request, what each score means, and where to write the
result. Copy the whole file into the engagement workspace and fill it there.

Rules that hold for every block:

- **No score without evidence.** A score with an empty evidence cell is a guess and is reported as "not assessed".
- **Score what exists, not what is planned.** A control on a roadmap scores as absent.
- **Lowest score first.** Unless a regulator or a go-live date says otherwise, the lowest category is remediation priority one.
- **Two axes, never averaged.** This worksheet scores the organisation. The enforcement point's configuration is scored separately on the [runtime maturity ladder](../maturity-ladder.md).

> Claim limits for all consulting material are in [README.md](README.md). Read them before client use.

## Interview opening

Say this, or something with the same content, at the start of every interview:

> We are mapping where this organisation's AI systems are permitted to send data and where they
> actually send it. Nothing you say is a finding against you or your team. If you don't know an
> answer, "I don't know" is useful — it tells us where the map has no owner.

## Evidence request

Send this list to the client in week 1, with the date it is due. Items not received by the due
date are recorded as "not provided" and the affected category is scored on what was seen.

1. AI workload inventory, including anything adopted without architecture review
2. RACI or ownership record for AI systems and for the context sources they read
3. Architecture and data-flow diagrams for each workload in scope
4. API gateway, egress proxy, and DLP or redaction configuration
5. MCP server list, tool schemas, and service-account scopes for each agent
6. Approval rules for agent actions, and the record of approvals given
7. SIEM queries or log exports showing AI-related boundary crossings for the last 30 days
8. Log retention and tamper-evidence configuration
9. MSAs, DPAs, BAAs, SCCs, and subprocessor lists for every AI vendor in scope
10. Vendor terms on model training, prompt retention, and telemetry

---

## Category 1 — ContextOps Accountability

**Ask**

- Who owns the context source an agent read when it acted?
- When a workload moved into operations, who inherited ownership of its context?
- Is there a record of that ownership anywhere other than in someone's head?
- What happens to ownership when the owner leaves?
- Who is accountable for an agent's action today: the agent's owner, the workflow's owner, or nobody?

**Evidence** — items 1, 2.

| Score | Anchor |
|---|---|
| 1 | No named owners for context sources or agents. |
| 2 | Owners known informally; not recorded. |
| 3 | Owners recorded for major workloads; a RACI exists. |
| 4 | No agent reaches production without a named Context Owner and business owner; the rule is enforced, not advised. |
| 5 | Ownership reviewed on a cadence; a lapse in ownership is detected and acted on. |

| Score | Evidence cited | Finding |
|---|---|---|
| | | |

## Category 2 — Boundary Architecture

**Ask**

- For one workload, walk me from a prompt to every place the data ends up.
- Which of the five Boundary Zones does each of those places sit in?
- Where do embeddings go? Where do tool outputs go? Where do the logs of all this go?
- Which crossings on that path have nobody who can name them?
- When a new crossing is added, who finds out, and when?

**Evidence** — items 1, 3.

| Score | Anchor |
|---|---|
| 1 | No map of where AI data goes. |
| 2 | Partial diagrams; zones not named. |
| 3 | The five zones mapped for major workloads; crossings listed. |
| 4 | Every crossing carries a zone, an Egress Tier, and an approval path, on record. |
| 5 | The topology is maintained continuously; a new crossing is detected before go-live, not after. |

| Score | Evidence cited | Finding |
|---|---|---|
| | | |

## Category 3 — Egress Enforcement

**Ask**

- What deterministic mechanism stops Tier I data reaching an external LLM vendor?
- Is a model ever consulted to decide whether an egress is acceptable?
- Which paths have redaction or DLP, and which have only guidance?
- If the control were misconfigured tomorrow, what would notice?
- Is the enforcement configuration versioned?

**Evidence** — items 4, 6.

| Score | Anchor |
|---|---|
| 1 | Nothing prevents Tier I data reaching an external LLM vendor. |
| 2 | Manual practices only: guidance, training, review. |
| 3 | Egress Tiers documented; deterministic controls on some paths. |
| 4 | Deterministic enforcement on every crossing; no model in the decision path. |
| 5 | Enforcement configuration versioned, tested, and monitored for drift. |

| Score | Evidence cited | Finding |
|---|---|---|
| | | |

## Category 4 — Integration Protocols

**Ask**

- Which tools can an agent discover today without anyone approving it?
- Which of those tools can delete, pay, send, or change access?
- What does an agent need before it may invoke a destructive tool?
- What scope does the agent's service account carry, and who set it?
- When a tool schema changes, who reviews the exposure?

**Evidence** — items 5, 6.

| Score | Anchor |
|---|---|
| 1 | Agents discover whatever is available; no allowlist. |
| 2 | Ad hoc restrictions, per team. |
| 3 | MCP allowlists for major agents; destructive tools identified. |
| 4 | Discovery filtered by policy; destructive invocations require approval; scopes are minimal. |
| 5 | Tool exposure reviewed on every change; approvals leave evidence. |

| Score | Evidence cited | Finding |
|---|---|---|
| | | |

## Category 5 — Audit Evidence

**Ask**

- Show me the last 30 days of AI boundary crossings, by vendor zone.
- When your AI system refused something, what can you show a regulator six months later about why?
- Can the log be altered after the fact? By whom?
- Which policy version was in force when a given decision was made — can you tell?
- What is the retention period, and who chose it?

**Evidence** — items 7, 8.

| Score | Anchor |
|---|---|
| 1 | No AI boundary crossing can be reconstructed. |
| 2 | Logs exist somewhere; not queryable by crossing. |
| 3 | Crossings logged for major workloads; retention set. |
| 4 | The SOC can produce 30 days of crossings by vendor zone; storage is tamper-evident. |
| 5 | Evidence drives drift detection; each decision is bound to the policy version that produced it. |

| Score | Evidence cited | Finding |
|---|---|---|
| | | |

## Category 6 — Contract Alignment

**Ask**

- Which contract covers the prompts your agents send to this vendor? The embeddings? The telemetry?
- Does any vendor's terms permit training on your data?
- Who are the subprocessors, and when did you last check?
- Where a technical control exists, does a contract term back it — and the reverse?
- Who is notified when a vendor changes its terms?

**Evidence** — items 9, 10. Use the [CLOUD Act Contract Checklist](cloud-act-contract-checklist.md).

| Score | Anchor |
|---|---|
| 1 | AI vendor terms not reviewed for prompts, telemetry, or training. |
| 2 | Reviewed once; gaps known and unremediated. |
| 3 | Major vendors covered: training exclusion, retention, subprocessors. |
| 4 | Every LLM and product-vendor crossing has a named contract instrument matching the technical control. |
| 5 | Terms re-verified on vendor change; subprocessor changes tracked. |

| Score | Evidence cited | Finding |
|---|---|---|
| | | |

---

## Summary

| Category | Score | Priority | One-line finding |
|---|---|---|---|
| ContextOps Accountability | | | |
| Boundary Architecture | | | |
| Egress Enforcement | | | |
| Integration Protocols | | | |
| Audit Evidence | | | |
| Contract Alignment | | | |

Runtime maturity ladder level, scored separately: `[ ]`

Carry the summary table into Section 3 of the [Executive Readout](executive-readout-template.md)
unchanged.
````
