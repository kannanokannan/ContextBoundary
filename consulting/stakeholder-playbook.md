# Stakeholder Approval Playbook

The Stakeholder Approval Playbook maps ContextOps accountability to ContextBoundary enforcement decisions.

It defines who should approve boundary crossings, which evidence they need, and which technical preconditions must exist before a crossing is allowed.

## Stakeholder Roles

| Stakeholder | Primary Concern |
|---|---|
| Business Owner | Business purpose, utility, operational impact |
| Context Owner | Context source accountability, domain ownership, lifecycle |
| Enterprise Architecture | Zone placement, system fit, target architecture |
| Security and Risk | technical control, misuse risk, threat model, auditability |
| Data Protection / Privacy | data category, lawful basis, retention, subject rights |
| Legal / Procurement | contract terms, DPA, BAA, SCC, subprocessors |
| Operations / SOC | logging, monitoring, incident response, drift detection |

## Egress Tier Approval Model

| Egress Tier | Default Posture | Typical Approval |
|---|---|---|
| Tier I | Never leaves the system | Exception review by Context Owner, Security, Data Protection, and Enterprise Architecture |
| Tier II | Anonymised aggregates only, with consent or documented basis | Context Owner, Data Protection, Security |
| Tier III | Explicit per-call API escalation | Business Owner, Security, and the owner of the receiving endpoint; Legal where vendor or cross-border terms apply |

Tier III does not mean raw regulated data may freely leave. It means the crossing is explicit, declared, logged, and permitted by policy.

## Zone Crossing Approval Matrix

| Crossing | Default Tier Posture | Required Review | Technical Preconditions |
|---|---|---|---|
| Customer Sovereign to Customer Sovereign | Tier I retained | Business Owner, Context Owner, Enterprise Architecture | local execution, no external telemetry, internal audit path |
| Customer Sovereign to AMS-Service Vendor | Tier II by default | Context Owner, Data Protection, Security, Legal / Procurement | DPA, redaction or aggregation, role-based access, WORM logging |
| Customer Sovereign to Product Vendor | Tier II by default; Tier III by explicit escalation | Business Owner, Security, Data Protection, Legal / Procurement | scoped credentials, retention limits, product DPA, audit events |
| Customer Sovereign to Compute Vendor | Tier II/III with custody review | Enterprise Architecture, Security, Risk, Legal / Procurement | residency controls, key custody, deletion evidence, subprocessor review |
| Customer Sovereign to LLM Vendor | Tier II/III with strict per-call governance | Business Owner, Security, Data Protection, Legal / Procurement, Risk | model-training exclusion, retention terms, transfer mechanism, prompt logging policy |

## Approval Gate Checklist

Before approving a crossing, confirm:

- Business purpose is documented.
- Context Owner is assigned.
- Egress Tier is assigned.
- Audit Profile is selected.
- Receiving Boundary Zone is named.
- Contract instrument is named.
- Redaction, aggregation, or tokenization controls are defined.
- Approval path is documented.
- Audit event is emitted.
- Review cycle is scheduled.

## Where Approval Fails

Common failure modes:

- tool visibility is granted without ownership
- destructive invocation is allowed without human approval
- telemetry bypasses the approved egress path
- vendor terms allow model training on customer data
- subprocessors are invisible to the architecture team
- logs cannot reconstruct the boundary crossing

The approval process is only defensible when the business decision, technical control, and audit record all align.

