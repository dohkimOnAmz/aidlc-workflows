# Parallel Execution Rules

## Overview
Enables multiple developers/agents to work on units simultaneously with automatic branch management, Interface Contracts, and Mock-based isolation.

## Cross-Cutting Constraints (enforced at every applicable stage)

### Rule PARALLEL-01: Branch Isolation
Each unit MUST operate in its own Git branch (unit/{unit-name}). No unit may directly modify files outside its designated scope. No direct pushes to main — always via PR.

### Rule PARALLEL-02: Interface Contract Required
Multi-unit projects with cross-unit dependencies MUST establish Interface Contracts before any unit branch is created. Contracts MUST be in machine-readable format (OpenAPI, gRPC proto, JSON Schema).

### Rule PARALLEL-03: Contract Test on PR
Every PR to main MUST pass Contract Tests for all interfaces the unit implements (Provider Tests) before merge is allowed.

### Rule PARALLEL-04: PO Merge Authority
Only PO may merge PRs to main. Merge order follows business priority. PO is the final authority on all integration decisions.

### Rule PARALLEL-05: Mock Transition Verification
When a dependency unit merges to main, dependent units MUST:
1. Pull main into their branch
2. Replace Mock with real service reference
3. Rerun Contract Tests against real service
4. Report pass/fail to PO

### Rule PARALLEL-06: Contract Issue Handling During Development
When a contract issue is discovered during development:
1. Unit lead logs the issue in dashboard: "Contract Issue Noted - {description}"
2. Unit continues development using Mock as-is (do NOT block)
3. Contract issues are resolved during Sync Point phase after all units complete
4. Do NOT create Contract Change PRs during active development

## Stage Orchestration

When this extension is enabled, the Construction Phase executes additional stages in the following order:

### Before Per-Unit Loop:
1. **Interface Contract Establishment**
   - Load all steps from `extensions/parallel/interface-contract.md`
   - Establish contracts for all inter-unit interfaces
   - Commit contracts to main branch

2. **Contract-Driven Mock Generation**
   - Load all steps from `extensions/parallel/contract-mock-generation.md`
   - Generate Mock servers + Contract Test suites from contracts

3. **Parallel Execution Strategy**
   - Load all steps from `extensions/parallel/parallel-execution-strategy.md`
   - Create Git branches, prepare unit contexts, initialize dashboard

### During Per-Unit Loop:
Each unit executes in its own branch with context isolation:
- Load shared context from main: Interface Contracts, architecture docs
- Load unit-specific context: assigned stories, component design
- Load Mock server configurations for dependencies
- Do NOT load other units' implementation details

### After Each Unit Completes Code Generation:
4. **Sync Point** (per unit PR)
   - Load all steps from `extensions/parallel/sync-point.md`
   - PR creation, Contract Test verification, PO review, Mock→Real transition

### When Parallel Execution Is Not Feasible
If parallel execution turns out to be infeasible after opt-in (e.g., insufficient team members to staff all units concurrently, or tooling constraints):
1. Complete ALL units' Functional Design first (sequential, lightweight)
2. Then complete ALL units' Code Generation (sequential)
3. This provides cross-unit design awareness even in sequential mode

> Note: For truly single-developer projects, prefer answering "B" to the parallel-execution opt-in prompt rather than enabling the extension and using this fallback. The fallback is intended for teams that opted in but later encountered practical constraints.

## Verification (at stage completion)
When presenting any stage completion during Construction Phase:
- Confirm branch isolation is maintained
- Confirm no contract violations detected
- Confirm dashboard status is current
