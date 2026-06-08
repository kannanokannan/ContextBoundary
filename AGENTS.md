# ContextBoundary Agent Instructions

Read `CLAUDE.md` before changing this repository. It is the canonical agent briefing for ContextBoundary.

## Locked Rules

- Use "Egress Tier", not "Privacy Tier".
- Tier I is most restrictive: data never leaves the system.
- Tier II permits anonymised aggregates only, with consent or documented basis.
- Tier III is least restrictive: explicit per-call API escalation.
- Do not invert tier numbering.
- Do not add tool-specific implementation details to `FRAMEWORK.md`; it is a specification, not a product doc.
- Keep ContextBoundary deployment-agnostic. Do not frame it as cloud-only or on-prem-only.
- Treat Audit Profiles as overlays. They do not replace the underlying regulation.
- MCP is an integration layer, not the governance authority.
- Do not create a separate `CapabilityOps` framework.

## Responsibility Split

- ContextOps owns accountability: ownership, roles, trust classification, lifecycle, governance metadata, and context source accountability.
- ContextBoundary owns boundary enforcement: discovery control, capability visibility, MCP filtering, invocation control, approval workflows, egress control, and auditing.

## Cross-Project References

Before introducing any new term, check:

https://github.com/kannanokannan/context-stack/blob/main/GLOSSARY.md

Before making any cross-project decision, check:

https://github.com/kannanokannan/context-stack/blob/main/DECISIONS.md
