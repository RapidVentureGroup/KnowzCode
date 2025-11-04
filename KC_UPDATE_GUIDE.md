># KnowzCode Framework Update Guide

## Overview

The `/kc-update` command intelligently merges improvements from a newer KnowzCode version into your active project while preserving all your customizations and project data.

## When to Update

Update your KnowzCode framework when:
- ✅ New orchestration features available
- ✅ Bug fixes in loop mechanics
- ✅ Improved agent definitions
- ✅ Enhanced command functionality
- ✅ New prompts or documentation

## Safety Guarantees

The update system provides comprehensive protection:

### Automatic Backups
- Complete backup created before ANY changes
- Timestamped for easy identification
- Includes both `.claude/` and `knowzcode/` directories
- Rollback command available: `/kc-rollback {timestamp}`

### Data Preservation
**NEVER overwritten** (always preserved):
- `knowzcode/knowzcode_tracker.md` - Your tracking data
- `knowzcode/knowzcode_log.md` - Your project history
- `knowzcode/knowzcode_project.md` - Your project documentation
- `knowzcode/environment_context.md` - Your environment config
- `knowzcode/workgroups/*.md` - Active WorkGroup state
- `knowzcode/specs/*.md` - Your specifications

### Customization Protection
- Detects custom modifications to framework files
- Creates `.new` files for review instead of overwriting
- Allows manual merge of improvements
- Preserves project-specific agents and commands

### Conflict Resolution
- Identifies conflicts before applying changes
- Presents options for each conflict
- Allows selective update application
- Provides detailed conflict reports

## Usage

### Basic Update

```bash
/kc-update /path/to/newer/knowzcode
```

This will:
1. Validate source contains valid KnowzCode
2. Create backups of current installation
3. Analyze differences between versions
4. Present detailed update plan
5. **PAUSE** for your approval
6. Apply approved changes
7. Validate update success
8. Report results

### Update Process Flow

```
┌─────────────────────────────────────────────────────┐
│ 1. VALIDATION PHASE                                 │
│    • Check source path valid                        │
│    • Verify KnowzCode installed                     │
│    • Confirm no active WorkGroups blocking          │
└────────────────┬────────────────────────────────────┘
                 ▼
┌─────────────────────────────────────────────────────┐
│ 2. BACKUP PHASE                                     │
│    • Create timestamped backups                     │
│    • Backup both .claude/ and knowzcode/            │
│    • Create backup manifest                         │
└────────────────┬────────────────────────────────────┘
                 ▼
┌─────────────────────────────────────────────────────┐
│ 3. ANALYSIS PHASE                                   │
│    • Diff source vs target                          │
│    • Identify: New, Modified, Deleted, Custom       │
│    • Detect data files to protect                   │
│    • Flag potential conflicts                       │
└────────────────┬────────────────────────────────────┘
                 ▼
┌─────────────────────────────────────────────────────┐
│ 4. APPROVAL GATE (USER INPUT REQUIRED)             │
│    • Present detailed update plan                   │
│    • Show what will change                          │
│    • List protections in place                      │
│    • Await: Approve / Reject / Review               │
└────────────────┬────────────────────────────────────┘
                 ▼
┌─────────────────────────────────────────────────────┐
│ 5. EXECUTION PHASE                                  │
│    • Add new files                                  │
│    • Update framework files                         │
│    • Preserve custom files                          │
│    • Create .new for conflicts                      │
│    • Never touch data files                         │
└────────────────┬────────────────────────────────────┘
                 ▼
┌─────────────────────────────────────────────────────┐
│ 6. VALIDATION PHASE                                 │
│    • Verify all components present                  │
│    • Check agents have YAML frontmatter             │
│    • Validate commands functional                   │
│    • Confirm data files intact                      │
│    • Test basic orchestration                       │
└────────────────┬────────────────────────────────────┘
                 ▼
┌─────────────────────────────────────────────────────┐
│ 7. REPORTING PHASE                                  │
│    • Generate update manifest                       │
│    • Create detailed change log                     │
│    • List .new files for review                     │
│    • Provide next steps                             │
└─────────────────────────────────────────────────────┘
```

## Update Plan Example

When you run `/kc-update`, you'll see:

```markdown
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
KNOWZCODE FRAMEWORK UPDATE PLAN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Source: /path/to/newer/knowzcode
Target: /mnt/c/Code/YourProject

## Summary

New Components:
- Agents: 2 new agents to add
- Commands: 1 new command to add
- Prompts: 5 new prompts to add

Updates Available:
- Agents: 8 agents with improvements
- Commands: 3 commands with improvements
- Core docs: 2 docs with improvements

Customizations Detected:
- Custom agents: 1 will be preserved
- Modified docs: 2 will create .new files for review

Data Files (Protected):
✅ knowzcode_tracker.md - 45 entries will be preserved
✅ knowzcode_log.md - 128 events will be preserved
✅ knowzcode_project.md - content will be preserved
✅ workgroups/ - 2 active WorkGroups will be preserved
✅ specs/ - 23 specs will be preserved

Potential Conflicts: 2
- .claude/commands/kc.md (you have custom modifications)
- knowzcode/knowzcode_loop.md (custom sections detected)

## Backups Created

📦 .claude.backup.20250104_200000/
📦 knowzcode.backup.20250104_200000/

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Proceed with update? [Yes/No/Review Details]
```

## Handling Conflicts

When conflicts are detected, you have options:

### Option A: Use Source Version
Replace your customization with the new version from source.
- **Use when**: Framework improvements are more valuable than your changes
- **Result**: Your customization is lost (but backed up)

### Option B: Keep Current Version
Skip updating this file, keep your version.
- **Use when**: Your customizations are critical
- **Result**: You won't get framework improvements for this file

### Option C: Create .new File
Copy source version as `{filename}.new` for manual review.
- **Use when**: You want both versions to compare
- **Result**: You can manually merge improvements
- **Recommended**: This is the safest option

### Option D: Smart Merge
Attempt intelligent merge of both versions.
- **Use when**: Changes are in different sections
- **Result**: Combined version (may need manual review)

## Post-Update Tasks

After successful update:

### 1. Review .new Files

```bash
# Find all .new files created
find .claude knowzcode -name "*.new"

# Compare with originals
diff .claude/commands/kc.md .claude/commands/kc.md.new

# Merge improvements manually if desired
# Then remove .new file
rm .claude/commands/kc.md.new
```

### 2. Test Orchestration

```bash
# Run a dry-run test
/kc-step 1A

# Verify agents respond
# Check commands work
# Confirm file operations succeed
```

### 3. Review Update Manifest

```bash
cat knowzcode/update_manifest.md
```

This shows complete history of what changed.

### 4. Clean Up

```bash
# After 30 days, remove old backups
rm -rf .claude.backup.20241205_*
rm -rf knowzcode.backup.20241205_*
```

## Rollback

If something goes wrong after update:

### Immediate Rollback

```bash
/kc-rollback 20250104_200000
```

This will:
- Create emergency backup of current state
- Restore from specified backup
- Validate restoration
- Report success

### List Available Backups

```bash
ls -d .claude.backup.* knowzcode.backup.*
```

### Manual Rollback (if command fails)

```bash
# Restore .claude
rm -rf .claude/*
cp -r .claude.backup.20250104_200000/* .claude/

# Restore knowzcode
rm -rf knowzcode/*
cp -r knowzcode.backup.20250104_200000/* knowzcode/
```

## Advanced Usage

### Dry Run (Preview Only)

```bash
/kc-update /path/to/source --dry-run
```

Shows what would change without applying updates.

### Preserve Custom Strategy

```bash
/kc-update /path/to/source strategy=preserve-custom
```

Automatically chooses to preserve customizations when conflicts detected.

### Force Replace Strategy

```bash
/kc-update /path/to/source strategy=force-replace
```

⚠️ **Dangerous**: Replaces customizations with source versions. Use carefully.

## Best Practices

### Before Updating

1. ✅ **Commit current work** to git
2. ✅ **Close active WorkGroups** or note them
3. ✅ **Review changelog** from source (if available)
4. ✅ **Test in non-production** first (if possible)

### During Update

1. ✅ **Read the update plan** carefully
2. ✅ **Choose "Create .new"** for conflicts when uncertain
3. ✅ **Note backup timestamp** for potential rollback
4. ✅ **Watch for warnings** about active WorkGroups

### After Update

1. ✅ **Review ALL .new files** created
2. ✅ **Test basic workflow**: `/kc-step 1A`
3. ✅ **Check data files** preserved correctly
4. ✅ **Read update manifest** for details
5. ✅ **Keep backup** for at least 30 days

## Troubleshooting

### Update Fails

**Symptom**: Error during update execution

**Solution**:
1. System automatically rolls back
2. Check error message for cause
3. Fix issue (e.g., permissions, disk space)
4. Retry update

### Backups Not Found

**Symptom**: `/kc-rollback` can't find backup

**Solution**:
```bash
# List all backups
ls -la | grep backup

# Verify timestamp format
# Should be: .claude.backup.YYYYMMDD_HHMMSS
```

### Agent Format Errors

**Symptom**: Agents don't respond after update

**Solution**:
```bash
# Check YAML frontmatter
head -n 10 .claude/agents/*.md

# Should see:
# ---
# name: agent-name
# description: ...
# ---

# If missing, rollback and report issue
/kc-rollback {timestamp}
```

### Data Loss

**Symptom**: Tracker or log entries missing

**Solution**:
```bash
# This should NEVER happen
# Update system protects data files
# If occurs:

1. Immediately rollback
/kc-rollback {timestamp}

2. Verify data restored
cat knowzcode/knowzcode_tracker.md

3. Report bug with details
```

## FAQ

**Q: Will update overwrite my project data?**
A: No. Tracker, log, project, and workgroup files are NEVER overwritten.

**Q: What if I have custom agents?**
A: Custom agents are detected and preserved. Source versions created as .new files.

**Q: Can I rollback after committing to git?**
A: Yes, backups are independent of git. Use `/kc-rollback {timestamp}`.

**Q: How do I know what changed?**
A: Check `knowzcode/update_manifest.md` for complete change log.

**Q: What if I'm mid-WorkGroup?**
A: Update will warn you. Recommended: Finish WorkGroup first, or note it will be preserved.

**Q: How long to keep backups?**
A: Recommend 30 days. Remove older with `rm -rf *.backup.YYYYMMDD_*`

**Q: Can I update just agents, not commands?**
A: Not currently. Update is holistic. But you can selectively use .new files.

## Support

If you encounter issues:

1. Check this guide's troubleshooting section
2. Review update manifest for what changed
3. Try rollback if system unstable
4. Report bugs with:
   - Update plan output
   - Error messages
   - Backup timestamp
   - Current state description

## Summary

**Update Command**:
```bash
/kc-update /path/to/newer/knowzcode
```

**Rollback Command**:
```bash
/kc-rollback {timestamp}
```

**Safety Features**:
- ✅ Automatic backups
- ✅ Data preservation
- ✅ Customization protection
- ✅ Conflict resolution
- ✅ Validation checks
- ✅ Rollback capability

**What's Protected**:
- ✅ Tracker data
- ✅ Log history
- ✅ Project metadata
- ✅ WorkGroup state
- ✅ Specifications
- ✅ Custom modifications

The update system is designed to be **safe, intelligent, and reversible**. Your project data and customizations are always protected.
