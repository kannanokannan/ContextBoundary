# AARM v1.0 Implementation Status and Historical Gap Analysis

> **Ratified implementation status — 2026-07-26.** ContextBoundary is an **AARM-aligned strict-determinism profile — all Core requirements (R1–R6) implemented and CI-verified; independent conformance review not yet undertaken.** R1–R6 are implemented in the reference gateway: transparent interception; frozen owner-declared session context and intent envelopes; deterministic envelope evaluation; all five decisions (ALLOW, DENY, STEP_UP, MODIFY, DEFER); tamper-evident receipts; and per-agent Ed25519 identity binding. R7 is a designed deterministic divergence using envelope-drift counting, and R8 OpenTelemetry export is implemented.

> **Honesty boundary.** The reference gateway is verifiable from a clean clone; production deployment remains pending. R6 proves that the registered agent key signed an action, not the human principal behind it. Production operation and independent CSA TWG evidence review remain required before any AARM conformance claim. No AARM approval claim is made. The pre-v1.1 analysis below is retained as historical context and is superseded where it describes R2/R3/R4/R6 as unfinished.

**Where ContextBoundary stands against the standard we've decided to conform to (D-04).**
Version 1.0 (2026-07-11) · Author: Chanakya · Source: AARM v1.0 (CSA TWG, Errico, Feb 2026, arXiv:2602.09433, CC BY 4.0), archived at references/aarm/2026-07-11_aarm.dev_spec_v1.0.txt
Status: updated 2026-07-26: deterministic R3 reading is implemented; R1–R6 are implemented and CI-verified; R7 remains a designed deterministic divergence; R8 is implemented. Egress-sovereignty and vendor-continuity remain profile extensions. Production operation and independent CSA TWG evidence review remain before any AARM conformance claim.

---

## 0. Headline (read this first)

ContextBoundary is **architecturally AARM-aligned** (AARM's §6.1 Protocol Gateway) and its reference gateway implements and CI-verifies all Core requirements, R1–R6. D4 receipts provide tamper-evident, third-party-verifiable evidence. **R7 semantic distance** remains a designed deterministic divergence: envelope-drift counting replaces a model-in-path semantic mechanism. **R8 OpenTelemetry export** is implemented. This is a *strict-determinism profile*, not a claim that independent AARM conformance review has been completed.

The D-04 pivot is now implemented rather than merely a documentation reframe. Egress-sovereignty and vendor-continuity remain clean *extensions* AARM does not require. Production operation and independent CSA TWG evidence review remain the prerequisites for any AARM conformance claim.

---

## 1. AARM v1.0 in brief

- Control plane intercepts/evaluates/decides/records every agent-initiated action **before execution**, model/framework/environment-agnostic (AARM §Abstract).
- **Nine requirements.** Core R1–R6 (MUST) = "AARM Core" conformance. Extended R7–R9 (SHOULD) = "AARM Extended."
- **Eleven threat classes** T1–T11; an implementation making the AARM conformance claim must address all.
- **Conformance is evidence-reviewed** by the AARM TWG: satisfy the MUSTs, run their testing protocol, operate in production with real workloads, submit an evidence package (~14-day review).
- **Four reference architectures:** Protocol Gateway (§6.1), SDK Instrumentation, Kernel eBPF, Vendor Integration. **We are §6.1.**

## 2. Requirement-by-requirement

| AARM req | Our mechanism | Status | Scope note |
|---|---|---|---|
| **R1** Pre-execution interception (MUST) | contextboundary-gw evaluates all MCP discovery and call paths before forwarding | **Implemented + CI-verified** | Gateway topology still requires a no-bypass deployment configuration. |
| **R2** Context accumulation — intent + prior actions + thread, available to policy eval (MUST) | Frozen session envelope, envelope hash, and prior-action trace are supplied to evaluation | **Implemented + CI-verified** | The owner-declared envelope is frozen at session start. |
| **R3** Policy eval with **intent alignment** (MUST) — actions evaluated in isolation do NOT satisfy | Base policy runs first; the owner-declared envelope can only narrow authority | **Implemented + CI-verified** | The deterministic reading of stated intent is the declared authorization envelope. |
| **R4** Five decisions ALLOW/DENY/MODIFY/STEP_UP/DEFER (MUST) | Declared deterministic transforms support MODIFY; durable condition-gated records support DEFER | **Implemented + CI-verified** | MODIFY is allowlisted and narrowing-only; DEFER never executes until a fresh re-evaluation. |
| **R5** Tamper-evident receipts — action/decision/timestamp/policy-context, verifiable (MUST) | D4 hash chain with Ed25519 seal, replay inputs, and public-key verification | **Implemented + CI-verified** | Third-party receipt verification uses public keys only. |
| **R6** Identity binding — receipt **cryptographically bound to agent identity**, non-repudiation (MUST) | Agent-held Ed25519 action signatures, key rotation, nonce replay protection, and Ed25519 gateway seal | **Implemented + CI-verified** | Proves the registered agent key signed the action, not the human principal behind it. |
| **R7** Semantic distance tracking (SHOULD, addresses T6 drift) | Deterministic envelope-drift counting | **Designed deterministic divergence** | No semantic or model-in-path mechanism; do not make an AARM Extended claim. |
| **R8** Telemetry export, OpenTelemetry (SHOULD) | Non-authoritative OTLP/HTTP mirror; JSONL receipts remain the system of record | **Implemented + CI-verified** | Export failure never blocks or drops a receipt. |
| **R9** Least-privilege at execution (SHOULD) | A3 Least Agency, tier gates, discovery filtering (E3), trusted_sources | **Partial, deployment-dependent** | Tool-access scoping is implemented; per-action credential scoping remains deployment configuration. |

## 3. The crux: R2/R3 and the determinism tension

AARM R3 is explicit that evaluating actions **in isolation, without intent context, does not satisfy it**. The pre-v1.1 assessment recorded that work as unfinished. The reference gateway now maintains session context and evaluates each action against the frozen owner-declared envelope after base policy evaluation.

The resolution is also our sharpest contribution: **read "stated intent" as a declared, machine-checkable authorization envelope, not an inferred natural-language goal.** The agent (or its owner) declares intent structurally at session start — the set of capabilities, egress tiers, sources, and endpoints the task is authorized to touch. R3 "intent alignment" then becomes: *does this action fall within the declared envelope?* — a deterministic check. R2 "context accumulation" becomes: *maintain that envelope + the prior-action trace across the session and feed it to the engine.*

This reading is implemented and CI-verified. It keeps determinism intact and remains the strict-determinism profile's defining move. It addresses intent drift and goal hijacking **at the declared-envelope boundary**; semantic drift *inside* the envelope remains out of scope by design and must not be presented as semantic coverage.

## 4. Decision-vocabulary reconciliation (R4)

| AARM | ContextBoundary | Note |
|---|---|---|
| ALLOW | allow | direct |
| DENY | deny | direct |
| STEP_UP | step_up | Human-gated, terminal until its required approval path is satisfied. |
| MODIFY | modify | Executes only a declared, deterministic, narrowing transform whose result passes base policy and the envelope. |
| DEFER | defer | Persists a condition-gated action with a resume token; it never executes until a fresh re-evaluation. |

All five decisions are implemented and CI-verified. The implementation does not itself make an AARM conformance claim.

## 5. Threat coverage (our mechanisms vs AARM's eleven)

Strong: **T1** prompt injection (payload-ignored identity/tier), **T2** confused deputy (source-trust + identity binding), **T3** data exfiltration (egress tiers + detectors — our *extension* strength), **T8** over-privileged creds (least-agency/tier), **T11** malicious tool output (egress detectors on responses + trusted_sources).

Profile-scoped limits remain: **T4** goal hijacking, **T5** memory poisoning, and **T6** intent drift are bounded by R2/R3 at the declared envelope, while semantic drift inside the envelope remains out of scope. **T9** side-channel and **T10** environmental manipulation remain deployment and threat-model concerns; T10 is partly caught by response detectors. The threat model must describe these limits rather than imply semantic coverage.

## 6. What we extend beyond AARM (the differentiation, confirmed)

- **Egress-sovereignty tiers (I/II/III).** AARM names data exfiltration (T3) as a threat but specifies no data-sensitivity/sovereignty model. Our egress tiers + escalate-only detectors are a concrete deterministic mechanism AARM leaves open. Pure extension.
- **Vendor-continuity (V1–V3).** Endpoint withdrawal / reroute-to-equal-or-stricter has no analogue in AARM at all. Entirely ours.
- **Deterministic profile.** A strict, no-model-in-path reading of R1–R6 (esp. the R3 reinterpretation) — a contribution AARM's Extended tier (R7 semantic) points *away* from.

These three are the substance of "ContextBoundary as an AARM profile" (#2).

## 7. Implementation status and review path

- **AARM Core (R1–R6): implemented and CI-verified.** The gateway implements transparent interception, session context, declared-intent evaluation, all five decisions, tamper-evident receipts, and per-agent cryptographic identity binding.
- **Strict-determinism profile:** R7 semantic distance is a designed divergence using deterministic envelope-drift counting. R8 OpenTelemetry export is implemented. R9 remains deployment-dependent for per-action credential scoping.
- **Independent review remains pending.** Production operation with real workloads and CSA TWG evidence review remain required before any AARM conformance claim. The reference gateway is verifiable from a clean clone but is not deployed as a live production service.

## 8. Historical workstreams

The R2/R3 session envelope, R4 MODIFY/DEFER outcomes, R6 per-agent identity binding, and R8 OpenTelemetry export described as future work in the original analysis are implemented and CI-verified. Remaining work is operational and evidentiary: production operation, independent CSA TWG evidence review, and the documented profile limits in §5 and §7.

## 9. Ratified status

- The deterministic reading of R3 (intent = a declared envelope) is the profile's implemented core stance.
- All AARM Core mechanisms (R1–R6) are implemented and CI-verified; this does not itself make an AARM conformance claim.
- Egress-sovereignty and vendor continuity remain profile *extensions*, not conformance items.

## Changelog
- 2026-07-26 — Ratified status update. R1–R6 implemented and CI-verified; R7 documented as a deterministic envelope-drift divergence; R8 implemented. Production operation and independent CSA TWG evidence review remain before any AARM conformance claim.
- 2026-07-11 — v1.0. Full R1–R9 + T1–T11 pre-implementation assessment against archived AARM v1.0. It recorded R2/R3/R4/R6 as unfinished and a designed divergence on R7; those historical findings were superseded by the 2026-07-26 implementation update above.
