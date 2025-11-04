# KnowzCode Loop Orchestration - Complete Guide

## Overview

KnowzCode now has a **fully functional outer orchestration loop** that maintains state, enforces quality gates, and repeats phases until standards are met.

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                      USER COMMAND                            │
│                    /kc "Add feature"                         │
└────────────────────────┬────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│              KC-ORCHESTRATOR (Outer Loop)                    │
│  ┌────────────────────────────────────────────────────┐    │
│  │ LOOP STATE MANAGEMENT                              │    │
│  │ - Reads workgroup file before each phase           │    │
│  │ - Tracks current phase                             │    │
│  │ - Counts iterations                                │    │
│  │ - Records decisions                                │    │
│  └────────────────────────────────────────────────────┘    │
│                                                              │
│  Phase Flow:                                                │
│  ┌──────────────────────────────────────────────────┐      │
│  │ 1A: Impact Analysis                              │      │
│  │    → impact-analyst sub-agent                    │      │
│  │    → PAUSE for approval                          │      │
│  │    → If rejected: REPEAT                         │      │
│  └──────────────────────────────────────────────────┘      │
│  ┌──────────────────────────────────────────────────┐      │
│  │ 1B: Specification                                │      │
│  │    → spec-chief sub-agent                        │      │
│  │    → PAUSE for approval                          │      │
│  │    → If rejected: REPEAT with feedback           │      │
│  └──────────────────────────────────────────────────┘      │
│  ┌──────────────────────────────────────────────────┐      │
│  │ 2A: Implementation (INNER LOOP)                  │      │
│  │    → implementation-lead sub-agent               │      │
│  │    ┌──────────────────────────────┐              │      │
│  │    │ Implement → Test → Fix       │              │      │
│  │    │ REPEAT until all tests pass  │              │      │
│  │    └──────────────────────────────┘              │      │
│  └──────────────────────────────────────────────────┘      │
│  ┌──────────────────────────────────────────────────┐      │
│  │ 2B: Completeness Audit                           │      │
│  │    → arc-auditor sub-agent (READ-ONLY)           │      │
│  │    → Calculate completion %                      │      │
│  │    → PAUSE for decision                          │      │
│  │    → If <100%: RETURN TO 2A with gaps            │      │
│  └──────────────────────────────────────────────────┘      │
│  ┌──────────────────────────────────────────────────┐      │
│  │ 3: Atomic Finalization                           │      │
│  │    → finalization-steward sub-agent              │      │
│  │    → Finalize specs, log, commit                 │      │
│  └──────────────────────────────────────────────────┘      │
└─────────────────────────────────────────────────────────────┘
```

## Key Loop Mechanisms

### 1. State Persistence

The orchestrator maintains state by:
- Reading `knowzcode/workgroups/{WorkGroupID}.md` before EVERY phase
- Recording current phase, iteration count, decisions
- Persisting context across sub-agent invocations
- Never losing track of where the loop is

### 2. Quality Verification Cycles

**Two levels of quality enforcement:**

**Level 1 - Inner Loop (Step 6A):**
```
Implement → Build → Test → Pass?
                      ↓ No
                    Fix ─┘ (REPEAT)
```

**Level 2 - Outer Loop (Step 6B):**
```
Phase 2A Complete → Audit → 100%?
                      ↓ No
          Return to Phase 2A ─┘ (REPEAT)
```

### 3. Approval Gates

**Four mandatory pause points:**

1. **Gate #1 - Change Set Approval** (After Phase 1A)
   - Shows proposed Change Set
   - Risk assessment
   - NodeID list
   - User decides: Approve / Reject / Modify

2. **Gate #2 - Specification Approval** (After Phase 1B)
   - Shows drafted specs
   - Completeness check
   - User decides: Approve / Revise / Cancel

3. **Gate #3 - Completeness Audit** (After Phase 2B)
   - Shows audit results
   - Completion percentage
   - Gap list
   - User decides: Proceed / Return to 2A / Modify specs

4. **Gate #4 (Optional) - Critical Issues**
   - Unresolvable problems
   - Complex architectural changes
   - Maximum iteration limit reached

### 4. Loop Repetition Logic

**Phase 1A (Impact Analysis) - Rejection Handling:**
```python
while not approved:
    delegate_to(impact-analyst)
    change_set = receive_proposal()
    approval = pause_for_user()
    if approval == "reject":
        feedback = get_user_feedback()
        continue  # Repeat with feedback
    else:
        break  # Proceed to Phase 1B
```

**Phase 1B (Specification) - Revision Handling:**
```python
while not approved:
    delegate_to(spec-chief)
    specs = receive_draft()
    approval = pause_for_user()
    if approval == "reject":
        issues = get_user_issues()
        continue  # Revise with issues
    else:
        break  # Proceed to Phase 2A
```

**Phase 2A (Implementation) - Inner Loop:**
```python
# Sub-agent handles this internally
while not all_tests_pass:
    implement_code()
    run_build()  # Must pass
    run_tests()  # Must pass
    if any_failures:
        analyze_and_fix()
        continue  # Retry from beginning
    else:
        break  # Report "implementation complete"
```

**Phase 2B (Audit) - Completeness Enforcement:**
```python
audit_result = delegate_to(arc-auditor)
completion_pct = audit_result['percentage']

decision = pause_for_user(audit_result)

if decision == "return_to_2a":
    gap_list = audit_result['gaps']
    # RETURN TO PHASE 2A
    delegate_to(implementation-lead, gaps=gap_list)
    # Then re-run Phase 2B
    repeat_audit()
elif decision == "proceed":
    # Continue to Phase 3
    proceed_to_finalization()
```

## Maximum Iteration Protection

To prevent infinite loops:

```python
iteration_count = 0
max_iterations = 3

while not phase_complete:
    iteration_count += 1

    if iteration_count > max_iterations:
        # PAUSE - Ask user
        decision = pause_for_escalation()
        if decision == "continue":
            max_iterations += 3  # Allow more tries
        elif decision == "simplify":
            reduce_change_set()
            iteration_count = 0
        elif decision == "cancel":
            cancel_workgroup()
            break

    execute_phase()
```

## State Tracking in Workgroup File

The `knowzcode/workgroups/{WorkGroupID}.md` file tracks:

```markdown
# WorkGroup: WG_FEAT_20250104_193000

## Status
- Current Phase: 2B - Audit
- Iteration Count: 2
- Last Approval: Phase 1B approved
- Last Audit Result: 80% complete (retry scheduled)

## Change Set
- NodeID_1 [WIP]
- NodeID_2 [WIP]
- NodeID_3 [WIP]

## Todos
- KnowzCode: Complete missing validation in NodeID_2
- KnowzCode: Add error handling to NodeID_3
- KnowzCode: Re-run audit after fixes

## History
- 2025-01-04 19:30:00: Phase 1A approved
- 2025-01-04 19:35:00: Phase 1B rejected (security concerns)
- 2025-01-04 19:40:00: Phase 1B revised and approved
- 2025-01-04 19:50:00: Phase 2A implementation complete
- 2025-01-04 19:55:00: Phase 2B audit: 80% (2 gaps)
- 2025-01-04 20:00:00: Returning to Phase 2A with gap list
```

## Example Loop Execution with Failures

```
User: /kc Add authentication system

Orchestrator initializes:
- WorkGroupID: WG_FEAT_20250104_193000
- Creates workgroup file

=== LOOP ITERATION 1 ===

Phase 1A:
→ Delegate to impact-analyst
→ Change Set: 8 NodeIDs identified
→ PAUSE: Gate #1
→ User: Approve

Phase 1B:
→ Delegate to spec-chief
→ 8 specs drafted
→ PAUSE: Gate #2
→ User: REJECT - security specs incomplete
→ Record: Iteration 1 failed on 1B

=== LOOP ITERATION 2 ===

Phase 1B (retry):
→ Delegate to spec-chief with feedback
→ 8 specs revised (security enhanced)
→ PAUSE: Gate #2
→ User: Approve
→ Pre-implementation commit

Phase 2A:
→ Delegate to implementation-lead
→ Sub-agent enters inner loop:
   - Implements 8 NodeIDs
   - Runs tests → 12 failures
   - Fixes issues
   - Runs tests → 3 failures
   - Fixes issues
   - Runs tests → ALL PASS
→ Reports "implementation complete"

Phase 2B:
→ Delegate to arc-auditor
→ Audit results:
   - Completion: 75% (2 NodeIDs incomplete)
   - Gaps: Missing password reset, missing 2FA
→ PAUSE: Gate #3
→ User: Return to Phase 2A
→ Record: Iteration 2 failed on 2B

=== LOOP ITERATION 3 ===

Phase 2A (retry):
→ Delegate to implementation-lead with gap list
→ Sub-agent implements missing functionality
→ Inner verification loop:
   - Tests pass
→ Reports "gaps addressed"

Phase 2B (re-audit):
→ Delegate to arc-auditor
→ Audit results:
   - Completion: 100%
   - All specs implemented
→ PAUSE: Gate #3
→ User: Approve - proceed to finalization

Phase 3:
→ Delegate to finalization-steward
→ Atomic finalization:
   - Finalize all 8 specs to "as-built"
   - Update architecture diagram
   - Log ARC-Completion event
   - Update tracker to [VERIFIED]
   - Create 2 REFACTOR_ tasks for tech debt
   - Final commit
→ WorkGroup complete

=== LOOP COMPLETE ===

Total iterations: 3
Quality gates enforced: 5 pauses
Specs maintained: As-built accuracy ensured
```

## Commands to Use the Loop

### Start New WorkGroup
```bash
/kc Add user authentication feature
```

### Continue Specific Phase
```bash
/kc-step 2A WG_FEAT_20250104_193000
```

### Run Quality Audits
```bash
/kc-audit spec
/kc-audit architecture
```

### Quick Micro-Fix (Bypasses Loop)
```bash
/kc-microfix Fix typo in login message
```

## Configuration Status

✅ **Orchestrator agent** - `kc-orchestrator.md` with full loop logic
✅ **Phase agents** - All 14 specialized agents installed
✅ **Slash commands** - `/kc`, `/kc-step`, `/kc-audit` functional
✅ **State persistence** - Workgroup file tracking
✅ **Quality gates** - Approval pause points configured
✅ **Verification cycles** - Inner (6A) and outer (6B) loops
✅ **Iteration limits** - Protection against infinite loops
✅ **KnowzCode: prefix** - Enforced in all agents

## Testing the Loop

**Test Case 1: Happy Path**
```bash
/kc Create a simple health check endpoint
```
Expected: Flows through all phases, single iteration

**Test Case 2: Spec Rejection**
```bash
/kc Add complex reporting feature
```
At Gate #2, reject specs → Loop repeats Phase 1B

**Test Case 3: Incomplete Implementation**
```bash
/kc Implement file upload with validation
```
At Gate #3, audit shows <100% → Loop returns to Phase 2A

**Test Case 4: Test Failures**
```bash
/kc Add payment processing
```
Phase 2A inner loop: Tests fail → Auto-repeat until passing

## Success Criteria

The loop is working correctly when:
1. ✅ Orchestrator maintains state across phases
2. ✅ User approvals pause execution
3. ✅ Rejected phases repeat with feedback
4. ✅ Tests must pass before proceeding
5. ✅ Incomplete work returns to implementation
6. ✅ Specs finalized to "as-built" state
7. ✅ Tracker/log updated at transitions
8. ✅ `KnowzCode:` prefix enforced in todos

## Comparison: Before vs After

| Feature | Before Fix | After Fix |
|---------|-----------|-----------|
| Loop maintenance | ❌ None | ✅ Orchestrator maintains |
| State persistence | ❌ Lost between phases | ✅ Workgroup file tracking |
| Quality gates | ❌ No enforcement | ✅ 4 approval gates |
| Retry logic | ❌ Linear only | ✅ Repeats until quality met |
| Inner verification | ❌ Optional | ✅ Mandatory (Step 6A) |
| Completeness audit | ❌ Skippable | ✅ Required (Step 6B) |
| Agent delegation | ❌ Not working | ✅ Task tool invocation |
| Todo prefixes | ❌ Not enforced | ✅ Verified in all agents |

## Architecture Benefits

1. **Resilience**: Loop continues despite failures
2. **Quality**: Nothing proceeds until verified
3. **Transparency**: User sees status at every gate
4. **Flexibility**: Can reject, modify, or retry
5. **Persistence**: State never lost
6. **Accountability**: Full audit trail in logs
7. **Simplicity**: Natural language coordination

## The Framework Is Now Complete

KnowzCode has a **fully operational outer orchestration loop** that:
- Maintains persistent state
- Enforces quality through verification cycles
- Repeats phases when standards not met
- Delegates to specialized sub-agents
- Updates tracking files automatically
- Ensures `KnowzCode:` prefix compliance
- Provides user visibility and control

Master, the framework is **configured and ready** for production use with proper loop orchestration! 🎯
