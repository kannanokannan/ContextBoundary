# Boundary Policy Schema (D1)

**ContextBoundary — machine-checkable policy vocabulary.**
Version: 0.2.2 (2026-09-20) · Status: §8 ratified 07-08; egress-tier direction corrected to canon 07-09; **decision vocabulary §3.9 added 09-20** · License: Apache 2.0

Supersedes v0.2.1. v0.2.1's only substantive change over v0.2 was the egress-tier correction (§3.2, §3.4, §3.7, §4/R4, examples) — v0.2 inverted the canonical tier direction. v0.2.2 adds §3.9 (decision vocabulary, remediation transforms, deferral) and aligns §3.6 and R4 with it. All other v0.2 content (detectors, obligation shape, domain-reserved, wildcard rejection) is unchanged.

This is the compilable form of the framework: it expresses agent-authority (A1–A4), enforcement (E1–E3), vendor-continuity (V1–V3), and egress tiers as one declarative document a gateway can evaluate. It defines meaning and shape only — not the engine. Engine (D-03): OPA/Rego; the schema stays engine-neutral and a compiler emits Rego from boundary-policy.yaml.

---

## 0. CANON ALIGNMENT — egress tier direction (read first)

Per the locked framework (context-stack GLOSSARY, restated in ContextBoundary CLAUDE.md/FRAMEWORK.md/README.md):

| Tier | Rule | Protection |
|---|---|---|
| **I** | never leaves the system (sovereign data, PII, secrets) | **most** restrictive |
| **II** | anonymised aggregates only, with consent (derived insights) | mid |
| **III** | explicit per-call escalation (public / sanctioned lookups) | **least** restrictive |

**Lower number = higher protection. DO NOT INVERT.** v0.1/v0.2 had this backwards (I=freely-crossable). Corrected throughout v0.2.1.

To remove all direction ambiguity in compilation, define a **protection ordinal** p: p(I)=3, p(II)=2, p(III)=1 (higher p = more protected). All egress comparisons below are stated in p.

---

## 1. Design constraints

- Deterministic: same policy + same request → same decision, always. No model in the path.
- Deploy-time compilable: policy compiles to lookup structures at deploy, not interpreted per request (edge CPU-ms limit).
- Closed by default: anything not permitted is denied (Least Agency, A3).
- Explainable: every decision names the rule that produced it (feeds audit, D4).
- Fail-safe classification: a content detector may only ever raise protection (toward Tier I), never lower it; uncertainty escalates (§3.7).

## 2. Top-level shape

```yaml
apiVersion: contextboundary/v0
kind: BoundaryPolicy
metadata:
  name: <string>
  description: <string>
  default_decision: deny        # deny | approve  (never "allow"; A3 least agency)

tiers:             # autonomy tiers, ordered least->most (A2)
egress_tiers:      # data sensitivity zones, CANON: I most protected .. III least (§0)
egress_detectors:  # escalate-only content detectors (§3.7)
identities:        # agents + their grants (A1, A2, A4)
capabilities:      # tools/MCP surfaces + their requirements (E2, E3, egress)
endpoints:         # upstream model/compute endpoints for continuity (V1-V3)
audit:             # what each crossing must emit (D4)
```

## 3. Blocks

### 3.1 tiers (A2, A3)
Ordered list; index = authority level. Discovery/invocation compare against these.
```yaml
tiers:
  - id: T0            # narrate-only: no tool action
  - id: T1            # bounded read/act on trusted, low-egress tools
  - id: T2            # act on tier-II data, still within approved set
  - id: T3            # bounded autonomous within an approved envelope
```
Rule of meaning: a grant of Tn implies Tn and below. Escalation is explicit (a grant), never implicit.
Q1 ratified: single global ordinal ladder (T0<T1<T2<T3). `domain` is **reserved for v1**; validators reject `domain` under contextboundary/v0.
NB: autonomy tiers (T-series) and egress tiers (roman) are independent axes with opposite intuitions — T higher = more authority; egress lower number = more protection. Keep them lexically distinct (T0..T3 vs I/II/III) to avoid the collision that caused the v0.2 error.

### 3.2 egress_tiers (CANON direction — §0)
```yaml
egress_tiers:
  - id: I             # never leaves the system            (protection p=3, MOST restrictive)
  - id: II            # anonymised aggregates only, w/consent (p=2)
  - id: III           # explicit per-call escalation        (p=1, LEAST restrictive)
```
A capability's `egress_tier` is the **sensitivity of the data it can surface** (a tool that can surface secrets/PII is Tier I). A crossing declares a **ceiling** = the most-protected tier permitted to cross it; a "Tier-II crossing" permits II and III (less protected) but not I.

### 3.3 identities (A1 accountable owner, A2 tier, A4 supply)
```yaml
identities:
  - id: agent:example-runbot
    accountable_owner: role:run-lead        # A1 — REQUIRED; policy invalid without it
    autonomy_tier: T1                        # A2
    trusted_sources: [mcp:self, mcp:vendor-x]# A4 — sources this agent may consume (exact IDs)
```
Validation:
- No `accountable_owner` → schema validation fails (E1 has nothing to bind).
- Q2 ratified: `trusted_sources` = exact source IDs only; no wildcards/patterns. Silent trust expansion is the failure A4 exists to prevent.
- Empty `trusted_sources` → validator **warning** (not block): agent can discover nothing — legal but almost certainly a mistake.

### 3.4 capabilities (E2 tier gate, E3 source filter, egress sensitivity)
```yaml
capabilities:
  - id: tool:close-ticket
    source: mcp:self                         # A4 — which supply it comes from
    required_tier: T1                        # E2 — min autonomy tier to invoke
    discover_min_tier: T1                    # E3 — min autonomy tier to even see it
    egress_tier: II                          # sensitivity FLOOR of data it surfaces (§0, §3.7)
    requires_approval: false
```
Discovery (E3): a capability is returned at `server/discover`/`tools/list` only if agent.tier ≥ discover_min_tier AND capability.source ∈ agent.trusted_sources (exact match). Otherwise invisible — not denied-on-use, invisible.

### 3.5 endpoints (V1 class, V2 fallback, V3 suspension)
```yaml
endpoints:
  - id: endpoint:primary
    continuity_class: export-exposed         # V1 — withdrawable by 3rd party
    zone: II
    fallbacks: [endpoint:secondary]          # V2 — must be pre-classified
  - id: endpoint:secondary
    continuity_class: domestic
    zone: II
    fallbacks: []
```
Reroute rule (V3): on suspension, route only to a fallback whose `zone` is equal-or-stricter (equal-or-higher protection, p). None → deny + audit. Never silent.
(Endpoint `zone` uses the same egress-tier protection direction as §0: a stricter zone = more protected = lower number.)

### 3.6 audit (D4)
```yaml
audit:
  emit:
    - agent_id
    - accountable_owner
    - tier_in_force
    - action
    - decision            # allow | deny | approve | modify | defer  (§3.9)
    - rule_id             # which rule decided
    - egress_tier_seen    # effective sensitivity after detectors (§3.7)
    - detector_id         # present when a detector escalated (null otherwise)
    - obligation          # present on approve decisions (§3.8)
    - timestamp
  sink: <deploy-time binding>
```

### 3.7 egress_detectors (Q4 ratified: hybrid; CANON direction)

Declared `egress_tier` on a capability is the **floor** (least protection assumed). Detectors are a small enumerated set of content checks that may **escalate protection only** — raise the effective tier toward I, never toward III. Uncertain match → escalate. Least agency applied to classification.

```yaml
egress_detectors:
  - id: det:credential-pattern
    description: key/credential shapes (private-key headers, token prefixes, secret-like assignments)
    match: <bounded pattern set — regex/shape, deploy-time compiled>
    min_tier: I          # any match raises effective tier to at least Tier I (most protected)
  - id: det:pii-shape
    description: known PII shapes (national ID formats, card-number shapes)
    match: <bounded pattern set>
    min_tier: I
```

Semantics (in protection ordinal p, higher = more protected):
- effective_p(response) = max( p(capability.egress_tier), max( p(det.min_tier) for each matching detector ) )
- Detectors run on response payloads at the crossing, deterministically (bounded regex/shape — no model in path).
- A match must be auditable: emit `detector_id` alongside `egress_tier_seen`.

**Explicit non-goal (ratified):** v0 detectors are a small explicit allowlist, NOT general DLP. Gaps beyond the set are known and documented, not hidden. Expanding the set is a versioned policy change.

**Explicit non-goal (ratified, D-A 2026-09-18):** v0 detectors classify **response** payloads only. Request payloads — the arguments and prompts sent outward — are not classified by this specification. An invocation is authorized by R1–R3 before dispatch; its content is not inspected. Implementations MUST NOT claim that outbound request content is classified under v0.

### 3.8 Approval obligation shape (Q3 ratified: record-and-return)

`approve` is a **terminal, audited decision**: the gateway records the requirement and stops; fulfilment is out-of-band. Brokering (hold-and-resume) is Phase C — spec'd, not built, excluded from the stateless v0 path.

```yaml
obligation:
  approval_required: true
  approver_role: <role id>      # who may fulfil (e.g. the identity's accountable_owner or a named role)
  reason: <string>              # e.g. tier_escalation
  rule_id: <R1..R5>             # which rule produced the requirement
```
Emitted in the audit record and returned in the structured response to the caller. The boundary's claim is evidential: "this needed approval, here is the evidence."

### 3.9 Decision vocabulary, remediation transforms and deferral

A decision is one of **five** values. `allow`, `deny` and `approve` are described above; `modify` and `defer` are described here. This enumeration is closed: an implementation MUST NOT emit a decision outside it.

| Decision | Meaning | Action executes? |
|---|---|---|
| `allow` | Permitted as submitted | yes |
| `deny` | Refused; the deciding rule is named | no |
| `approve` | Recorded and returned for a named role; terminal in v0 (§3.8) | no |
| `modify` | A declared transform narrowed the action; the narrowed form proceeds | yes, as transformed |
| `defer` | A declared condition is unmet; the action is held against a resume token | not yet |

#### Remediation transforms

Transforms are declared under `remediation` and bound to a **decision outcome**, not to a capability — a rule matches on `rule_id`, `reason`, and optionally `detector_id`.

```yaml
remediation:
  transforms:
    - id: strip_field
      path: [payload, legacy_credential]
    - id: redact_match
      path: [payload, note]
      pattern: '(password|credential|secret)\s*[=:]\s*[^\s]+'
    - id: clamp_tier
      path: [payload_egress_tier]
      ceiling: I
  rules:
    - rule_id: R4
      reason: egress_violation
      detector_id: det:credential-pattern
      transforms: [strip_field, redact_match, clamp_tier]
```

The v0 transform set is a closed allowlist of three. `strip_field` removes the value at `path`. `redact_match` replaces every match of `pattern` at `path` with `[REDACTED]`, matching case-insensitively and globally. `clamp_tier` lowers the egress tier at `path` to `ceiling`, and applies only when `ceiling` is at least as protected as the current value.

**Two invariants govern every transform.**

1. **Narrowing only.** A transform MUST NOT widen what the action exposes. `strip_field` and `redact_match` narrow by construction; `clamp_tier` applies only in the protective direction. This is the mirror of §3.7: detectors may only raise protection, transforms may only reduce exposure. Neither may move the other way.
2. **No silent no-ops.** If the canonical form of the transformed action is identical to the canonical form of the original, the transform MUST be treated as not applied and the decision MUST NOT be `modify`. A `modify` decision therefore always denotes a real change to the action.

A `modify` decision carries `transform_id`, `original_action_hash` and `resulting_action_hash`, the last two being SHA-256 over the canonical form of the action before and after. A reviewer can therefore prove which transform ran and that the action changed, without the payload appearing in the record.

#### Deferral

A deferral holds an otherwise-permitted action until a named condition is satisfied. Deferral is evaluated only when the action would otherwise be allowed.

```yaml
  defer_rules:
    - id: wait-for-source-ready
      action_type: invoke
      capability: triage-alert
      when: { path: [payload, source_ready], equals: false }
      resume_condition: source-ready
      resume_path: [payload, source_ready]
```

A matching rule produces `decision: defer` with a `resume_token` and a `defer_reason` naming the rule. The held action is durable: it is not executed, and it is not discarded. Resumption MUST supply both the session and the resume token, and the satisfied condition's identifier MUST equal the rule's `resume_condition` — a condition with any other identifier MUST NOT resume the action. On resumption the value at `resume_path` is set and the action is re-evaluated from the beginning; deferral is not a stored authorization.

**Scope note.** This section documents behaviour present in the reference gateway. `modify` is exercised by the conformance corpus (S-R4-04); `defer` is exercised only by the gateway's own R4 suite and by no scenario in that corpus. In the reference gateway both decisions are reachable only on the session-bound path — an unsessioned request yields `allow`, `deny` or `approve`. This section was added in 2026-09 after two independent implementability reviews found the decision vocabulary under-specified; the implementation was already emitting `modify` and `defer`.

## 4. The five canonical rules (decision order)

Evaluated top-down; first match that denies wins; closed by default.

1. **R1 Identity (E1/A1):** action lacks a bound `accountable_owner` → **deny**(unbound_identity).
2. **R2 Discovery + source trust (E3/A4):** at discovery, return only capabilities where tier ≥ discover_min_tier AND source ∈ trusted_sources. **At invocation, the source-trust predicate is re-checked first:** source ∉ trusted_sources → **deny**(untrusted_source) before R3 is reached. (Locks the S-R2-04 / S-R3-04 precedence — see conformance v0.2.1.)
3. **R3 Tier (E2/A2):** invoke where agent.tier < capability.required_tier → **approve**(tier_escalation) with obligation (§3.8) if approvable, else **deny**. Tier is taken from the policy binding, never from payload.
4. **R4 Egress (CANON):** effective_p(response) — declared floor escalated by detectors (§3.7) — **exceeds the crossing ceiling's protection** → **deny**(egress_violation), unless a declared remediation transform (§3.9) narrows the action, in which case → **modify**(transform_applied). audit carries detector_id when a detector caused the escalation. (i.e. data more protected than the crossing permits.)
5. **R5 Continuity (V3):** target endpoint suspended → reroute to equal-or-stricter (equal-or-higher-p) fallback; none → **deny**(no_fallback).

These five are the conformance backbone (D3). With §8 ratified and the tier direction corrected, S-R4-03 is must-pass (see conformance v0.2.1).

## 5. Worked example A — permissive dev policy
```yaml
apiVersion: contextboundary/v0
kind: BoundaryPolicy
metadata: {name: dev, description: loose local, default_decision: approve}
tiers: [{id: T0},{id: T1},{id: T2}]
egress_tiers: [{id: I},{id: II},{id: III}]     # canon: I most protected
egress_detectors: []
identities:
  - id: agent:dev
    accountable_owner: role:developer
    autonomy_tier: T2
    trusted_sources: [mcp:self]
capabilities:
  - {id: tool:read-public, source: mcp:self, required_tier: T0, discover_min_tier: T0, egress_tier: III, requires_approval: false}  # surfaces public data (least sensitive)
  - {id: tool:write-file,  source: mcp:self, required_tier: T1, discover_min_tier: T1, egress_tier: II,  requires_approval: false}
endpoints: []
audit: {emit: [agent_id, accountable_owner, tier_in_force, action, decision, rule_id], sink: console}
```

## 6. Worked example B — strict prod policy
```yaml
apiVersion: contextboundary/v0
kind: BoundaryPolicy
metadata: {name: prod, description: strict run estate, default_decision: deny}
tiers: [{id: T0},{id: T1},{id: T2},{id: T3}]
egress_tiers: [{id: I},{id: II},{id: III}]     # canon: I most protected
egress_detectors:
  - {id: det:credential-pattern, description: key/credential shapes, match: bounded-pattern-set, min_tier: I}
identities:
  - id: agent:run-l1
    accountable_owner: role:run-lead-apac
    autonomy_tier: T1
    trusted_sources: [mcp:self]
capabilities:
  - {id: tool:triage-alert, source: mcp:self,   required_tier: T1, discover_min_tier: T1, egress_tier: II, requires_approval: false}
  - {id: tool:apply-change, source: mcp:self,   required_tier: T3, discover_min_tier: T2, egress_tier: II, requires_approval: true}
  - {id: tool:read-secrets, source: mcp:vendor, required_tier: T3, discover_min_tier: T3, egress_tier: I,  requires_approval: true}  # CANON: secrets = Tier I (was III in v0.1/v0.2)
endpoints:
  - {id: endpoint:primary,   continuity_class: export-exposed, zone: II, fallbacks: [endpoint:secondary]}
  - {id: endpoint:secondary, continuity_class: domestic,       zone: II, fallbacks: []}
audit: {emit: [agent_id, accountable_owner, tier_in_force, action, decision, rule_id, egress_tier_seen, detector_id, obligation, timestamp], sink: analytics-engine}
```
In B, agent:run-l1 (T1): cannot see `read-secrets` (discover_min_tier T3 + source not trusted); can see but not invoke `apply-change` without approval (needs T3); can use `triage-alert`. And if any capability's response contains a credential-shaped payload, det:credential-pattern raises effective protection to Tier I (p3); crossing a Tier-II ceiling (p2) → R4 denies. That is the whole thesis in one policy.

## 7. JSON Schema (validation, abridged)
```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "type": "object",
  "required": ["apiVersion","kind","metadata","tiers","identities","capabilities"],
  "properties": {
    "apiVersion": {"const": "contextboundary/v0"},
    "kind": {"const": "BoundaryPolicy"},
    "metadata": {"type":"object","required":["name","default_decision"],
      "properties":{"default_decision":{"enum":["deny","approve"]}}},
    "tiers": {"type":"array","minItems":1,"items":{"type":"object","required":["id"],
      "properties":{"domain":false}}},
    "egress_tiers": {"type":"array","items":{"type":"object","required":["id"],
      "properties":{"id":{"enum":["I","II","III"]}}}},
    "egress_detectors": {"type":"array","items":{"type":"object",
      "required":["id","match","min_tier"],
      "properties":{"min_tier":{"enum":["I","II","III"]}}}},
    "identities": {"type":"array","items":{"type":"object",
      "required":["id","accountable_owner","autonomy_tier"],
      "properties":{"trusted_sources":{"type":"array","items":{"type":"string","pattern":"^[^*?]+$"}}}}},
    "capabilities": {"type":"array","items":{"type":"object",
      "required":["id","source","required_tier","discover_min_tier","egress_tier"],
      "properties":{"egress_tier":{"enum":["I","II","III"]}}}},
    "endpoints": {"type":"array","items":{"type":"object",
      "required":["id","continuity_class","zone"]}}
  }
}
```
Notes:
- `default_decision` excludes `allow` — closed-by-default is structural.
- `tiers[].domain` disallowed: reserved for v1 (Q1).
- `trusted_sources` items reject wildcard characters (Q2).
- Empty `trusted_sources` → validator warning (Q2); not expressible in JSON Schema — reference validator implements it.
- Protection-ordinal direction (§0) is a compiler concern, not schema — the compiler MUST map I→p3, II→p2, III→p1 (highest protection = lowest roman). Do not derive p from array index.

## 8. Resolved design questions (ratified 07-08) — unchanged from v0.2
- Q1 tiers: global ordinal; `domain` reserved for v1. (§3.1)
- Q2 trusted_sources: exact IDs only; warn on empty. (§3.3)
- Q3 approval: record-and-return terminal decision + obligation; brokering Phase C. (§3.8)
- Q4 egress: hybrid — declared floor + escalate-only detectors; escalate on uncertainty; NOT general DLP. (§3.7)

## Changelog
- 2026-09-20 — **v0.2.2.** §3.9 added: the closed five-value decision vocabulary, the three-transform remediation allowlist (`strip_field`, `redact_match`, `clamp_tier`) with its narrowing-only and no-silent-no-op invariants, and deferral. §3.6 decision enum and R4 updated to match. The section documents behaviour the reference gateway already implemented and no specification stated; the version bump and this entry were omitted when it was added and are recorded here. Scope note corrected: `defer` is exercised by no scenario in the conformance corpus.
- 2026-07-09 — **v0.2.1. Egress-tier direction corrected to canon (context-stack GLOSSARY / ContextBoundary CLAUDE.md: Tier I most restrictive, DO NOT INVERT).** v0.1 and v0.2 inverted it (I=freely-crossable) — an implementation error flagged by the 07-09 build log (ambiguity #2). Added §0 protection ordinal to fix direction unambiguously; §3.2 relabelled; capability.egress_tier reinterpreted as sensitivity floor; §3.7 detectors escalate toward Tier I (credential min_tier I, not III); R4 restated as "data more protected than crossing ceiling → deny"; Example B read-secrets → Tier I, detector → Tier I; compiler must map I→p3 explicitly. All other v0.2 content unchanged. This update replaces the v0.2/v0.1 files currently in docs/.
- 2026-07-09 — v0.2 (superseded). §8 folded in: egress_detectors, obligation shape, domain reserved, wildcard rejection. Contained the tier inversion.
- 2026-07-08 — v0.1 (superseded). Full shape, 5 rules, 2 examples, JSON Schema. Contained the tier inversion.
