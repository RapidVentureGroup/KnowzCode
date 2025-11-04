---
description: "Kick off the KCv2.0 loop: capture PrimaryGoal, initialize WorkGroup state, and start with Loop 1A"
argument-hint: "[primary_goal] [workgroup_type]"
---

Execute the KnowzCode v2.0 workflow initialization sequence.

## Workflow Steps

1. **Validate Environment** - Ensure all prerequisites are met
2. **Load Core Context** - Load knowzcode_loop.md, knowzcode_log.md, knowzcode_tracker.md, automation_manifest.md
3. **Generate WorkGroup ID** - Create unique identifier for this work session
4. **Initialize WorkGroup State** - Set up tracking and todo management
5. **Background Orchestration** - Prepare feature context
6. **Run Impact Analysis** - Assess scope and dependencies
7. **Render Start Prompt** - Load KCv2.0__Start_Work_Session.md

## Arguments

- `primary_goal` (optional): High-level objective to start the KnowzCode loop. If not provided, you'll be prompted.
- `workgroup_type` (optional): WorkGroup classification - accepts "feature", "bug fix", "refactor", or "issue". Natural language allowed.

## Example Usage

```
/kc "Implement user authentication" feature
/kc "Fix payment processing bug" "bug fix"
/kc
```

## Post-Execution

After completion, the WorkGroup todos will be displayed and caches updated.
