# Contract-Driven Mock Generation - Detailed Steps

## Overview
Generates Mock HTTP/gRPC servers and Contract Test suites from Interface Contracts. Mock servers are real network servers that respond according to the contract spec, enabling units to develop against actual network communication without waiting for dependency units to complete.

Mock servers are NOT stub classes, fake objects, in-memory implementations, or language-specific mock frameworks. They are real HTTP or gRPC servers listening on a port.

## Prerequisites
- Interface Contract Establishment must be complete
- All contracts must be in language-neutral format (OpenAPI, .proto, AsyncAPI)
- Machine-readable contract specs must exist in aidlc-docs/construction/contracts/

## Step 1: Identify Mock Requirements
- [ ] Load unit-team-assignment.md (check "Mock Required" column)
- [ ] Load unit-of-work-dependency.md
- [ ] For each unit with dependencies:
  - List which interfaces need Mock servers
  - Assign a unique port per Mock server
  - Determine Mock technology based on contract format:
    - OpenAPI 3.0 → Prism (preferred) or WireMock
    - Protocol Buffers .proto → grpc-mock or gripmock
    - AsyncAPI → stub event publisher with JSON Schema validation

## Step 2: Generate Mock Servers
For each required Mock:
- [ ] Generate Mock server configuration from contract spec
- [ ] Generate sample response data aligned with contract schemas
- [ ] Generate startup script (docker-compose or shell script):
  - Each Mock server runs on its assigned port
  - All Mocks can be started with a single command
- [ ] Store in `aidlc-docs/construction/mocks/{interface-name}/`:
  - Mock configuration file
  - Sample response data
  - Startup script
  - README with port number and usage instructions

## Step 3: Generate Contract Test Suites
For each interface contract:
- [ ] Generate **Provider Tests**: HTTP/gRPC requests that verify the real service matches the contract
  - Run against the real service after Code Generation
  - Each API endpoint/method in the contract = one test case
  - Verify response schema, status codes, headers
- [ ] Generate **Consumer Tests**: same HTTP/gRPC requests targeting the Mock server
  - Run against the Mock server during development
  - Identical test cases as Provider Tests but targeting Mock URL/port
- [ ] Store in `aidlc-docs/construction/contract-tests/{interface-name}/`

## Step 4: Verify Mock Servers
- [ ] Start all Mock servers (single command)
- [ ] Run Contract Tests against each Mock → all must pass
- [ ] Document Mock server URLs and ports in contract-summary.md

## Step 5: Document Mock → Real Service Transition Plan
- [ ] For each Mock, document:
  - Mock server URL: `http://localhost:{port}`
  - Real service URL: to be determined after deployment
  - Transition trigger: dependency unit's PR is merged to main
  - Transition process: update service URL configuration, rerun Contract Tests
  - Verification: same Contract Tests must pass against real service

## Step 6: Present Completion Message
- List all Mock servers with their ports and URLs
- Confirm all Mocks are running and Contract Tests pass
- Show single command to start/stop all Mocks
- Wait for PO approval before proceeding to parallel execution

## Critical Rules
- Mock servers MUST be real HTTP/gRPC servers (NOT stub classes, fake objects, or in-memory implementations)
- Mock servers MUST return responses matching contract spec EXACTLY (schema, status codes, headers)
- Contract Tests MUST make real HTTP/gRPC requests (NOT in-process calls)
- Contract Tests MUST be auto-generated from specs (not hand-written)
- Contract Tests are the SAME suite for both Mock verification and real service verification
- All Mock servers MUST be startable with a single command (docker-compose or script)
- If a contract changes, Mocks and Tests MUST be regenerated
