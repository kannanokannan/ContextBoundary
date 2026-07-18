# AARM v1.0 Conformance Gap-Analysis (roadmap #1)

**Where ContextBoundary stands against the standard we've decided to conform to (D-04).**
Version 1.0 (2026-07-11) · Author: Chanakya · Source: AARM v1.0 (CSA TWG, Errico, Feb 2026, arXiv:2602.09433, CC BY 4.0), archived at references/aarm/2026-07-11_aarm.dev_spec_v1.0.txt
Status: ratified 2026-07-17 (D-05): deterministic R3 reading accepted; v1.0 ships as Core-partial + strict-determinism profile, full Core at v1.1; egress-sovereignty and vendor-continuity confirmed as profile extensions. Feeds the positioning doc (#2), the novelty scrub (#3), and the threat model (#4).

---

## 0. Headline (read this first)

ContextBoundary is **architecturally AARM-aligned** (we are AARM's §6.1 Protocol Gateway) and our **receipts (D4) meet or exceed AARM's**, but we **do not yet meet AARM Core**. Four MUST-level requirements have real gaps: **R2 (context accumulation), R3 (intent-aligned evaluation), R4 (MODIFY + DEFER decisions), R6 (agent-identity binding)**. And one Extended requirement — **R7 semantic distance** — we **diverge from by design**, which is exactly why we are a *strict-determinism profile* and not a vanilla AARM implementation.

Bluntly: the D-04 pivot is not just a documentation reframe. Claiming AARM Core conformance (the v1.0 launch claim) requires closing R2/R3/R4/R6 — that's feature work, some of it non-trivial. The good news is that most of it aligns with things already on our roadmap or strengthens our thesis, and two of our pillars (egress-sovereignty, vendor-continuity) are clean *extensions* AARM doesn't require at all.

---

## 1. AARM v1.0 in brief

- Control plane intercepts/evaluates/decides/records every agent-initiated action **before execution**, model/framework/environment-agnostic (AARM §Abstract).
- **Nine requirements.** Core R1–R6 (MUST) = "AARM Core" conformance. Extended R7–R9 (SHOULD) = "AARM Extended."
- **Eleven threat classes** T1–T11; a conformant implementation must address all.
- **Conformance is evidence-reviewed** by the AARM TWG: satisfy the MUSTs, run their testing protocol, operate in production with real workloads, submit an evidence package (~14-day review).
- **Four reference architectures:** Protocol Gateway (§6.1), SDK Instrumentation, Kernel eBPF, Vendor Integration. **We are §6.1.**

## 2. Requirement-by-requirement

| AARM req | Our mechanism | Status | Gap / action |
|---|---|---|---|
| **R1** Pre-execution interception (MUST) | contextboundary-gw proxies all agent→tool (MCP) traffic; R1–R5 gate before execution | **CONFORM** (architecture) | Document the no-bypass deployment assumption (AARM allows this for the gateway model — it's a topology property, not a code one). |
| **R2** Context accumulation — intent + prior actions + thread, available to policy eval (MUST) | — none. Our decisions are per-request/stateless by design | **GAP (MUST)** | Accumulate session-scoped state (declared intent envelope + prior-action trace) and make it available to the policy engine at decision time. New workstream. |
| **R3** Policy eval with **intent alignment** (MUST) — actions evaluated in isolation do NOT satisfy | R1–R5 evaluate each action in isolation (identity/tier/source/egress/continuity) | **GAP / reinterpret (MUST)** | The crux (§3). Satisfiable deterministically only if "stated intent" = a **declared authorization envelope**, and the action is checked against it. Requires R2's accumulated context. This reinterpretation is our profile's defining move. |
| **R4** Five decisions ALLOW/DENY/MODIFY/STEP_UP/DEFER (MUST) | We emit **allow / deny / approve** (3) | **PARTIAL (MUST)** | Map allow→ALLOW, deny→DENY, approve→STEP_UP. **Missing MODIFY and DEFER.** DEFER ≈ our Phase-C brokering (already planned). MODIFY (execute a transformed action) is new and must be added to conform. |
| **R5** Tamper-evident receipts — action/decision/timestamp/policy-context, verifiable (MUST) | D4 audit: hash-chained, signed-sealed, replayable, retained | **CONFORM — exceeds** | D4 already surpasses R5 (chain + seal + replay + retention). This is our strongest alignment. Frame D4 as our R5 implementation. |
| **R6** Identity binding — receipt **cryptographically bound to agent identity**, non-repudiation (MUST) | Receipts carry agent_id + accountable_owner; seal signed with **gateway** key (K_seal) | **PARTIAL (MUST)** | We bind to a gateway attestation + a human accountable role, not to the **agent's own cryptographic identity**. AARM R6 wants agent-level non-repudiation. Add per-agent identity keys bound into the receipt. |
| **R7** Semantic distance tracking (SHOULD, addresses T6 drift) | — none; our thesis rejects semantic/model-in-path | **DIVERGE (by design)** | We cannot do semantic distance without importing the probabilistic element we exclude. Propose a **deterministic substitute**: envelope-drift counting (actions crossing the declared envelope), not NL semantic distance. Documented profile deviation → blocks a plain "AARM Extended" claim. |
| **R8** Telemetry export, OpenTelemetry (SHOULD) | D4 is sink-neutral (JSONL + AE profiles); trace model is session/span/parent (OTel-shaped) | **PARTIAL — easy** | Add an explicit OpenTelemetry export binding profile to D4. Low effort; our trace model already matches. |
| **R9** Least-privilege at execution (SHOULD) | A3 Least Agency, tier gates, discovery filtering (E3), trusted_sources | **CONFORM (access) / PARTIAL (creds)** | Strong on tool-access scoping. Per-action *credential* scoping (vs session-wide) is a deployment-level partial gap; document. |

## 3. The crux: R2/R3 and the determinism tension

AARM R3 is a MUST and is explicit that evaluating actions **in isolation, without intent context, does not satisfy it**. Our entire R1–R5 model evaluates actions in isolation. Taken literally, **we fail the central MUST of the standard** — and the obvious way to "fix" it (infer whether an action matches a natural-language intent) is precisely the model-in-the-path, probabilistic evaluation our strict-determinism thesis exists to avoid. This is the real collision, deeper than any naming overlap.

The resolution is also our sharpest contribution: **read "stated intent" as a declared, machine-checkable authorization envelope, not an inferred natural-language goal.** The agent (or its owner) declares intent structurally at session start — the set of capabilities, egress tiers, sources, and endpoints the task is authorized to touch. R3 "intent alignment" then becomes: *does this action fall within the declared envelope?* — a deterministic check. R2 "context accumulation" becomes: *maintain that envelope + the prior-action trace across the session and feed it to the engine.*

This is defensible against AARM's own text (R3 says "aligned with stated intent," not "inferred intent"), it keeps determinism intact, and it *is* the strict-determinism profile D-04 committed us to. But it is not free: it requires building session-scoped state and an intent-declaration format we don't currently have. It also honestly means we address the intent-drift/goal-hijack threats (T4/T6) **only within the declared envelope** — semantic drift *inside* the envelope is out of scope for us by design, and the threat model (#4) must say so plainly rather than paper over it.

## 4. Decision-vocabulary reconciliation (R4)

| AARM | ContextBoundary | Note |
|---|---|---|
| ALLOW | allow | direct |
| DENY | deny | direct |
| STEP_UP | approve (record-and-return obligation, §3.8) | semantics differ slightly — AARM STEP_UP resumes after approval; ours is terminal record-and-return until brokering (Phase C) |
| MODIFY | — | **missing.** Execute a transformed action (e.g., strip a field, downgrade scope). Can be deterministic; new work. |
| DEFER | — | **missing.** Delay pending context ≈ our planned Phase-C brokering. |

To conform, the engine must be *capable* of all five. MODIFY is the genuinely new one; DEFER folds into brokering we'd already scheduled.

## 5. Threat coverage (our mechanisms vs AARM's eleven)

Strong: **T1** prompt injection (payload-ignored identity/tier), **T2** confused deputy (source-trust + identity binding), **T3** data exfiltration (egress tiers + detectors — our *extension* strength), **T8** over-privileged creds (least-agency/tier), **T11** malicious tool output (egress detectors on responses + trusted_sources).

Partial / gap, and they cluster around the same root (no session-intent context + no semantic tracking): **T4** goal hijacking, **T5** memory poisoning, **T6** intent drift, **T9** side-channel, **T10** environmental manipulation. T4/T5/T6 need R2/R3; T9 is a documented-hard problem AARM also struggles with; T10 partly caught by response detectors. The threat model (#4) should present these as **profile-scoped**: we defend the envelope deterministically and explicitly scope out in-envelope semantic drift, rather than claim semantic coverage we don't have.

## 6. What we extend beyond AARM (the differentiation, confirmed)

- **Egress-sovereignty tiers (I/II/III).** AARM names data exfiltration (T3) as a threat but specifies no data-sensitivity/sovereignty model. Our egress tiers + escalate-only detectors are a concrete deterministic mechanism AARM leaves open. Pure extension.
- **Vendor-continuity (V1–V3).** Endpoint withdrawal / reroute-to-equal-or-stricter has no analogue in AARM at all. Entirely ours.
- **Deterministic profile.** A strict, no-model-in-path reading of R1–R6 (esp. the R3 reinterpretation) — a contribution AARM's Extended tier (R7 semantic) points *away* from.

These three are the substance of "ContextBoundary as an AARM profile" (#2).

## 7. Conformance verdict + path

- **AARM Core (R1–R6): NOT YET.** Blocking gaps: R2 (build), R3 (build the deterministic-intent envelope; depends on R2), R4 (add MODIFY + DEFER), R6 (agent-identity binding). R1/R5 already conform.
- **AARM Extended (R1–R9): will not claim as-written.** R7 semantic is a designed divergence. We instead publish a **strict-determinism profile** that conforms to Core and substitutes deterministic mechanisms for R7, adds R8 (OTel, easy), and meets R9.
- **Evidence-reviewed conformance** (the TWG process, ~14 days, needs production operation with real workloads) lines up with our dogfood phase — the evidence package is a natural output of roadmap #13, not extra work.

## 8. Roadmap impact (feeds ROADMAP v3)

New/'changed workstreams implied:
1. **Intent-envelope + session context (R2+R3)** — the big one. New spec + build. Chanakya specs, Hanuman builds. This becomes the defining feature of the determinism profile.
2. **MODIFY + DEFER decisions (R4)** — extend the decision vocabulary; DEFER reuses Phase-C brokering.
3. **Agent-identity binding (R6)** — per-agent keys into receipts; extends D4.
4. **OTel export profile (R8)** — small D4 addition.
5. **Positioning doc (#2)** now writes itself from §3/§6/§7 here.
6. **Threat model (#4)** maps to T1–T11 with §5's honest profile-scoping.
Net effect: v1.0 (M3) scope grows. The AARM-conformance claim is now gated on R2/R3/R4/R6 landing before dogfood, or we ship v1.0 claiming **Core-partial + documented profile** and target full Core at v1.1. That's a decision for you (§9).

## 9. For ratification
- **Accept the deterministic reading of R3** (intent = declared envelope) as our profile's core stance? This is the load-bearing interpretation; everything else follows.
- **v1.0 conformance target:** full AARM Core (adds R2/R3/R4/R6 to the critical path, likely slips M3), OR ship v1.0 as "AARM-Core-partial + strict-determinism profile, full Core at v1.1" (holds M3, honest, weaker claim)?
- **Confirm** egress-sovereignty + continuity are positioned as profile *extensions*, not conformance items.

## Changelog
- 2026-07-11 — v1.0. Full R1–R9 + T1–T11 gap-analysis against archived AARM v1.0. Verdict: architecturally aligned, receipts exceed, four MUST gaps (R2/R3/R4/R6), designed divergence on R7. Roadmap impact flagged; three ratification questions raised.
