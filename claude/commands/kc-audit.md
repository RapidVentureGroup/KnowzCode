---
description: "Launch a KCv2.0 audit routine"
argument-hint: "[audit_type] [workgroup_id]"
---

Launch a comprehensive audit routine using KnowzCode v2.0 specialized auditors.

## Workflow Steps

1. **Validate Environment** - Ensure prerequisites are met
2. **Load Core Context** - Load core KnowzCode documentation
3. **Resolve Audit Type** - Map natural language to audit category
4. **Run Appropriate Auditor** - Delegate to specialized audit agent
5. **Render Audit Prompt** - Execute audit-specific prompt
6. **Update Caches** - Refresh internal state

## Audit Types

- **spec** - Specification quality and ARC compliance audit (uses arc-auditor)
- **implementation** - Code implementation audit (uses arc-auditor)
- **architecture** - Architectural review (uses architecture-reviewer)
- **security** - Security assessment (uses security-officer)
- **integration** - Holistic integration audit (uses holistic-auditor)

## Arguments

- `audit_type` (optional): Audit category - accepts values above or natural language
- `workgroup_id` (optional): WorkGroupID or NodeID targeted by the audit

## Example Usage

```
/kc-audit spec WG_FEAT_20250103_001
/kc-audit architecture
/kc-audit "security review" WG_BUGFIX_20250103_002
/kc-audit
```

## Context Files

- knowzcode/automation_manifest.md
- Audit-specific prompt files (resolved dynamically)
