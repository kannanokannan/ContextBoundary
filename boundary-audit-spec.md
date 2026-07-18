# Boundary Audit Spec (D4)

**The evidential half of the thesis: reconstruct what happened from logs + policy alone.**
Version 0.1.1 (2026-07-10) · Status: **RATIFIED** (DA-1/2/3 resolved 2026-07-10) · Author: Chanakya
Normative. Belongs in the ContextBoundary framework repo (not the gateway). Pairs with boundary-policy-spec.md v0.2.1 and boundary-conformance-scenarios.md v0.2.1.
Supersedes v0.1 DRAFT. Only change: the three open decisions are ratified and folded in — pluggable+self-describing algorithms (DA-1, DA-3), mandatory retention with audited deletion (DA-2). No mechanics changed from v0.1 beyond these.

---

## 0. Claim and guarantee level

ContextBoundary's enforcement half proves decisions are *made* correctly at the boundary. This spec covers the other half: that they can be *proven* after the fact.

> Given only the audit log for a session and the policy artifact in force, a reviewer can (a) verify the log is intact, and (b) restate the complete authority path — who acted, under whose accountability, at what tier, and which rule allowed or denied each crossing — without trusting the gateway operator.

- Guarantee level (ratified): **tamper-evident** — the log lets a verifier detect missing, reordered, truncated, or altered events (§5). Strength of "tamper-evident" is set by the configurable seal method (DA-3, §4.5).
- Correlation model (ratified): **full trace** — session / span / parent-span (§3).
- Sink model (ratified): **sink-neutral** — an emission contract + verification procedure; concrete sinks are binding profiles (§7).

## 1. Privacy invariant (hard rule)

**Audit events MUST NOT contain raw payloads, matched secret bytes, PII, or any Tier-I content.** Only classifications, identifiers, tiers, rule ids, and hashes.
- A detector records *that it fired* and the resulting tier — never the matched content.
- Consequence: the audit log carries no Tier-I content, so it is storable and shippable **without violating its own egress tiers**. A log embedding what it caught would be un-exportable.
- Replay (§6) works over recorded classifications, never raw data.

## 2. The audit event envelope

```yaml
# --- trace band (§3) ---
session_id, span_id, parent_span_id
seq:         <int, 0-based, monotonic, NO gaps>
event_type:  <session.start | discover | invoke | egress | reroute | session.seal | session.delete>

# --- chain band (§4) ---
prev_hash, event_hash
hash_alg:    <recorded per event; default sha-256; pluggable — DA-1>

# --- decision band (policy-spec fields) ---
agent_id, accountable_owner, tier_in_force, action,
decision (allow|deny|approve), rule_id (R1..R5), reason,
egress_tier_seen (I|II|III|null), detector_id (|null), obligation (|null)

# --- context band (§6) ---
policy_hash: <hash of exact policy artifact in force>
replay_inputs: <minimal privacy-safe rule inputs>
timestamp:   <RFC3339; descriptive, not the ordering key — seq is>
```

## 3. Spans and event types

Session = one agent interaction. Span = one decision. Spans form a causal tree via `parent_span_id`.

| event_type | when | parent |
|---|---|---|
| session.start | session opens; records agent, owner, tier, policy_hash | none (seq 0) |
| discover | discovery filter runs (R2) | session.start |
| invoke | an invocation is decided (R1/R2/R3) | the discover that surfaced it |
| egress | a response crossing is decided (R4) | the invoke that produced it |
| reroute | continuity reroute/deny (R5) | the invoke/egress that hit suspension |
| session.seal | session closes / checkpoint; seals + signs the chain; carries retention (§4.5) | session.start |
| **session.delete** | a sealed session is deleted at retention expiry; tombstone proving lawful deletion (DA-2, §4.6) | references the sealed session |

## 4. Chain construction (tamper-evident)

Per session, a signed hash chain.
1. **Canonical form.** Envelope minus `event_hash`, canonical JSON (sorted keys, UTF-8, no insignificant whitespace).
2. **Genesis.** seq 0: `prev_hash = H(hash_alg, "cb-audit-genesis" || session_id || policy_hash)` — binds the chain to one session + one policy version.
3. **Link.** `event_hash = H(hash_alg, canonical(envelope_without_event_hash))`. Envelope contains `prev_hash`, so each link commits to its predecessor.
4. **Continuity.** Event n's `prev_hash` == event n-1's `event_hash`.

### 4.5 Seal (DA-2, DA-3)
`session.seal` carries:
- `event_count`, and the `event_hash` of the last pre-seal event (truncation-detecting).
- **`retention` (REQUIRED — DA-2):** `{expires_at: <RFC3339>, policy: <retention-policy-id>}`. A session with no retention is **non-conformant**. Expiry is set at deploy time.
- **`seal_method` (pluggable, self-describing — DA-3):** recorded in the seal so a verifier needs no out-of-band knowledge. Default `hmac-sha256`. Available: `hmac-sha256` (baseline — detects rewrite by anyone without `K_seal`), `sig-ed25519` (asymmetric — operator cannot forge), `anchored-<method>` (seal hash published to an append-only store outside operator control — operator-independent). 
- `seal_sig`: the signature over `canonical(seal_without_event_hash)` under the chosen method's key.

Strength note: the *default* (HMAC) is what an evaluator sees, so the v1 launch claim is worded to the default — "tamper-evident against outsiders/rogue processes; operator-independent anchoring available." Making anchoring the default is a Phase-C positioning decision, not a build change (the field already supports it).

### 4.6 Deletion tombstone (DA-2)
When a sealed session reaches `expires_at`, its events may be deleted, but a `session.delete` tombstone MUST remain: `{session_id, sealed_final_hash, deleted_at, retention_policy, deletion_sig}`. This makes lawful expiry **distinguishable from suppression** — a verifier seeing a tombstone reads "legitimately expired & deleted," whereas a session that simply vanishes with no tombstone reads as a gap/truncation (§5). Deletion is itself an audited, signed act.

Algorithms (DA-1): `hash_alg` and `seal_method` are both recorded per artifact and pluggable. Default `sha-256` / `hmac-sha256`. An old log stays verifiable after defaults change, because each log declares what it used.

## 5. Verification: gap + tamper detection

Given a session's events (+ the key/public-part for the seal method):
1. **Order + gaps.** seq 0..N, no holes; exactly one session.start (seq 0) and one terminal seal (or a valid `session.delete` tombstone). Missing seq → `gap_detected`. No seal, no tombstone → `unsealed_session`.
2. **Recompute hashes.** Each `event_hash` recomputed from canonical form must match → else `event_altered`.
3. **Continuity.** Each `prev_hash` links to prior `event_hash`; genesis matches this session_id + policy_hash → else `chain_broken` / `policy_substituted`.
4. **Seal.** `event_count` + sealed final hash match; `seal_sig` verifies under the recorded `seal_method`; `retention.expires_at` present → else `seal_invalid`.
Any failure = not trustworthy for reconstruction; verifier names the reason. All pass = intact and complete (or lawfully expired, if a tombstone).

## 6. Reconstruction (replay)

Given an intact log + the policy whose hash == events' `policy_hash`:
1. For each decision span, re-evaluate the named `rule_id` against its `replay_inputs`; assert recomputed `decision` + `rule_id` match recorded → else `log_policy_divergence`. Detectors are not re-run on raw data (none present); replay consumes recorded `detector_firings`.
2. Walk the span tree in seq order, emit the authority path per span: `<agent> as <owner> at <tier> → <action> → <decision> by <rule_id> (<reason>)`, egress spans showing `egress_tier_seen` + `detector_id`.
3. Output = the reviewer's restated story, provably the one enforced under that exact policy.

**intact chain (§5) + policy replay (§6.1) ⇒ trustworthy reconstruction without trusting the operator.**

## 7. Sink neutrality + profiles

A conformant sink preserves per-session ordering, exact values, chain fields verbatim, seal + tombstone durability; must not reorder/coalesce/drop events or mutate canonical hashes.
- **Profile A — JSONL (reference, harness target):** one envelope/line, append-only, per session. System of record.
- **Profile B — Workers Analytics Engine:** map bands to blobs/doubles/indexes. Because AE samples/aggregates and may not retain every event losslessly, it **cannot be the sole sink**; it serves as a queryable mirror with JSONL (or an equivalent durable append-only sink) as the system of record.

## 8. Conformance impact (supersedes provisional S-AUD-02)

- **S-AUD-01** (extended): every decision emits a full envelope (trace+chain+decision+context bands).
- **S-AUD-02** (redefined): discover→invoke→egress reconstructs (§6) to the correct authority path; assert events, parentage, replay match.
- **S-AUD-03** (gap): drop an event → `gap_detected`.
- **S-AUD-04** (tamper): alter a field in a sealed log without re-signing → `event_altered` / `seal_invalid`.
- **S-AUD-05** (policy substitution): verify intact log vs a different policy → `policy_substituted` / `log_policy_divergence`.
- **S-AUD-06** (expiry ≠ suppression): a deleted expired session with a valid tombstone verifies as lawful; the same session with events gone and no tombstone → `gap_detected`.
Adds 4 scenarios (21 → 25).

## 9. Decisions — RATIFIED 2026-07-10
- **DA-1 hash algorithm:** default SHA-256, **pluggable + self-describing** (`hash_alg` recorded per event). Old logs stay verifiable after defaults change. (Kannan: framework must be configurable, not hardcoded.)
- **DA-2 retention:** expiry is **mandatory** — every seal carries `retention.expires_at`; a session without it is non-conformant. Deletion at expiry emits a signed `session.delete` **tombstone**, so lawful expiry is distinguishable from suppression. (Kannan: audit cannot be open-ended; must expire.)
- **DA-3 seal strength:** `seal_method` **pluggable + self-describing**, default `hmac-sha256`, with `sig-ed25519` and `anchored-*` available. Ship v1 default HMAC; anchoring designed-but-deferred; launch claim worded to the default. Making anchoring the default = Phase-C positioning decision, not a build change. (Kannan: same shape as DA-1 — defaults + configurable.)

## Non-goals (documented)
Not a SIEM / not real-time alerting. Retention *duration* is deploy-time config (the requirement that it exist is normative — DA-2). Not encryption-at-rest (§1 means no Tier-I content in the log). Detector *correctness* is proven by policy conformance, not here.

## Changelog
- 2026-07-10 — v0.1.1 RATIFIED. DA-1/2/3 folded: pluggable self-describing `hash_alg` (default sha-256) and `seal_method` (default hmac-sha256, ed25519/anchored available); mandatory `retention.expires_at` in seal; `session.delete` tombstone event so expiry ≠ suppression; S-AUD-06 added (21→25); §9 moved from open → ratified. No other mechanics changed.
- 2026-07-10 — v0.1 DRAFT. Envelope, span+parent model, signed hash chain, gap+tamper verification, policy-replay reconstruction, sink-neutral contract, privacy invariant, S-AUD redefinition, open DA-1..3.
