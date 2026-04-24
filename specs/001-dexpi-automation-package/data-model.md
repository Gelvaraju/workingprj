# Data Model: DEXPI-to-Automation Converter

## 1. DEXPI Source Model

- Purpose: Parsed representation of DEXPI 2.0/3.0 input document.
- Key fields:
  - sourceFileId (string)
  - dexpiVersion (enum: 2.0, 3.0)
  - equipment[] (EquipmentNode)
  - instruments[] (InstrumentNode)
  - pipingSegments[] (PipingNode)
  - nozzles[] (NozzleNode)
  - topologyEdges[] (TopologyEdge)
  - sourceHash (string)
- Validation rules:
  - XML must be well-formed and pass schema validation for declared DEXPI version.
  - Required identifiers must be unique in document scope.
  - Topology references must resolve to known nodes; cycles are allowed but reported.

## 2. Mapping Rule Set

- Purpose: Declarative mapping from source component classes to canonical semantics.
- Key fields:
  - ruleSetVersion (string)
  - rules[]:
    - ruleId (string)
    - priority (int)
    - sourceMatch (pattern)
    - targetSemanticClass (string)
    - tagTemplate (string)
    - datatypeMap (object)
    - fallbackMode (enum: error, warn, skip)
- Validation rules:
  - ruleId must be unique per ruleSetVersion.
  - priority ordering must be deterministic.
  - fallbackMode must be explicitly set.

## 3. Engineering Tag Definition

- Purpose: Canonical tag/signal definition shared by MTP and PLC generation.
- Key fields:
  - tagId (string)
  - name (string)
  - dataType (enum/string)
  - direction (enum: in, out, inout)
  - semanticClass (string)
  - sourceRefs[] (string)
  - ruleRef (string)
- Validation rules:
  - name must be deterministic and unique within target namespace.
  - dataType conversion must be lossless or recorded as warning.

## 4. HMI Engineering Output

- Purpose: Generated MTP artifact payload for HMI engineering.
- Key fields:
  - artifactPath (string)
  - mtpSchemaVersion (string)
  - hmiObjects[]
  - tagBindings[]
  - topologyRepresentation
- Validation rules:
  - Must pass MTP schema validation gate.
  - Every generated object must reference sourceRefs and ruleRef.

## 5. PLC Engineering Output

- Purpose: Generated TIA-importable S7-1500 PLC project artifact.
- Key fields:
  - artifactPath (string)
  - targetController (string: S7-1500)
  - tiaTargetVersions[] (V20, V21)
  - blocks[]
  - interfaces[]
  - tags[]
- Validation rules:
  - Must include import validation manifest for V20 and V21.
  - Naming/type contracts must align with HMI and OPC UA bindings.

## 6. OPC UA Binding Map

- Purpose: Deterministic mapping between HMI-facing and PLC-facing interfaces.
- Key fields:
  - namespaceUri (string)
  - bindings[]:
    - bindingId (string)
    - hmiInterfaceRef (string)
    - plcInterfaceRef (string)
    - nodeId (string)
    - dataType (string)
  - securityMode (enum: None)
  - migrationHint (string)
- Validation rules:
  - bindingId and nodeId must be deterministic for same input/rule set.
  - securityMode fixed to None in POC; migration hint required.

## 7. Conversion Evidence Record

- Purpose: Complete run evidence and diagnostics.
- Key fields:
  - runId (string)
  - timestampUtc (datetime)
  - sourceFileId (string)
  - warnings[]
  - unmappedElements[]
  - traceability[]
  - validationResults[]
  - importValidation (V20Result, V21Result)
- Validation rules:
  - traceability coverage must be 100% for generated output elements.
  - warnings/unmapped must never block partial valid output generation.

## Relationships

- DEXPI Source Model -> Mapping Rule Set (applies rules)
- Mapping Rule Set -> Engineering Tag Definition (creates canonical tags)
- Engineering Tag Definition -> HMI Engineering Output (tag bindings)
- Engineering Tag Definition -> PLC Engineering Output (interface/tag structures)
- HMI Engineering Output + PLC Engineering Output -> OPC UA Binding Map (cross-artifact alignment)
- All entities -> Conversion Evidence Record (traceability + diagnostics)

## State Transitions

1) Ingested -> Parsed
2) Parsed -> Validated
3) Validated -> Mapped
4) Mapped -> Generated (HMI/PLC/OPC UA)
5) Generated -> Validated Outputs
6) Validated Outputs -> Packaged
7) Any state -> Partial Completed (on non-fatal mapping/input issues with warnings)
8) Any state -> Failed (on fatal schema/parser/runtime errors with diagnostics)
