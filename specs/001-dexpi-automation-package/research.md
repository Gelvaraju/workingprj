# Phase 0 Research: DEXPI-to-Automation Converter

## Runtime and Platform

- Decision: Use .NET 8 (C#) for a standalone local CLI converter.
- Rationale: Best fit for deterministic XML/XSD processing, Windows/TIA engineering environments, and mature OPC UA tooling with a clear hardening path.
- Alternatives considered: Python 3.12 (faster prototyping, weaker packaging reproducibility), Java 21 (heavier distribution), Rust (higher delivery complexity).

## Dependencies

- Decision: Use minimal core libraries: `System.Xml.*`, `System.CommandLine`, `OPCFoundation.NetStandard.Opc.Ua`, `Serilog`, `YamlDotNet`, `xUnit`, `FluentAssertions`.
- Rationale: Maintains auditability and deterministic behavior while supporting schema validation, traceability reporting, and structured diagnostics.
- Alternatives considered: Large transformation frameworks, custom-only logging, and test strategy without golden-file coverage.

## Output Bundle Strategy

- Decision: Emit a deterministic bundle with `mtp/`, `plc/`, `opcua/`, and `reports/` directories plus a run manifest.
- Rationale: Keeps delivery artifacts and evidence together while supporting partial-output behavior and version-specific validation records.
- Alternatives considered: Monolithic file output, loose ad-hoc folders, or database-backed run storage.

## Mapping Architecture

- Decision: Implement parser → canonical model → ordered declarative rule packs → target generators.
- Rationale: Ensures shared semantics across HMI/PLC/OPC UA outputs, deterministic naming, and traceable rule-driven decisions.
- Alternatives considered: Per-target direct transforms, hard-coded rules only, and non-deterministic mapping heuristics.

## Validation Strategy

- Decision: Use staged quality gates: schema checks, deterministic tests, contract tests, malformed-input resilience, then TIA V20/V21 import validation.
- Rationale: Balances fast feedback with strong release confidence for industrial engineering workflows.
- Alternatives considered: End-only validation, manual-only import checks, and fail-fast-only error handling.

## OPC UA Security Posture

- Decision: Keep POC generation at `None` security mode, but preserve stable node/interface contracts for later migration to `SignAndEncrypt` + `Basic256Sha256`.
- Rationale: Meets current scope without introducing production security debt.
- Alternatives considered: No migration path (high retrofit risk) or forcing production security immediately (scope overreach).
