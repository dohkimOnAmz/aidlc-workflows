# Sync Point - Detailed Steps

## Overview
Defines the PR-based integration verification procedure when a unit completes and merges to main, and the subsequent Mock → Real Service transition.

## Trigger
A sync point occurs when:
- A unit completes Code Generation and creates a PR to main

## Unit Completion → PR Process

### Step 1: Pre-PR Verification (in unit branch)
- [ ] All unit tests pass
- [ ] Contract Tests pass for all interfaces this unit PROVIDES
- [ ] Consumer Contract Tests pass against Mock servers for dependencies
- [ ] No uncommitted changes
- [ ] Unit lead updates dashboard: status → "PR Review"

### Step 2: PR Creation
- [ ] Create PR: unit/{unit-name} → main
- [ ] PR description includes:
  - Unit name and scope
  - Stories implemented
  - Interfaces implemented (link to contract specs)
  - Test results summary
  - Any known issues or limitations

### Step 3: PO Review and Merge
- [ ] PO reviews PR for business requirement alignment
- [ ] Tech Lead reviews for technical quality
- [ ] PO approves and merges PR
- [ ] PO updates dashboard: unit status → "Completed" / "Merged"

## Mock → Real Service Transition

### Step 4: Notify and Transition Dependent Units
After a unit is merged to main:
- [ ] Identify all units that were using Mock for this unit
- [ ] Post notification (dashboard log + team communication)
- [ ] For each dependent unit:
  - [ ] `git pull origin main` in unit branch
  - [ ] Replace Mock server configuration with real service reference
  - [ ] Rerun Consumer Contract Tests against real service

### Step 5: Transition Verification and Resolution
- [ ] If Contract Tests PASS:
  - Transition complete, continue to next unit merge
  - Update dashboard: Mock status → "Replaced with Real Service"
- [ ] If Contract Tests FAIL:
  - Analyze root cause:
    - **Implementation bug** (provider didn't match contract):
      - Provider unit creates hotfix PR to main
      - After hotfix merged, dependent unit retries transition from Step 4
    - **Contract defect** (contract itself is wrong or incomplete):
      - Update contract spec on main
      - Regenerate affected Mock servers and Contract Tests
      - Affected units that already merged: create fix PR
      - Affected units not yet merged: pull main, adjust, continue
      - Rerun Contract Tests for all affected interfaces
  - [ ] Update dashboard with resolution status
  - [ ] Retry transition from Step 4 after resolution

## Critical Rules
- NEVER merge a PR that fails Contract Tests
- PO is the final authority on merge order
- Mock → Real transition MUST verify Contract Tests pass
- Transition failures MUST be categorized (implementation bug vs contract defect)
- Dashboard MUST reflect real-time status of all sync point activities
