# ContextBoundary Consulting Enablement

This directory packages ContextBoundary into repeatable consulting engagements. It is for enterprise consultants, risk advisors, cloud and security architects, procurement teams, and compliance transformation groups that need a practical way to assess and remediate AI egress risk.

These materials are enablement assets, not legal advice, compliance certification, or product commitments. They help consultants structure discovery, evidence collection, contract review, stakeholder alignment, and executive reporting around the ContextBoundary specification.

## What you may and may not claim

Every document in this directory is governed by the statements below. They are stated **once, here**, rather than repeated in each file, so that there is one place to correct if they change.

- **AARM is a Cloud Security Alliance standard, not a Context Stack standard.**
- **ContextBoundary is AARM-aligned. No conformance claim is made.** Its reference gateway implements all AARM v1.0 Core requirements (R1–R6) and verifies them in CI. That is alignment, not conformance.
- **Not listed on the CSA Builders Registry.**
- **No independent conformance review has been undertaken.**
- ContextBoundary is a specification. `contextboundary-gw` is a reference implementation. **Neither is a product, and neither is offered as a hosted service.**

Do not soften these in a client setting. Overstating them costs more than the engagement is worth.

## What changed in 2026

This package was first written before the reference gateway existed. The material below is still current, but the engagement it supports has changed in one important way:

**You can now demonstrate the control, not only assess the gap.**

`contextboundary-gw` runs from a clean clone in about ten minutes and executes 22 conformance scenarios against a live local gateway. Every decision it emits carries a sealed, tamper-evident receipt bound to a hash of the exact compiled policy that produced it. A client can watch a denial happen and then inspect the evidence for why.

That changes phases 2 and 5 of the lifecycle below: current-state assessment gains a working reference for what "enforced" looks like, and the remediation roadmap can point at something runnable rather than a diagram. See the [Demonstration Guide](demonstration-guide.md).

## Engagement Lifecycle

A standard ContextBoundary consulting engagement has six phases:

1. **Discovery Workshop** - identify AI workloads, agents, MCP servers, context sources, vendors, and current crossings.
2. **Current-State Assessment** - inspect architecture, egress paths, tool discovery, invocation controls, and audit evidence.
3. **Maturity Scoring** - score the organization against the ContextBoundary Egress Assessment model.
4. **Vendor Contract Review** - review MSAs, DPAs, BAAs, SCCs, subprocessors, telemetry, training exclusions, audit rights, and retention clauses as technical risk inputs.
5. **Boundary Remediation Roadmap** - define the target-state boundary, required controls, policy gaps, and sequenced remediation.
6. **Executive Readout** - translate technical findings into business risk, investment priorities, and accountable next steps.

## Deliverables

- [Workshop Agenda](workshop-agenda.md) - phase-one discovery workshop runbook.
- [Egress Assessment](egress-assessment.md) - maturity model, scoring categories, interview questions, evidence requests, and outputs.
- [Demonstration Guide](demonstration-guide.md) - how to show the boundary working, using the reference gateway.
- [CLOUD Act Contract Checklist](cloud-act-contract-checklist.md) - vendor contract review prompts for data residency, legal sovereignty, telemetry, retention, and subprocessors.
- [Stakeholder Playbook](stakeholder-playbook.md) - approval gates and stakeholder responsibilities by Egress Tier and Boundary Zone.
- [Executive Readout Template](executive-readout-template.md) - board-level summary structure.
- [Case Study: A Decision Without a Receipt](case-study-decision-without-a-receipt.md) - a documented incident showing what a decision costs when it carries no evidence.

## Consulting Boundary

ContextBoundary consulting work should stay within these limits:

- Do not claim legal advice.
- Do not claim compliance certification.
- Do not endorse vendors.
- **Do not imply that ContextBoundary or its reference gateway is a product, a hosted service, or something being sold.** A reference implementation exists and can be demonstrated; that is not the same as a product, and the distinction matters most in the room where someone is deciding whether to buy something.
- Do not create another framework such as CapabilityOps.
- Keep ContextOps as the accountability layer and ContextBoundary as the boundary-control layer.

The consulting package exists to make the open specification actionable in client engagements while preserving vendor neutrality.
