# Interface Contract Establishment - Detailed Steps

## Overview
This stage defines machine-readable contracts for all inter-unit interfaces before any unit branch is created. Contracts serve as:
1. Shared reference for all parallel development sessions
2. Source for Mock server auto-generation
3. Source for Contract Test auto-generation
4. Single source of truth for integration verification

**Contract Format Rule**: All contracts MUST be written in language-neutral, machine-readable formats ONLY:
- OpenAPI 3.0 YAML/JSON (for REST APIs)
- Protocol Buffers .proto (for gRPC)
- JSON Schema (for data models and event payloads)
- AsyncAPI YAML/JSON (for event-driven interfaces)

No programming language code (Swift, Python, Java, TypeScript, etc.) is allowed in contract files.

## Prerequisites
- Units Generation must be complete
- `aidlc-docs/inception/application-design/unit-of-work-dependency.md` must exist with dependency matrix
- `aidlc-docs/inception/application-design/unit-team-assignment.md` must exist

## PART 1: PLANNING

### Step 1: Identify All Inter-Unit Interfaces
- [ ] Load `aidlc-docs/inception/application-design/unit-of-work-dependency.md`
- [ ] For each dependency arrow, determine the network protocol and contract format:
  - REST API → OpenAPI 3.0 spec
  - gRPC → .proto file
  - Event/Message → AsyncAPI spec + JSON Schema for payloads
- [ ] If a dependency has no clear network protocol, ask the **PO** to decide. If the PO defers to technical judgment, consult the **Unit Leads** of the provider and consumer units; the PO retains final authority.
- [ ] Create interface inventory list with protocol type for each

### Step 2: Generate Context-Appropriate Questions
- [ ] For each interface, generate questions about:
  - **Data Models**: What fields? Types? Required vs optional?
  - **Operations**: What endpoints/methods? Request/response formats?
  - **Error Handling**: HTTP status codes? Error response schema? Retry behavior? Timeout values?
  - **Authentication**: How do units authenticate to each other? (API key, JWT, mTLS)
  - **Versioning**: How will interface changes be managed?

### Step 3: Store Plan and Collect Answers
- [ ] Save as `aidlc-docs/construction/plans/interface-contract-plan.md`
- [ ] Request user input on all [Answer]: tags
- [ ] Analyze answers for ambiguities (MANDATORY)
- [ ] Resolve all ambiguities with follow-up questions

### Step 4: Request Approval
- Ask: "**Interface contract plan complete. Review and approve?**"
- DO NOT PROCEED until PO approves

## PART 2: GENERATION

### Step 5: Generate Contract Specifications
For each interface, generate language-neutral specification:
- [ ] REST APIs: `aidlc-docs/construction/contracts/{provider}-{consumer}-api.yaml` (OpenAPI 3.0)
- [ ] gRPC: `aidlc-docs/construction/contracts/{service-name}.proto` (Protocol Buffers)
- [ ] Events: `aidlc-docs/construction/contracts/{event-name}-async-api.yaml` (AsyncAPI)
- [ ] Shared Data Models: `aidlc-docs/construction/contracts/{model-name}-schema.json` (JSON Schema)

### Step 6: Generate Contract Summary
- [ ] Generate `aidlc-docs/construction/contracts/contract-summary.md`:
  - Interface inventory with protocol type and links to specs
  - Dependency diagram (Mermaid) with interface labels
  - Data flow overview
  - Mock server port assignments (each interface gets a unique port)

### Step 7: Validate Contracts
- [ ] Verify all contracts use language-neutral formats (no programming language code)
- [ ] Verify all inter-unit dependencies have corresponding contracts
- [ ] Verify no circular dependencies in contracts
- [ ] Verify data models are consistent across contracts (shared field names/types)
- [ ] Verify contract specs are syntactically valid (OpenAPI/proto/JSON Schema parseable)

### Step 8: Commit Contracts to Main
- [ ] All contract files committed to main branch
- [ ] All unit branches will inherit these contracts on branch creation

### Step 9: Present Completion and Wait for Approval
- PO and all unit leads must approve contracts
- Contracts are the foundation for parallel execution
- Changes to contracts after this point are deferred to Sync Point phase

## Critical Rules
- Contracts MUST use language-neutral formats (OpenAPI, .proto, JSON Schema, AsyncAPI)
- No programming language code is allowed in contract files
- ALL inter-unit interfaces MUST have a contract (no undocumented dependencies)
- Contracts define the "API surface" - implementation details are NOT in contracts
