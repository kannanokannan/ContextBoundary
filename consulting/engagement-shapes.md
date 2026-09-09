# Engagement Shapes

Three time-boxed engagements. Each has a purpose, a duration, an effort estimate in
person-days (pd) for the delivering team, a team, what the client must supply, what is
delivered, what "done" means, and what is out of scope.

There are no prices here. The firm delivering the work prices it. What this file fixes is
the shape, so that two firms running the same engagement produce comparable results.

> Claim limits for all consulting material are in [README.md](README.md). Read them before client use.

## Entry criteria

Run any shape only when all three hold:

- The client runs, or has approved, at least one AI agent, assistant, or MCP-connected workflow
  that can reach a system of record, a vendor endpoint, or a customer-facing channel.
- A named business owner exists for that workload.
- The organisation is at ContextOps Maturity Level 2 or above. ContextOps states that it is not
  applicable below Level 2; a boundary assessment inherits that limit. Below Level 2 the first
  engagement is ContextOps foundation work, not this.

## When to decline

- No agent or MCP workload exists or is planned within twelve months. There is no boundary to assess.
- The client wants a certificate. No shape here produces one, and none can.
- The client wants the reference gateway installed as a product. It is not one — see the README.
- The client wants a legal opinion on CLOUD Act or GDPR exposure. The
  [contract checklist](cloud-act-contract-checklist.md) frames questions for counsel; it does not
  replace counsel.

Declining is a normal outcome. Say why, name the prerequisite, and leave the door open.

## Shape 1 — Discovery Workshop

| | |
|---|---|
| Purpose | Make the current AI boundary visible before anything is scored. |
| Duration | One day on site, or two half-days remote. |
| Effort | ~3 pd: preparation 1, delivery 1, write-up 1. |
| Team | One lead (architecture or security background). A second person as scribe is optional and worth it. |
| Client supplies | Participants and pre-workshop inputs per the [Workshop Agenda](workshop-agenda.md), inputs delivered at least five working days before. |
| Delivered | AI Boundary Topology Map; crossing inventory; the five highest-risk gaps; a scope proposal for Shape 2. |
| Done when | The business owner confirms the topology map reflects the workloads in scope. |
| Out of scope | Scoring, contract review, remediation design. |

## Shape 2 — Boundary Assessment

| | |
|---|---|
| Purpose | Score the boundary, review the contracts behind it, sequence remediation, and report to executives. All six lifecycle phases. |
| Duration | Three weeks elapsed. |
| Effort | ~20 pd. Week 1 discovery and evidence, 6. Week 2 scoring, contract review, demonstration, 8. Week 3 roadmap and readout, 6. |
| Team | Lead architect, full time. Security or risk reviewer, half time. Contract reviewer, 2–3 pd — a procurement analyst or paralegal, not a substitute for counsel. |
| Client supplies | Workshop participants; evidence per the [Scoring Worksheet](scoring-worksheet.md) inside week 1; six to eight interviews of 45 minutes; an executive sponsor for the readout. |
| Delivered | Everything in Shape 1; the scored worksheet with evidence cited per category; contract remediation matrix; 30/60/90 roadmap; executive readout per the [template](executive-readout-template.md). |
| Done when | The readout has been delivered to the sponsor, every one of the six scores cites at least one evidence item, and the Decisions Required section is populated. |
| Out of scope | Implementing controls. Writing policy. Negotiating contracts. |

Shape 3 runs inside week 2. If Shape 1 was delivered separately, credit its 3 pd here.

## Shape 3 — Demonstration

| | |
|---|---|
| Purpose | Show enforcement outside the model, reproducible, leaving evidence — exactly as the [Demonstration Guide](demonstration-guide.md) sets out. |
| Duration | Half a day. |
| Effort | ~1 pd, including the same-day rehearsal the guide makes mandatory. |
| Team | One presenter who has run the suite green that day. |
| Client supplies | Architects and security staff. Not executives — the guide says why. |
| Delivered | Nothing written. Optionally the receipt from that session, labelled as an example run with ephemeral keys. |
| Done when | Twenty-two scenarios green in the room, one denial shown, one receipt opened. |
| Out of scope | Client data. Anything that implies the client is buying something. |

## Using these in a proposal

The [SOW Skeleton](sow-skeleton.md) is built around these three shapes. Pick one, fill the
brackets, and attach this file so the client sees the same definition the firm is working from.
