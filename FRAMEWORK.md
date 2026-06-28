## Audit Profiles

An Audit Profile is a regulation-specific overlay on the canonical boundary diagram. Each profile constrains specific zones and crossings, defines required asset metadata, and lists evidence required for regulator audit.

v0.1 ships the GDPR Audit Profile as the reference implementation. Additional profiles will be added in future versions as the framework matures and field validation informs scope.

Each Audit Profile answers:

- Which zones are constrained by this regulation?
- Which crossings require explicit legal instruments?
- What data minimisation obligations apply at each crossing?
- What audit evidence must exist before or after a crossing?
- What is the regulator's expected audit posture?

Audit Profiles do not replace legal advice. They provide architectural responses to regulatory obligations. Legal interpretation remains the responsibility of the adopting organisation.
