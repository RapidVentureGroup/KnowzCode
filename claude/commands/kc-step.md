---
description: "Run a KCv2.0 loop phase with the active WorkGroup context preloaded"
argument-hint: "[phase] [workgroup_id]"
---

Execute a specific phase of the KnowzCode v2.0 loop with full WorkGroup context.

## Workflow Steps

1. **Validate Environment** - Ensure prerequisites are met
2. **Confirm Change Set** - Verify pending changes before proceeding
3. **Load Core Context** - Load core KnowzCode documentation
4. **Scan Tracker** - Update current state from tracker
5. **Resolve Phase** - Map natural language to loop phase
6. **Run Phase-Specific Agent** - Delegate to appropriate subagent
7. **Render Phase Prompt** - Execute phase-specific prompt
8. **Display WorkGroup Todos** - Show current task list
9. **Git Status Check** - Show repository state

## Loop Phases

### Phase Codes
- **1A** - Draft specifications
- **1B** - Refine specifications
- **2A** - Implement changes
- **2B** - Verify implementation
- **3** - Finalize and close

### Friendly Names (Natural Language)
- "draft specs" → 1A
- "implement" → 2A
- "verify" → 2B
- (and more natural language variations)

## Arguments

- `phase` (optional): Loop phase code (1A/1B/2A/2B/3) or friendly name
- `workgroup_id` (optional): WorkGroupID - required for phases beyond 1A

## Example Usage

```
/kc-step 1A
/kc-step "draft specs"
/kc-step 2A WG_FEAT_20250103_001
/kc-step verify WG_BUGFIX_20250103_002
/kc-step
```

## Context Files

- knowzcode/automation_manifest.md
- knowzcode/knowzcode_tracker.md
- Phase-specific prompt files (resolved dynamically)

## Post-Execution

After completion:
- WorkGroup todos are displayed
- Git status is shown
- Caches are updated
