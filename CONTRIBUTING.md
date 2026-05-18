# Contributing to ContextBoundary

ContextBoundary is an open framework. Contributions are welcome from architects, security engineers, compliance practitioners, and anyone deploying enterprise AI under regulatory constraint.

## Before You Start

Read [`FRAMEWORK.md`](FRAMEWORK.md) in full. Read the existing Audit Profile in [`audit-profiles/gdpr.md`](audit-profiles/gdpr.md) and the [`connectors/contextops-mapping.md`](connectors/contextops-mapping.md) before proposing additions. The framework's value depends on internal consistency. Contributions that drift in tone or structure cost more to review than they save.

## How to Propose Changes

1. **Open an Issue first** for any substantive contribution. Brief description of the gap you are addressing, what you propose to add or change, and what the success criteria are.
2. Wait for maintainer acknowledgment before opening a PR. This avoids wasted effort on contributions that conflict with planned roadmap items.
3. Submit a PR referencing the Issue. Keep PRs scoped — one Audit Profile, one new Endpoint Atlas entry batch, or one cross-cutting clarification per PR.

## Voice and Style

ContextBoundary documents must read as if written by the same author. Specific rules:

- **Declarative, not persuasive.** Each section opens with a flat statement of fact, not a claim of value.
- **Practice rhythm.** Each named practice or pattern follows the three-beat structure: declarative opener, what it requires, what fails without it.
- **No marketing language.** Banned words: leverage, synergy, robust, best-in-class, cutting-edge, seamlessly, empower, unlock, transformative, revolutionary, holistic, world-class, mission-critical (unless quoting a regulation).
- **No vendor naming as defaults.** Use generic labels: "frontier LLM provider," "managed cloud GPU service," "self-hosted gateway." Specific vendors appear only in the Endpoint Atlas, where classification is the deliverable.
- **No emoji in framework prose.** The 🚧 marker is permitted for forthcoming content, matching existing usage.
- **Vendor-neutral always.** The framework's credibility depends on this. If your contribution implicitly favors a vendor, it will be sent back for revision.

## Adding an Audit Profile

New Audit Profiles must follow the section order defined in [`audit-profiles/README.md`](audit-profiles/README.md). Use [`audit-profiles/gdpr.md`](audit-profiles/gdpr.md) as the structural reference.

Audit Profiles are architectural documents, not legal interpretations. Where a regulation is ambiguous, the profile should state the ambiguity rather than resolve it. Legal interpretation is the responsibility of the adopting organization.

## Adding Endpoint Atlas Entries

Each entry must include: endpoint name, default Boundary Zone, conditional zones with conditions, sovereignty notes, and Audit Profile compatibility flags. Entries are descriptive, not promotional. Marketing language in entries will be rewritten.

## Reporting Issues

Open a GitHub Issue with:
- What part of the framework is unclear, incorrect, or missing
- The use case that surfaced the issue
- Suggested resolution, if any

## Code of Conduct

Engage respectfully. Disagreement on framework design is expected and welcome. Personal attacks, vendor flame wars, and dismissive language toward other practitioners are not.

## License

By contributing, you agree that your contributions are licensed under Apache 2.0, matching the project license.
