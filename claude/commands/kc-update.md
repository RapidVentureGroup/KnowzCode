---
description: "Intelligently merge KnowzCode framework updates from a newer checkout into the active project"
argument-hint: "[source_path] [options]"
---

# KnowzCode Framework Update

Merge improvements from a newer KnowzCode version into your active project while preserving customizations and project data.

**Source Path**: $1
**Options**: $2

## Safety First

This command will:
- ✅ Create backups before any changes
- ✅ Preserve all project data (tracker, logs, workgroups)
- ✅ Protect customizations
- ✅ Provide rollback capability
- ✅ Validate updates before applying
- ⚠️ Pause for your approval before making changes

## Execution

Use the update-coordinator sub-agent to orchestrate the update process

Context:
- Source Path: $1
- Current Project: /mnt/c/Code/KnowzCode-git
- Mode: Intelligent framework update with data preservation
- Options: $2

Instructions for update-coordinator:

### Phase 1: Validation and Backup

1. **Validate source path**:
   - Check that $1 exists and is a directory
   - Verify it contains valid KnowzCode structure:
     - claude/ or .claude/ directory with agents
     - knowzcode/ directory with core files
   - Confirm it's a newer/different version

2. **Validate current project**:
   - Confirm KnowzCode is installed (.claude/ exists)
   - Check for active WorkGroups:
     - Read knowzcode/workgroups/ directory
     - Look for in-progress WorkGroups
     - If active WorkGroups exist, WARN user and confirm proceed

3. **Create backups**:
   ```bash
   timestamp=$(date -u +"%Y%m%d_%H%M%S")
   mkdir -p .claude.backup.$timestamp
   mkdir -p knowzcode.backup.$timestamp
   cp -r .claude/* .claude.backup.$timestamp/
   cp -r knowzcode/* knowzcode.backup.$timestamp/
   ```

4. **Create backup manifest**:
   ```json
   {
     "timestamp": "{timestamp}",
     "source": "$1",
     "backup_path_claude": ".claude.backup.{timestamp}",
     "backup_path_knowzcode": "knowzcode.backup.{timestamp}",
     "status": "backup_complete"
   }
   ```

### Phase 2: Change Analysis

1. **Identify all changes**:
   - Use Glob to list all files in source
   - Use Glob to list all files in target
   - Use Bash diff to compare directories
   - Categorize into:
     - NEW: Files in source not in target
     - MODIFIED: Files in both but different
     - DELETED: Files in target not in source
     - CUSTOM: Files in target that appear customized

2. **Detect customizations**:
   - Compare against known default content (if available)
   - Look for:
     - Project-specific agent modifications
     - Custom commands
     - Added sections in documentation
     - Modified templates

3. **Identify data files** (must preserve):
   - knowzcode/knowzcode_tracker.md
   - knowzcode/knowzcode_log.md
   - knowzcode/knowzcode_project.md
   - knowzcode/environment_context.md
   - knowzcode/workgroups/*.md
   - knowzcode/specs/*.md (project-specific specs)

4. **Flag potential conflicts**:
   - Framework files with custom modifications
   - Structural changes to data files
   - Deleted files that may be in use

### Phase 3: Present Update Plan

PAUSE and present detailed plan:

```markdown
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
KNOWZCODE FRAMEWORK UPDATE PLAN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

**Source**: $1
**Target**: Current project

## Summary

**New Components**:
- Agents: {count} new agents to add
- Commands: {count} new commands to add
- Prompts: {count} new prompts to add
- Documentation: {count} new docs to add

**Updates Available**:
- Agents: {count} agents with improvements
- Commands: {count} commands with improvements
- Core docs: {count} docs with improvements
- Prompts: {count} prompts with improvements

**Customizations Detected**:
- Custom agents: {count} will be preserved
- Custom commands: {count} will be preserved
- Modified docs: {count} will create .new files for review

**Data Files (Protected)**:
✅ knowzcode_tracker.md - {n} entries will be preserved
✅ knowzcode_log.md - {n} events will be preserved
✅ knowzcode_project.md - content will be preserved
✅ workgroups/ - {n} files will be preserved
✅ specs/ - {n} specs will be preserved

**Potential Conflicts**: {count}
{List conflicts if any}

## Backups Created

📦 .claude.backup.{timestamp}/
📦 knowzcode.backup.{timestamp}/

## Actions to Take

1. Add {n} new files
2. Update {n} framework files
3. Preserve {n} custom files
4. Create {n} .new files for manual review
5. Skip {n} unchanged files

## Safety Guarantees

✅ All project data will be preserved
✅ All customizations will be protected
✅ Backups available for rollback
✅ No active WorkGroups will be affected
✅ Validation performed after update

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

**Proceed with update?** [Yes/No/Review Details]
```

### Phase 4: Execute Update (After Approval)

**Systematic application**:

1. **Add new files**:
   ```bash
   for new_file in {new_files}:
     cp {source}/{new_file} {target}/{new_file}
     echo "ADDED: {new_file}" >> update.log
   ```

2. **Update framework files** (no customizations):
   ```bash
   for updated_file in {updated_files}:
     cp {source}/{updated_file} {target}/{updated_file}
     echo "UPDATED: {updated_file}" >> update.log
   ```

3. **Handle custom files**:
   ```bash
   for custom_file in {custom_files}:
     cp {source}/{custom_file} {target}/{custom_file}.new
     echo "PRESERVED: {custom_file} (created .new for review)" >> update.log
   ```

4. **Preserve data files** (NEVER overwrite):
   ```bash
   # DO NOT copy these, only validate structure if needed
   - knowzcode/knowzcode_tracker.md
   - knowzcode/knowzcode_log.md
   - knowzcode/knowzcode_project.md
   - knowzcode/environment_context.md
   - knowzcode/workgroups/*.md
   ```

5. **Log every action** to `knowzcode/update_manifest.md`

### Phase 5: Validation and Reporting

1. **Run validation checks**:
   - Verify all expected files present
   - Check agents have YAML frontmatter:
     ```bash
     for agent in .claude/agents/*.md:
       head -n 3 $agent | grep -q "^---$" || echo "WARNING: $agent missing frontmatter"
     ```
   - Validate commands are readable
   - Ensure data files intact

2. **Test basic functionality**:
   - Check if kc-orchestrator can be invoked
   - Verify commands are registered
   - Test file operations work

3. **Generate update manifest**:
   Create/append to `knowzcode/update_manifest.md` with complete change log

4. **Create final report**:

```markdown
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ KNOWZCODE UPDATE COMPLETED SUCCESSFULLY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## Changes Applied

**Added**: {n} new files
**Updated**: {n} framework files
**Preserved**: {n} custom files
**Created for Review**: {n} .new files

## Data Integrity

✅ Tracker: {n} entries preserved
✅ Log: {n} events preserved
✅ Project metadata: Preserved
✅ WorkGroups: {n} files preserved
✅ Specs: {n} files preserved

## Files Requiring Review

{List any .new files created}

To review: `diff {original} {original}.new`

## Backup Location

Rollback available at:
- .claude.backup.{timestamp}/
- knowzcode.backup.{timestamp}/

To rollback: `/kc-rollback {timestamp}`

## Next Steps

1. ✓ Review any .new files and merge manually if desired
2. ✓ Test orchestration: `/kc-step 1A` (dry run)
3. ✓ Read update manifest: `knowzcode/update_manifest.md`
4. ✓ Remove .new files after review: `rm .claude/**/*.new`

## Validation Results

✅ All components present
✅ Agents have valid format
✅ Commands are functional
✅ Data files intact
✅ No broken references

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

**KnowzCode framework successfully updated!**
```

### Error Handling

If ANY step fails:

1. **Immediately stop**
2. **Log error**:
   ```markdown
   ERROR at Phase {n}, Step {m}:
   {error_message}

   State: Partial update applied
   Action: Initiating automatic rollback
   ```

3. **Attempt rollback**:
   ```bash
   rm -rf .claude/
   rm -rf knowzcode/
   cp -r .claude.backup.{timestamp}/* .claude/
   cp -r knowzcode.backup.{timestamp}/* knowzcode/
   ```

4. **Verify rollback**:
   - Check all files restored
   - Test basic functionality
   - Report rollback status

5. **Report to user**:
   ```markdown
   ⚠️ UPDATE FAILED - SYSTEM ROLLED BACK

   Error: {error_message}
   Phase: {phase_name}

   Status: ✅ Rollback successful, original state restored

   The system is in its original state before update attempt.
   Backups preserved at: .claude.backup.{timestamp}/

   Recommended: Review error and try again with different options
   ```

## Critical Rules

1. **NEVER overwrite without backup**
2. **ALWAYS preserve project data files**
3. **PAUSE for approval** before applying changes
4. **ROLLBACK on any error** automatically
5. **LOG every action** for audit trail
6. **VALIDATE after completion**
7. **PROVIDE clear next steps**

The update-coordinator will ensure your project data stays safe while bringing in framework improvements.
