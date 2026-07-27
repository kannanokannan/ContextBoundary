# Runtime Maturity Ladder

## Purpose

ContextBoundary presents many requirements at once; adopters need to know what a minimum honest deployment looks like and what each additional step buys them. This ladder describes the deployment configuration state of an enforcement point without changing the framework's organizational maturity model.

## Two Axes

| Axis | Authority | What it describes |
|---|---|---|
| Organizational maturity | [ContextOps Levels 1–5](https://github.com/kannanokannan/ContextOps) | The organizational maturity model; its content is not restated here. |
| Runtime maturity | This ladder | The configuration state of the enforcement point. |

These axes are orthogonal and non-competing.

## The Ladder

| Level | Name | Adds | Meaning |
|---|---|---|---|
| 0 | Ungoverned | — | No gateway in path; agent egress is direct and unrecorded. |
| 1 | Observed | R1 interception, R5 sealed receipts (R4, R7 code-resident) | Every discovery and call path is evaluated before forwarding and recorded in a sealed receipt. |
| 2 | Bounded | R2/R3 intent envelopes | Owner-declared, narrowing-only envelopes are frozen per session and enforced. |
| 3 | Enforced | R4 exercised in policy | The five decisions — including MODIFY and DEFER — are actively used, not merely available. |
| 4 | Attributable | R6 agent identity | Agent-held Ed25519 request signatures plus the Ed25519 gateway seal; actions are attributable to a specific actor. |
| 5 | Evidenced | R8 OTel export | Operating evidence is exported continuously and consumable by an external reviewer. |

**Progression:** observe → declare → enforce → attribute → evidence. Every level is a legitimate stopping point and a deployable configuration; no level is a partial or broken state.

Levels are reached by configuring more controls, never by turning an enforcement gate off. This ladder does not document or propose a downgrade switch that disables an enforcement gate.

## Configuration Matrix

The configuration states below were verified against the `contextboundary-gw` source and its CI run.

| Requirement(s) | Configuration state | Ladder effect |
|---|---|---|
| R1, R4, R5, R7 | Code-resident and active once deployed | Supports the interception, decision, receipt, and deterministic divergence mechanisms used by the ladder. |
| R2/R3 | Requires the `INTENT_ENVELOPE_BOOTSTRAP_KEY` binding; without it `boundary/session.start` fails closed with `owner_proof_unavailable` | Enables Level 2 owner-declared, narrowing-only session envelopes. |
| R6 | Requires registered agent public keys (optionally supplied through `AGENT_KEY_REGISTRY`) plus the gateway Ed25519 private key | Enables Level 4 agent identity and gateway sealing. |
| R8 | Requires `OTLP_HTTP_ENDPOINT` | Enables Level 5 operating-evidence export. |

> **Verified status.** Verified against `contextboundary-gw` at commit `b9f58f3`; CI run `30280559652` is green. Confirmed: R2/R3 require `INTENT_ENVELOPE_BOOTSTRAP_KEY` for a valid frozen session and fail closed without it; R6 requires registered agent public keys plus gateway Ed25519 signing material; R8 requires `OTLP_HTTP_ENDPOINT`; R1/R4/R5/R7 are code-resident. The gateway receipt seal is Ed25519 and is verifiable with public keys only.

## Crosswalk

| Organizational maturity | Runtime maturity |
|---|---|
| ContextOps Level 2 | Prerequisite for runtime Level 2 and above, consistent with the gate rule in [contextops-mapping.md](./contextops-mapping.md). |

No additional coupling rule is defined here.

## Scope Limits

These levels describe deployment configuration state, not conformance. This is not a certification scheme, and there is no self-assessment badge.

The claim ceiling remains: **AARM-aligned strict-determinism profile — all Core requirements (R1–R6) implemented and CI-verified; independent conformance review not yet undertaken.** The ladder does not imply a live production service.
