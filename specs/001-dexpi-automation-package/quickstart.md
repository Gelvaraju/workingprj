# Quickstart: DEXPI-to-Automation Converter

## Prerequisites

- Windows engineering workstation
- .NET 8 SDK
- Access to TIA Portal V20 and V21 environments for import validation
- Sample DEXPI 2.0 and DEXPI 3.0 input files

## 1) Build

```powershell
dotnet restore
dotnet build -c Release
```

## 2) Run conversion

```powershell
dotnet run --project src/cli -- convert \
  --input .\samples\plant.dexpi.xml \
  --dexpi-version 3.0 \
  --rules .\config\mapping-rules\baseline.yaml \
  --output .\out\run-001
```

## 3) Expected outputs

- `out/run-001/mtp/`
- `out/run-001/plc/`
- `out/run-001/opcua/`
- `out/run-001/reports/traceability.json`
- `out/run-001/reports/warnings.json`
- `out/run-001/reports/validation.json`
- `out/run-001/reports/import-validation.json`

## 4) Validate deterministic behavior

```powershell
dotnet test tests/unit
dotnet test tests/contract
dotnet test tests/integration
```

Re-run conversion with identical input and rule set; output file hashes and binding IDs must match.

## 5) Validate TIA import baseline

- Import generated PLC artifact into TIA Portal V20.
- Import generated PLC artifact into TIA Portal V21.
- Confirm no blocking errors in either version.
- Record both results in `reports/import-validation.json`.

## 6) Validate graceful degradation

Run converter with malformed/incomplete DEXPI samples.

Expected:
- No process crash
- Partial valid outputs for resolvable elements
- Structured warnings and unmapped element reports

## 7) OPC UA security posture for this POC

- Generated OPC UA artifacts use security mode `None`.
- Preserve node/interface identity so future migration to `SignAndEncrypt + Basic256Sha256` does not require renaming.
