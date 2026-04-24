# Implementation Plan: DEXPI-to-Automation Package Generation

**Branch**: `[001-dexpi-automation-package]` | **Date**: 2026-04-24 | **Spec**: `specs/001-dexpi-automation-package/spec.md`
**Input**: Feature specification from `/specs/001-dexpi-automation-package/spec.md`

## Summary

Build a standalone local-first converter that parses DEXPI 2.0/3.0 and generates deterministic, traceable outputs for both MTP HMI and S7-1500 PLC engineering workflows. The implementation uses a shared canonical model and mapping-rule pipeline so HMI, PLC, and OPC UA artifacts remain semantically aligned, supports graceful degradation with structured diagnostics, and enforces import validation in TIA Portal V20 and V21.

## Technical Context

**Language/Version**: .NET 8 (C# 12)  
**Primary Dependencies**: `System.Xml.*`, `System.CommandLine`, `OPCFoundation.NetStandard.Opc.Ua`, `Serilog`, `YamlDotNet`  
**Storage**: File-based artifacts and JSON evidence reports (no database)  
**Testing**: `xUnit`, `FluentAssertions`, deterministic golden-file tests, contract tests, integration validation gates  
**Target Platform**: Windows 11 engineering workstation (primary), CI runners for automated checks  
**Project Type**: Local CLI application (single-repo)  
**Performance Goals**: End-to-end conversion per file ≤ 60 seconds on representative in-scope samples  
**Constraints**: Offline/local-first operation, deterministic outputs, OPC UA `None` security for POC, TIA-only validation baseline (V20/V21), no PCS Neo scope  
**Scale/Scope**: Representative POC sample set with ≥95% auto-mapping coverage and 100% traceability coverage for generated output elements

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

- Maintainability: PASS — layered architecture with parser, canonical model, mapping engine, generators, and reporting modules; dependency set remains minimal and justified.
- Security: PASS — XML parsing hardened against unsafe constructs, no secret material required for core conversion, structured logs redact sensitive values, fail-safe validation gates block invalid artifacts.
- Verification: PASS — deterministic test suite, schema and contract tests, malformed-input resilience tests, and import-validation gates defined.
- Traceability: PASS — every generated element must carry source DEXPI ID + mapping rule reference, with traceability reports emitted per run.
- Compatibility: PASS — compatibility-impact logging required when naming/type contracts change; OPC UA production security path defined without node renaming.

## Phase 0 Research Outcomes

- Runtime and packaging strategy selected for deterministic local execution.
- Mapping architecture selected: canonical intermediate model + declarative rule packs.
- Validation strategy selected: staged gates from schema through integration import checks.
- All technical-context unknowns resolved; no unresolved `NEEDS CLARIFICATION` items remain.

## Project Structure

### Documentation (this feature)

```text
specs/001-dexpi-automation-package/
├── plan.md
├── research.md
├── data-model.md
├── quickstart.md
├── contracts/
│   ├── cli-contract.md
│   └── output-manifest.schema.json
└── tasks.md
```

### Source Code (repository root)

```text
src/
├── cli/
├── dexpi/
├── canonical/
├── mapping/
├── generators/
│   ├── mtp/
│   ├── plc/
│   └── opcua/
├── validation/
└── reporting/

config/
└── mapping-rules/

tests/
├── unit/
├── contract/
├── integration/
└── fixtures/
```

**Structure Decision**: Single-project CLI architecture with strict module boundaries and file-based artifacts to keep local/offline execution simple, deterministic, and auditable.

## Phase 1 Design & Contracts

- Data model captured in `data-model.md` including entity rules and lifecycle transitions.
- External interface contracts captured under `contracts/` for CLI invocation and artifact manifest format.
- Quickstart execution and validation flow captured in `quickstart.md`.

## Post-Design Constitution Check

- Maintainability: PASS — clear separation of responsibilities and rule-pack versioning avoids hard-coded transform sprawl.
- Security: PASS — security posture is explicit for POC (`None`) and future hardening path (`SignAndEncrypt + Basic256Sha256`) is documented.
- Verification: PASS — plan includes deterministic, schema, contract, integration, and resilience checks.
- Traceability: PASS — model and contracts enforce source-to-output lineage fields and evidence outputs.
- Compatibility: PASS — designated baseline versions (TIA V20/V21, DEXPI 2.0/3.0) and compatibility-impact requirement are explicit.

## Delivery Slices (WSJF-aligned)

1. Slice A: DEXPI parsing + canonical model + MTP baseline generation.
2. Slice B: PLC generation aligned to shared semantics.
3. Slice C: OPC UA mapping, traceability coverage, diagnostics hardening, and validation gates.

## Complexity Tracking

No constitution violations or exemptions identified at planning time.
