# GDPR Audit Profile

**Regulation:** General Data Protection Regulation (EU) 2016/679
**Effective:** 25 May 2018
**Jurisdictional Scope:** Processing of personal data of natural persons in the European Union, regardless of where the processing occurs

This profile overlays the canonical ContextBoundary architecture with the constraints, mappings, and evidence requirements imposed by GDPR. It is descriptive of architectural response to the regulation; it is not legal interpretation.

## 1. Regulation Summary

GDPR governs the processing of personal data of natural persons located in the European Union. It applies to any organization processing such data, whether the organization is established in the EU or elsewhere. The regulation defines roles of controller (the entity that determines the purposes of processing) and processor (the entity that processes data on behalf of a controller). It imposes obligations including lawful basis for processing, data minimization, accuracy, storage limitation, integrity, confidentiality, and accountability.

Articles most relevant to AI deployment:

- **Article 5** — Principles of processing, including data minimization and accuracy
- **Article 6** — Lawful basis for processing
- **Article 9** — Special categories of personal data (health, biometric, racial, political, etc.)
- **Article 22** — Automated decision-making and profiling; right to human review
- **Article 25** — Data protection by design and by default
- **Article 28** — Processor obligations and Data Processing Agreements
- **Article 30** — Records of Processing Activities (ROPA)
- **Article 32** — Security of processing
- **Article 33-34** — Breach notification
- **Article 35** — Data Protection Impact Assessment (DPIA)
- **Article 44-49** — Transfers to third countries

Enforcement is conducted by supervisory authorities in each member state, coordinated through the European Data Protection Board. Maximum administrative fines: the higher of €20 million or 4% of global annual turnover.

## 2. Mandatory Boundary Constraints

Under the GDPR Profile, the canonical boundary diagram is constrained as follows:

### Customer Sovereign Zone
- Must reside within the EU or an adequacy-decision jurisdiction unless transfer mechanisms (Article 46) are documented and active.
- Must operate under controller obligations including ROPA maintenance, DPIA where applicable, and breach notification procedures.

### AMS / Service Vendor Zone
- May process personal data only as a processor under an Article 28-compliant Data Processing Agreement.
- DPA must specify: subject matter, duration, nature, purpose, type of personal data, categories of data subjects, controller obligations.
- Sub-processor relationships require explicit controller authorization.
- Where the AMS provider is located outside the EU, transfer mechanisms apply at this zone boundary.

### Product Vendor Zone
- Processor status with Article 28 DPA required for any personal data processing.
- Where the product vendor is located outside the EU, transfer mechanisms apply.
- Vendor-side data retention must not exceed customer-defined retention periods.

### Compute Vendor Zone
- Custody arrangements involving personal data require explicit data residency commitments within EU or adequacy-decision jurisdictions, or active SCCs / transfer mechanisms.
- Encryption-at-rest with customer-controlled keys is recommended; vendor-controlled keys require additional risk assessment.

### LLM Vendor Zone
- Personal data transit to LLM Vendor zones operated outside the EU requires Standard Contractual Clauses (Module 2 — controller to processor, or Module 3 — processor to processor as applicable).
- Transfer Impact Assessment (TIA) is required where the destination is not subject to an adequacy decision.
- Special category data (Article 9) may not transit to LLM Vendor zones outside adequacy-decision jurisdictions without exceptional justification and additional safeguards.
- Training of LLM provider's own foundation models on customer personal data is prohibited unless explicit, freely-given consent has been obtained from each data subject.

### Prohibited Crossings Under GDPR Profile
- Personal data crossing to any vendor zone in a non-adequacy jurisdiction without an active transfer mechanism.
- Special category data crossing to a non-EU LLM Vendor zone without exceptional safeguards.
- Personal data crossing to any zone that retains the data beyond the controller-defined retention period.
- Automated processing producing legal effects on data subjects without Article 22-compliant human review at the Customer Sovereign Zone output stage.

## 3. Privacy Tier Mapping

| GDPR Category | ContextBoundary Tier | Notes |
|---|---|---|
| Anonymous data (truly irreversible) | Tier I | Anonymization must be irreversible; pseudonymization is not anonymization. |
| Pseudonymized personal data | Tier II | Remains personal data under GDPR; treated as Tier II with redaction policies. |
| Personal data (Article 4) | Tier II minimum | May escalate to Tier III based on context and sensitivity. |
| Special categories (Article 9) | Tier III | Health, biometric, racial, political, religious, sexual orientation, trade union, genetic. |
| Criminal conviction data (Article 10) | Tier III | Subject to additional safeguards under member state law. |
| Children's data (Article 8) | Tier III | Heightened protection regardless of category. |

Where a payload contains data of mixed categories, the highest applicable Tier governs the entire payload until the higher-Tier component is irreversibly removed.

## 4. Required Asset Metadata

Every Context Asset processed under the GDPR Profile must record:

- **Lawful basis** — One of: consent, contract, legal obligation, vital interests, public task, legitimate interests (Article 6).
- **Special category lawful basis** — Where Article 9 applies, the specific Article 9(2) condition.
- **Controller / processor designation** — Whether the customer organization is acting as controller or processor for this asset.
- **Data subject categories** — Customers, employees, prospects, etc.
- **Personal data categories** — Identifiers, contact data, behavioral data, special category data, etc.
- **Retention period** — Maximum duration the asset will be processed before deletion or anonymization.
- **Cross-border transfer mechanism** — Where applicable: adequacy decision, SCCs, Binding Corporate Rules, derogation under Article 49.
- **Source of personal data** — Whether collected directly from the data subject or from another source (Article 14 applies for non-direct collection).
- **DPIA reference** — Where Article 35 applies, the identifier of the completed DPIA.

This metadata is attached to the asset record and reviewed during refresh cycles. Where ContextOps is in use, this metadata extends the standard Context Asset schema.

## 5. Evidence Requirements

A regulator audit under GDPR may demand the following artifacts. The architecture must support their production:

- **Records of Processing Activities (Article 30)** — Comprehensive list of processing activities, controllers, processors, data categories, retention, transfers. Often maintained as a ROPA register or equivalent.
- **Data Protection Impact Assessment (Article 35)** — Required for high-risk processing including profiling and large-scale processing of special category data. Must document the processing, necessity assessment, risks, and mitigation measures.
- **Transfer Impact Assessment** — Required for transfers under SCCs to non-adequacy jurisdictions. Documents the legal regime, surveillance laws, and supplementary measures of the destination jurisdiction.
- **Data Processing Agreements** — Article 28-compliant DPAs for every processor and sub-processor relationship.
- **Audit logs** — Records of access to personal data, with timestamp, user identity, asset accessed, purpose. Logs must be tamper-evident.
- **Breach notification records** — Records of personal data breaches, with timeline of detection, containment, notification to supervisory authority (within 72 hours where required), and notification to data subjects where applicable.
- **Consent records** — Where consent is the lawful basis, evidence that consent was freely given, specific, informed, and unambiguous, including the ability to withdraw consent as easily as it was given.
- **Records of right exercises** — Subject access requests, rectification, erasure, restriction, portability, objection (Articles 15-22). Records of how each request was handled and within statutory deadlines.

## 6. Conflict with Other Profiles

### GDPR vs DPDP (India)
Both regulations impose similar processor and consent obligations, but their adequacy frameworks differ. EU data flowing to India is not automatically permitted under GDPR adequacy; transfer mechanisms remain required. Indian data flowing to the EU is governed by DPDP cross-border provisions and may face additional restrictions.

### GDPR vs HIPAA
GDPR and HIPAA both protect health data but use different definitions and obligations. A US healthcare provider processing data of EU residents is bound by both. The intersection requires a BAA for HIPAA *and* DPA for GDPR, both transfer mechanisms (SCCs in this direction), and the more restrictive of the two regulations' obligations on each dimension.

### GDPR vs EU AI Act
The EU AI Act adds documentation, transparency, and oversight requirements on top of GDPR for high-risk AI systems. There is no conflict; the AI Act overlays GDPR with additional obligations. Audit Profiles for both apply simultaneously to in-scope AI systems.

### GDPR vs Retention Requirements (Sectoral)
Financial services and other sectors have statutory minimum retention periods that may exceed what GDPR data minimization principles would otherwise require. Where this conflict arises, the sectoral retention requirement typically prevails, provided the lawful basis is legal obligation under Article 6(1)(c). The conflict must be documented in the ROPA.

## 7. Worked Application

Consider an EU-based insurance company deploying an AI agent to summarize customer correspondence for claims processing. The agent ingests email and call transcripts, retrieves policy documents from a vector store, and generates structured summaries for adjuster review.

**Zones in use:** Customer Sovereign (the insurer), AMS (the implementation partner who built the agent), Product Vendor (the vector database and orchestration platform), LLM Vendor (the frontier model used for summarization).

**Personal data categories:** Identifiers (customer name, policy number), contact data, claim narrative content potentially including health information (special category).

**GDPR Profile application:**
- The claim narrative may contain Article 9 special category data. Special category data is Tier III under this profile. Tier III data may not by default cross to the LLM Vendor zone.
- Two options: (a) deploy a self-hosted LLM within the Customer Sovereign Zone or within a Compute Vendor zone with EU residency, eliminating the LLM Vendor crossing for sensitive content; (b) implement a redaction layer in the Customer Sovereign Zone that removes special category content before the prompt reaches the LLM Vendor, with the LLM operating on Tier II content only.
- The AMS provider requires an Article 28 DPA. Their access to plaintext customer data must be limited to the minimum necessary for their operational responsibilities.
- The Product Vendor (vector database) must be configured with EU residency or under SCCs. Customer documents stored as embeddings are personal data and require the same protections as the source.
- The LLM Vendor relationship requires SCCs and a Transfer Impact Assessment if the vendor operates outside the EU. The TIA must address the destination jurisdiction's surveillance regime.
- Article 22 applies if the agent's output drives a decision affecting the customer (claim approval, denial). Human review at the adjuster stage satisfies the recourse requirement, provided the review is meaningful and not perfunctory.
- A DPIA is required for the deployment under Article 35, given the use of automated processing on personal data including special categories.

The boundary diagram, with the GDPR Profile applied, drives specific architectural decisions: redaction layer placement, vendor selection (favoring EU-resident options or those with established transfer mechanisms), retention configuration, and audit logging granularity.

---

The GDPR Profile is a starting point. Specific deployments may require deeper interpretation, and member state supervisory authorities may issue guidance that modifies these defaults. Profile updates will track significant guidance changes.
