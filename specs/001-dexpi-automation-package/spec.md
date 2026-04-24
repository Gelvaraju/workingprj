# Feature Specification: DEXPI-to-Automation Package Generation

**Feature Branch**: `[001-dexpi-automation-package]`  
**Created**: 2026-04-24  
**Status**: Draft  
**Input**: User description: "Build a standalone DEXPI-to-Automation engineering POC that takes a DEXPI XML file and produces a complete automation package: (1) a VDI/VDE/NAMUR 2658 Part 4 compliant MTP file for HMI engineering, and (2) a Siemens S7-1500 PLC program project for control engineering using TIA Portal MTP CFL libraries..."

## Clarifications

### Session 2026-04-24

- Q: What is the expected PLC project deliverable level in this release? → A: Generate a full TIA Portal importable S7-1500 project artifact including PLC blocks, interface structures, and OPC UA-aligned tags.
- Q: Which target systems are mandatory for import validation in this release? → A: TIA Portal only; PCS Neo validation is not in scope for this release.
- Q: Which OPC UA security profile is required for generated connectivity artifacts? → A: `None` security mode (development/POC only); production security hardening is deferred to post-POC.
- Q: Which DEXPI profile version(s) must be supported? → A: Both DEXPI 2.0 and DEXPI 3.0 are in scope.
- Q: What is the mandatory TIA Portal version baseline for import validation? → A: Validation MUST include TIA Portal V20 and V21.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Generate importable HMI package from DEXPI (Priority: P1)

As an application engineer, I provide a DEXPI XML file and receive an MTP output that includes HMI-relevant content (screens, tags, and topology representation) so I can import it into the designated TIA Portal engineering environment without manual rebuild.

**Why this priority**: This delivers the primary value of eliminating manual HMI engineering effort from P&ID source data.

**Independent Test**: Run conversion with a valid representative DEXPI input and verify an output package is produced, schema-compliant, and importable in target TIA Portal V20 and V21 workflows.

**Acceptance Scenarios**:

1. **Given** a valid DEXPI XML file with equipment, instruments, nozzles, piping, and tags, **When** conversion is executed, **Then** an MTP output is generated with HMI screen objects, tag definitions, and represented piping/topology.
2. **Given** the generated MTP output, **When** it is validated and imported into designated target TIA Portal V20 and V21 systems, **Then** validation and import complete without blocking errors in both versions.

---

### User Story 2 - Generate aligned PLC project and OPC UA-ready interfaces (Priority: P2)

As a control engineer, I need an S7-1500 PLC engineering output generated from the same DEXPI source so PLC interfaces and HMI interfaces remain semantically aligned through shared MTP CFL mapping and can be connected consistently via OPC UA.

**Why this priority**: End-to-end automation engineering requires both HMI and PLC deliverables to be generated consistently from one source of truth.

**Independent Test**: Generate PLC output for the same DEXPI input used in User Story 1 and verify generated PLC-side structures align in naming and types with HMI/MTP-side interfaces and OPC UA mapping definitions.

**Acceptance Scenarios**:

1. **Given** a DEXPI file containing standard process components and instruments, **When** PLC generation is executed, **Then** a usable S7-1500 project artifact is produced with component-relevant control structures and interfaces.
2. **Given** both generated HMI and PLC artifacts, **When** OPC UA connectivity configuration is derived, **Then** signal/interface names and types are aligned and connection mapping is deterministic.

---

### User Story 3 - Preserve traceability and graceful degradation (Priority: P3)

As a QA/commissioning engineer, I need deterministic traceability and robust error handling so incomplete input does not block delivery and I can see what was generated, skipped, or mapped with warnings.

**Why this priority**: Traceability and robust handling are essential for engineering trust and adoption in industrial workflows.

**Independent Test**: Run with malformed or partially complete DEXPI samples; confirm non-crashing execution, partial valid output, warning report, and full mapping trace records.

**Acceptance Scenarios**:

1. **Given** a DEXPI file with missing attributes, **When** conversion runs, **Then** warnings are reported and valid subsets are still generated.
2. **Given** unmapped components, **When** output reports are produced, **Then** all unmapped components are listed and the rest of the conversion completes.

---

### Edge Cases

- What happens when required topology references are missing or cyclic in source data?
- How does the system handle duplicate tag names or tag/type mismatches across components?
- What happens when a DEXPI component has no known MTP CFL equivalent?
- How is output behavior defined when OPC UA mapping requires a type conversion that is not lossless?
- What happens when an input file is valid XML but not valid DEXPI structure?

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: System MUST parse DEXPI XML inputs conforming to DEXPI 2.0 or DEXPI 3.0 profiles and extract equipment, instruments, piping segments, nozzles, and tag identifiers.
- **FR-002**: System MUST generate an MTP artifact containing HMI-relevant engineering content from valid DEXPI input.
- **FR-003**: System MUST preserve represented spatial/topological relationships of process objects and piping from source input.
- **FR-004**: System MUST map DEXPI components to corresponding MTP CFL library semantics for HMI artifact generation.
- **FR-005**: System MUST generate NAMUR-aligned tags from extracted instrument and interface data.
- **FR-006**: System MUST link generated HMI objects to their corresponding generated tags.
- **FR-007**: System MUST generate a full TIA Portal importable S7-1500 PLC project artifact derived from the same DEXPI source, including PLC blocks, interface structures, and OPC UA-aligned tags.
- **FR-008**: System MUST reuse shared MTP CFL semantic mapping across HMI and PLC generation to keep both outputs consistent.
- **FR-009**: System MUST generate PLC-side interface/signal structures that align with generated HMI/MTP-side interfaces.
- **FR-010**: System MUST generate deterministic OPC UA interface mapping artifacts so HMI and PLC outputs can be connected consistently. Generated OPC UA connectivity artifacts MUST target `None` security mode (no transport encryption or signing) appropriate for development/POC environments; production security profile is explicitly out of scope for this release.
- **FR-011**: System MUST produce warning reports for incomplete, malformed, or unmapped source elements without crashing.
- **FR-012**: System MUST generate partial valid outputs for resolvable elements when some source elements are invalid or unmapped.
- **FR-013**: System MUST record traceability from each generated HMI/PLC element back to source DEXPI identifiers and mapping decisions.
- **FR-014**: System MUST produce outputs suitable for direct import/use in the designated target HMI and PLC engineering workflows without manual structural reconstruction. Mandatory import validation baseline for this release is TIA Portal V20 and V21.

### Key Entities *(include if feature involves data)*

- **DEXPI Source Model**: Parsed representation of process equipment, instruments, piping, nozzles, and source identifiers.
- **Mapping Rule Set**: Deterministic correspondence between DEXPI component types and shared MTP CFL semantics used by both HMI and PLC outputs.
- **Engineering Tag Definition**: Canonical signal/tag entity including name, type, direction, and mapping metadata.
- **HMI Engineering Output**: MTP-centered artifact with HMI object definitions, topology representation, and tag bindings.
- **PLC Engineering Output**: S7-1500 project artifact with generated control/interface structures aligned to tag definitions.
- **OPC UA Binding Map**: Connectivity mapping between generated HMI-facing and PLC-facing interfaces, including naming/type alignment metadata.
- **Conversion Evidence Record**: Log/report dataset containing warnings, unmapped elements, and source-to-output trace references.

## Prioritization Framework *(mandatory)*

### Requirement Prioritization (MoSCoW)

| Requirement Group | Priority | Rationale |
|-------------------|----------|-----------|
| DEXPI parsing, MTP generation, PLC generation, OPC UA interface alignment, deterministic traceability | Must | Core value proposition and minimum viable end-to-end automation outcome |
| Auto-mapping robustness, partial-output diagnostics, importability validation hardening | Should | Required for reliable enterprise adoption but can iterate after core flow is stable |
| Enhanced reporting depth and broader optional optimization aids | Could | Valuable improvement once core delivery and reliability targets are met |
| Alarm/event engineering, advanced process optimization, runtime simulation, round-trip back-conversion, non-MTP HMI output formats | Won't (this release) | Explicitly out of scope for this POC |

### Delivery Sequencing (WSJF)

| Initiative / Slice | Business Value (1-10) | Time Criticality (1-10) | Risk Reduction / Opportunity Enablement (1-10) | Job Size (1-10) | WSJF Score |
|--------------------|------------------------|--------------------------|-----------------------------------------------|------------------|------------|
| Slice A: DEXPI parse + canonical model + MTP HMI output baseline | 10 | 9 | 9 | 6 | 4.67 |
| Slice B: PLC S7-1500 output generation aligned to shared semantics | 9 | 8 | 9 | 7 | 3.71 |
| Slice C: OPC UA mapping alignment + traceability + diagnostics hardening | 8 | 8 | 10 | 6 | 4.33 |

Scoring note: WSJF values must be re-scored at plan finalization and before implementation kickoff.

## Outcome Metrics & Guardrails *(mandatory)*

### North Star Outcome

- **NSM-001**: Reduce manual engineering effort for DEXPI-driven HMI+PLC package preparation by at least 50% on representative POC workflows.

### Guardrail Metrics

- **GR-001**: Automatic generation MUST not reduce import/validation pass rate below 100% for baseline in-scope validation samples.
- **GR-002**: End-to-end generation latency MUST remain within 60 seconds for representative in-scope samples.

## Risks & Dependencies *(mandatory)*

- **RISK-001**: Inconsistent source DEXPI profiles may reduce auto-mapping coverage; mitigation requires profile conformance checks and fallback warning/reporting.
- **RISK-002**: Interface-type mismatches between generated artifacts may break OPC UA binding; mitigation requires strict type-alignment validation in generation outputs.
- **DEP-001**: Availability and stability of approved MTP CFL mapping catalogs and target import validation environments are required for reliable acceptance.

## Non-Functional Requirements *(mandatory)*

### Security & Privacy

- **NFR-SEC-001**: System MUST run in local/on-prem compatible mode and MUST NOT transmit source process data outside configured engineering boundaries unless explicitly approved.
- **NFR-SEC-002**: System MUST log conversion and mapping decisions without exposing secrets or sensitive credentials.
- **NFR-SEC-003**: Generated OPC UA artifacts MUST use `None` security mode for this POC release. A documented upgrade path to `SignAndEncrypt` + `Basic256Sha256` MUST be included in operational readiness notes to prevent security debt in production adoption.

### Reliability & Safety

- **NFR-REL-001**: System MUST not crash on malformed or incomplete input; it MUST return actionable diagnostics and preserve valid partial generation where feasible.
- **NFR-REL-002**: Outputs MUST be deterministic for identical inputs, configuration, and mapping rules.

### Maintainability & Traceability

- **NFR-MNT-001**: Every generated HMI object, PLC structure, and OPC UA mapping entry MUST be traceable to source DEXPI identifiers and mapping rules.
- **NFR-MNT-002**: Mapping rule changes MUST be auditable and distinguishable from baseline mappings.

### Breaking Change & Compatibility Impact

- **COMP-001**: Any change that alters generated interface names, signal types, or structural contracts MUST include a compatibility impact note and migration guidance for downstream HMI/PLC consumers.

## Operational Readiness *(mandatory for production-impacting features)*

- **OPS-001**: Conversion runs MUST emit structured diagnostics for mapping coverage, warning counts, unmapped elements, and output validation status.
- **OPS-002**: Release readiness MUST define acceptance gates requiring schema/format validation success and deterministic traceability evidence across generated outputs.
- **OPS-003**: Rollback criteria MUST require reversion to the last approved mapping baseline if importability, interface alignment, or traceability gates fail.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: 100% of representative in-scope sample files produce both HMI and PLC outputs or a documented partial output with explicit diagnostics (no silent failure).
- **SC-002**: For representative in-scope samples, end-to-end generation completes within 60 seconds per input file.
- **SC-003**: At least 95% of in-scope source components are automatically mapped without manual intervention in baseline validation samples.
- **SC-004**: 100% of generated output interface elements include source trace references and mapping evidence entries.
- **SC-005**: 100% of generated output packages satisfy schema/format validation checks and import validation gates in both TIA Portal V20 and V21 designated target engineering workflows.

## Assumptions

- Source DEXPI files follow expected profile conventions for required identifiers and structural sections.
- Supported source profile versions for this release are limited to DEXPI 2.0 and DEXPI 3.0.
- Required mapping rules for standard in-scope component families are available before execution.
- Target engineering environments include TIA Portal V20 and V21 for mandatory import validation and support import/use of the produced artifact versions.
- OPC UA connectivity setup is driven by generated interface maps; runtime commissioning specifics remain outside this feature scope.
- Alarm/event engineering, advanced process optimization, runtime simulation, round-trip back-conversion, and non-MTP HMI outputs are out of scope for this POC.

## Decision Log *(mandatory)*

| Decision | Options Considered | Selected Option | Rationale |
|----------|---------------------|-----------------|-----------|
| D-001 Source-to-output strategy | Separate HMI and PLC pipelines, Unified shared semantic pipeline | Unified shared semantic pipeline | Preserves consistent semantics and improves deterministic traceability across both outputs |
| D-002 Handling incomplete input | Fail-fast only, Partial output with diagnostics | Partial output with diagnostics | Maximizes engineering continuity while preserving transparency on unresolved elements |
| D-003 Interface connectivity basis | Independent per-target naming, Shared canonical interface map | Shared canonical interface map | Reduces mismatch risk and enables consistent OPC UA binding expectations |
