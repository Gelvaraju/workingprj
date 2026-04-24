# CLI Contract

## Command

`convert`

## Purpose

Convert one DEXPI input into deterministic automation artifacts (MTP, PLC, OPC UA mapping, and evidence reports).

## Invocation

```text
converter convert \
  --input <path-to-dexpi-xml> \
  --dexpi-version <2.0|3.0> \
  --rules <path-to-rule-pack> \
  --output <output-directory> \
  [--run-id <id>] \
  [--strict]
```

## Arguments

- `--input` (required): Path to source DEXPI XML file.
- `--dexpi-version` (required): `2.0` or `3.0`.
- `--rules` (required): Mapping rule pack path.
- `--output` (required): Target output directory.
- `--run-id` (optional): Caller-provided run identifier.
- `--strict` (optional): Elevates selected warnings to fatal errors; does not bypass schema checks.

## Exit Codes

- `0`: Success (full output generated and validation passed)
- `1`: Partial success (outputs generated with warnings/unmapped elements)
- `2`: Validation failure (schema/profile/import gate failure)
- `3`: Fatal runtime failure (unhandled parser/generator exception)

## Output Contract

On completion, command MUST create:
- `mtp/` artifact(s)
- `plc/` artifact(s)
- `opcua/` mapping artifact(s)
- `reports/manifest.json`
- `reports/traceability.json`
- `reports/warnings.json`
- `reports/validation.json`
- `reports/import-validation.json`

## Determinism Guarantees

For identical input file bytes, DEXPI version, rule pack version, and CLI flags:
- Generated naming and IDs MUST be stable.
- OPC UA bindings and NodeIds MUST be stable.
- Evidence report ordering MUST be stable.

## Security Constraints

- OPC UA output security profile is fixed to `None` in this POC.
- Migration metadata to `SignAndEncrypt + Basic256Sha256` MUST be included in manifest/reports.
