# Parallel Execution Strategy - Detailed Steps

## Overview
Defines the Git branch strategy, team assignment activation, and PO orchestration procedures for parallel unit development.

## Prerequisites
- Units Generation complete (with team roster and unit-team assignment)
- Interface Contracts established and committed to main
- Mock servers generated and verified (if applicable)

## Step 1: Create Git Branch Structure
- [ ] Verify main branch has all shared artifacts:
  - Inception documents (requirements, stories, architecture)
  - Interface Contracts (machine-readable specs)
  - Mock server configurations
  - Contract Test suites
- [ ] Create unit branches from main:
  ```
  For each unit in unit-of-work.md:
    git checkout main
    git checkout -b unit/{unit-name}
  ```
- [ ] Verify each branch inherits all shared artifacts

## Step 2: Prepare Unit-Specific Context Packages
For each unit branch, create a context guide:
- [ ] Generate `aidlc-docs/construction/{unit-name}/unit-context.md`:
  - Unit scope: assigned stories, component boundaries
  - Team: assigned members and roles
  - Dependencies: which interfaces this unit consumes (with Mock configs)
  - Provides: which interfaces this unit implements (with contract specs)
  - Branch rules: commit to unit branch only, PR to main for integration

## Step 3: Initialize Dashboard
- [ ] Load unit data into AI-DLC Dashboard (aidlc-dashboard.html):
  - All units with status "Pending"
  - Dependency graph from unit-of-work-dependency.md
  - Team assignments from unit-team-assignment.md
  - Branch names
  - Mock server status
- [ ] Export dashboard state as JSON for team distribution
- [ ] PO confirms dashboard reflects correct project structure

## Step 4: Define Execution Rules
Document in `aidlc-docs/construction/parallel-execution-rules.md`:

### Branch Rules
- Each unit branch is isolated: commit only to your branch
- Never push directly to main: always via PR
- Pull main regularly to get merged unit updates
- Resolve merge conflicts in unit branch, not main

### PR Rules
- **Unit → main PR**: Created when unit completes Code Generation + unit tests
  - Must pass: Contract Tests for all interfaces this unit implements
  - Must pass: unit tests
  - PO decides merge order based on business priority

### Communication Rules
- Dashboard is single source of truth for status
- Unit leads update dashboard when stage changes
- PO monitors dashboard and manages merge queue
- Blocked units flag status on dashboard immediately

## Step 5: Launch Parallel Execution
- [ ] PO announces parallel execution start
- [ ] Each team member opens their unit branch in their IDE/terminal
- [ ] Each team member starts AI agent session in their branch
- [ ] AI agent loads unit-context.md for branch-specific guidance
- [ ] Dashboard updated: all active units → "In Progress"
- [ ] PO monitors dashboard from central screen

## Step 6: Present Completion and Await PO Approval
- Show parallel execution plan summary
- Show dashboard initial state
- Confirm all branches created and contexts prepared
- PO approves to begin Construction

## Single-Agent Fallback
If parallel sessions are not available:
1. Complete ALL units' Functional Design first (sequential, lightweight)
2. Then complete ALL units' Code Generation (sequential)
3. This provides cross-unit design awareness even in sequential mode
4. Dashboard still tracks progress in sequential mode

## Critical Rules
- PO is the ONLY person who merges PRs to main
- Dashboard MUST be updated when any unit's status changes
- No unit should modify files outside its designated scope
