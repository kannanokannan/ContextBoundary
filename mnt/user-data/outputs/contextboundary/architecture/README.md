# Architecture

The `architecture/` directory contains the structural specifications of ContextBoundary. Three documents define the framework's technical surface:

## Contents

### [Boundary Diagram](boundary-diagram.md)
The canonical five-zone diagram with full annotations. Defines the Customer Sovereign Zone, AMS / Service Vendor Zone, Product Vendor Zone, Compute Vendor Zone, and LLM Vendor Zone. Names the four Crossing Points between them and the legal instruments that govern each.

### [Privacy Tier Classification](tier-classification.md)
Tier I, II, and III definitions. Crossing rules per tier. Tier inheritance behavior in compound payloads. Failure modes the classification prevents.

### [Vendor Tier Responsibility Matrix](vendor-tier-matrix.md)
Pipeline stages mapped to vendor zones. Cell notation (Owns, Operates, Conditional, Not Permitted) and conditional notes. How Audit Profiles modify the matrix.

## Reading Order

For first-time readers: boundary-diagram → tier-classification → vendor-tier-matrix. Each builds on the previous.

For practitioners using the framework to assess a specific deployment: start with the vendor-tier-matrix, identify the workload's pipeline stages, then return to boundary-diagram and tier-classification for the constraints that apply.

## Relationship to Other Directories

The architecture documents define the framework's base state. The directories that depend on them:

- **`audit-profiles/`** — modifies the base matrix and crossing rules per regulation
- **`endpoint-atlas/`** — classifies specific vendors against the zone definitions
- **`connectors/`** — bridges these architectural primitives to organizational governance

Changes to the architecture documents require corresponding review of all dependent directories.
