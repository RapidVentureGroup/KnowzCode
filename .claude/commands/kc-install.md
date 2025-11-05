---
description: "Install KnowzCode framework into a target project from this template repository"
argument-hint: "<target_path> [--force]"
---

# KnowzCode Installation

Install the complete KnowzCode framework into a target project directory.

**Target Path**: $1
**Options**: $2

## What Gets Installed

This command copies the KnowzCode framework from this template repository into your target project:

- ✅ **Agents**: All specialized sub-agents for orchestration
- ✅ **Commands**: Slash commands for workflow execution
- ✅ **Core Files**: knowzcode/ directory structure
- ✅ **Documentation**: Framework documentation
- ✅ **Prompts**: Reusable prompt templates

## Safety First

This command will:
- ✅ Validate target path exists and is a valid project
- ✅ Check for existing KnowzCode installation
- ✅ Create backups before overwriting (if --force used)
- ✅ Preserve project-specific data if updating
- ✅ Validate installation after completion
- ⚠️ Require --force flag to overwrite existing installation

## Execution

Use the update-coordinator sub-agent to orchestrate the installation process

Context:
- Source Path: $(pwd) (current directory - this template repository)
- Target Path: $1
- Mode: Fresh installation or forced reinstall
- Options: $2

Instructions for update-coordinator:

### Phase 1: Validation

1. **Validate arguments**:
   ```bash
   # Check if target path provided
   if [ -z "$1" ]; then
     echo "ERROR: Target path required"
     echo "Usage: /kc-install <target_path> [--force]"
     exit 1
   fi

   TARGET_PATH="$1"
   FORCE_FLAG="$2"
   ```

2. **Validate target path**:
   - Check that target path exists: `[ -d "$TARGET_PATH" ]`
   - Verify it's a directory, not a file
   - Check it appears to be a valid project (has .git, package.json, or *.csproj)
   - If not valid, ERROR and exit

3. **Check for existing installation**:
   ```bash
   if [ -d "$TARGET_PATH/.claude" ]; then
     if [ "$FORCE_FLAG" != "--force" ]; then
       ERROR: KnowzCode already installed at target

       Current installation detected:
       - Agents: {count .claude/agents/*.md}
       - Commands: {count .claude/commands/*.md}
       - Data: {check knowzcode/*.md files}

       To reinstall: /kc-install $TARGET_PATH --force

       EXIT
     else
       echo "FORCE mode: Will backup and reinstall"
     fi
   fi
   ```

### Phase 2: Backup (if --force)

If existing installation detected and --force flag present:

```bash
timestamp=$(date -u +"%Y%m%d_%H%M%S")
cd "$TARGET_PATH"

# Backup existing .claude directory
if [ -d ".claude" ]; then
  echo "Creating backup: .claude.backup.$timestamp"
  cp -r .claude ".claude.backup.$timestamp"
fi

# Backup existing knowzcode directory
if [ -d "knowzcode" ]; then
  echo "Creating backup: knowzcode.backup.$timestamp"
  cp -r knowzcode "knowzcode.backup.$timestamp"
fi

# Create backup manifest
cat > ".knowzcode.backup.$timestamp.json" <<EOF
{
  "timestamp": "$timestamp",
  "operation": "kc-install --force",
  "source": "/Users/alex/Code/RVG-KnowzCode",
  "backup_claude": ".claude.backup.$timestamp",
  "backup_knowzcode": "knowzcode.backup.$timestamp",
  "rollback_command": "/kc-rollback $timestamp"
}
EOF
```

### Phase 3: Installation

1. **Copy claude/ directory to .claude/**:
   ```bash
   SOURCE_PATH="$(pwd)"  # Current directory where command is run
   cd "$TARGET_PATH"

   # Remove existing if present
   [ -d ".claude" ] && rm -rf .claude

   # Copy framework files from source template
   cp -r "$SOURCE_PATH/claude" .claude

   echo "INSTALLED: .claude/ directory"
   ```

2. **Copy knowzcode/ directory**:
   ```bash
   # Preserve existing data files if they exist
   PRESERVE_FILES=(
     "knowzcode/knowzcode_tracker.md"
     "knowzcode/knowzcode_log.md"
     "knowzcode/knowzcode_project.md"
     "knowzcode/environment_context.md"
   )

   # Backup data files temporarily
   mkdir -p .knowzcode_temp_data
   for file in "${PRESERVE_FILES[@]}"; do
     if [ -f "$file" ]; then
       cp "$file" ".knowzcode_temp_data/$(basename $file)"
       echo "PRESERVED: $file"
     fi
   done

   # Backup workgroups and specs
   [ -d "knowzcode/workgroups" ] && cp -r knowzcode/workgroups .knowzcode_temp_data/
   [ -d "knowzcode/specs" ] && cp -r knowzcode/specs .knowzcode_temp_data/

   # Copy framework knowzcode directory
   [ -d "knowzcode" ] && rm -rf knowzcode
   cp -r "$SOURCE_PATH/knowzcode" .

   # Restore preserved data files
   for file in "${PRESERVE_FILES[@]}"; do
     backup_file=".knowzcode_temp_data/$(basename $file)"
     if [ -f "$backup_file" ]; then
       cp "$backup_file" "$file"
       echo "RESTORED: $file"
     fi
   done

   # Restore workgroups and specs
   [ -d ".knowzcode_temp_data/workgroups" ] && cp -r .knowzcode_temp_data/workgroups/* knowzcode/workgroups/
   [ -d ".knowzcode_temp_data/specs" ] && cp -r .knowzcode_temp_data/specs/* knowzcode/specs/

   # Cleanup
   rm -rf .knowzcode_temp_data

   echo "INSTALLED: knowzcode/ directory"
   ```

3. **Initialize data files if new installation**:
   ```bash
   # Only create these if they don't exist

   if [ ! -f "knowzcode/knowzcode_tracker.md" ]; then
     cat > knowzcode/knowzcode_tracker.md <<'EOF'
# KnowzCode Tracker

## Active WorkGroups

None

## Completed WorkGroups

None

## Statistics

- Total WorkGroups: 0
- Completed: 0
- In Progress: 0
EOF
     echo "CREATED: knowzcode/knowzcode_tracker.md"
   fi

   if [ ! -f "knowzcode/knowzcode_log.md" ]; then
     cat > knowzcode/knowzcode_log.md <<EOF
# KnowzCode Event Log

## Installation

**$(date -u +"%Y-%m-%d %H:%M:%S UTC")**: KnowzCode framework installed
EOF
     echo "CREATED: knowzcode/knowzcode_log.md"
   fi
   ```

4. **Create installation manifest**:
   ```bash
   cat > knowzcode/installation_manifest.md <<EOF
# KnowzCode Installation Manifest

**Installed**: $(date -u +"%Y-%m-%d %H:%M:%S UTC")
**Source**: $SOURCE_PATH
**Target**: $TARGET_PATH
**Installation Type**: $([ "$FORCE_FLAG" = "--force" ] && echo "Forced Reinstall" || echo "Fresh Install")

## Components Installed

### Agents ($(ls -1 .claude/agents/*.md 2>/dev/null | wc -l))

$(ls -1 .claude/agents/*.md 2>/dev/null | xargs -n1 basename)

### Commands ($(ls -1 .claude/commands/*.md 2>/dev/null | wc -l))

$(ls -1 .claude/commands/*.md 2>/dev/null | xargs -n1 basename)

### Core Files

- knowzcode/knowzcode_loop.md
- knowzcode/knowzcode_project.md
- knowzcode/knowzcode_tracker.md
- knowzcode/knowzcode_log.md
- knowzcode/environment_context.md

### Directories

- knowzcode/prompts/
- knowzcode/workgroups/
- knowzcode/specs/
- knowzcode/planning/

## Next Steps

1. **Configure environment**: Edit \`knowzcode/environment_context.md\`
2. **Review project setup**: Edit \`knowzcode/knowzcode_project.md\`
3. **Start first workflow**: Run \`/kc\` or \`/kc-step 1A\`

## Rollback

$([ "$FORCE_FLAG" = "--force" ] && echo "Backup created: .claude.backup.$timestamp" || echo "No backup needed (fresh install)")
EOF
   ```

### Phase 4: Validation

1. **Verify installation structure**:
   ```bash
   errors=()

   # Check required directories
   [ ! -d ".claude/agents" ] && errors+=("Missing: .claude/agents/")
   [ ! -d ".claude/commands" ] && errors+=("Missing: .claude/commands/")
   [ ! -d "knowzcode" ] && errors+=("Missing: knowzcode/")
   [ ! -d "knowzcode/prompts" ] && errors+=("Missing: knowzcode/prompts/")
   [ ! -d "knowzcode/workgroups" ] && errors+=("Missing: knowzcode/workgroups/")

   # Check required files
   [ ! -f "knowzcode/knowzcode_loop.md" ] && errors+=("Missing: knowzcode_loop.md")
   [ ! -f "knowzcode/knowzcode_tracker.md" ] && errors+=("Missing: knowzcode_tracker.md")

   # Check agent count
   agent_count=$(ls -1 .claude/agents/*.md 2>/dev/null | wc -l)
   [ "$agent_count" -lt 5 ] && errors+=("Too few agents: $agent_count")

   # Check command count
   command_count=$(ls -1 .claude/commands/*.md 2>/dev/null | wc -l)
   [ "$command_count" -lt 3 ] && errors+=("Too few commands: $command_count")

   if [ ${#errors[@]} -gt 0 ]; then
     echo "VALIDATION FAILED:"
     printf '%s\n' "${errors[@]}"
     exit 1
   fi
   ```

2. **Validate agent format**:
   ```bash
   for agent in .claude/agents/*.md; do
     # Check for YAML frontmatter
     if ! head -n 1 "$agent" | grep -q "^---$"; then
       echo "WARNING: $agent missing YAML frontmatter"
     fi
   done
   ```

### Phase 5: Report Success

Present final installation report:

```markdown
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ KNOWZCODE INSTALLATION SUCCESSFUL
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## Target Project

**Path**: $TARGET_PATH
**Installed**: $(date -u +"%Y-%m-%d %H:%M:%S UTC")

## Components Installed

**Agents**: {count} specialized sub-agents
**Commands**: {count} slash commands
**Prompts**: {count} reusable templates
**Core Files**: {count} framework files

## Installation Details

✅ Framework files: .claude/
✅ Core system: knowzcode/
✅ WorkGroup tracking: knowzcode/knowzcode_tracker.md
✅ Event logging: knowzcode/knowzcode_log.md
✅ Project metadata: knowzcode/knowzcode_project.md

$([ "$FORCE_FLAG" = "--force" ] && cat <<BACKUP

## Backups Created

📦 .claude.backup.$timestamp/
📦 knowzcode.backup.$timestamp/
📄 .knowzcode.backup.$timestamp.json

**Rollback**: \`/kc-rollback $timestamp\`

BACKUP
)

## Available Commands

Now available in your project:

- \`/kc\` - Start a new KnowzCode workflow
- \`/kc-step <phase>\` - Execute specific loop phases
- \`/kc-audit\` - Run quality audits
- \`/kc-plan\` - Strategic planning workflows
- \`/kc-microfix\` - Quick fixes for minor issues
- \`/kc-update <source>\` - Update framework from newer version
- \`/kc-rollback <timestamp>\` - Rollback to previous version

## Next Steps

### 1. Configure Your Environment

Edit \`knowzcode/environment_context.md\` with your project details:
- Build commands (dotnet build, npm run build, etc.)
- Test commands
- Server start commands
- Project-specific tools

### 2. Review Project Setup

Edit \`knowzcode/knowzcode_project.md\` with:
- Project name and description
- Technology stack
- Architecture patterns
- Coding standards

### 3. Start Your First Workflow

Quick start:
\`\`\`
/kc "Add user authentication"
\`\`\`

Or step-by-step:
\`\`\`
/kc-step 1A "Add user authentication"
\`\`\`

### 4. Explore the Framework

- Read: \`knowzcode/knowzcode_loop.md\` - Understand the workflow
- Review: \`knowzcode/prompts/\` - Available prompt templates
- Check: \`knowzcode/installation_manifest.md\` - Installation details

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

**KnowzCode is ready to use!** 🚀

Start automating your development workflow with quality-first practices.
```

### Error Handling

If ANY step fails:

1. **Stop immediately**
2. **Log error details**
3. **Attempt cleanup** (remove partial installation)
4. **Restore backups** (if --force was used)
5. **Report failure** with clear error message

```markdown
⚠️ INSTALLATION FAILED

**Error**: {error_message}
**Phase**: {phase_name}
**Step**: {step_details}

## Status

❌ Installation incomplete
$([ "$FORCE_FLAG" = "--force" ] && echo "✅ Backups preserved" || echo "ℹ️  No backups needed")

## What Happened

{Detailed error explanation}

## Recovery

$([ "$FORCE_FLAG" = "--force" ] && cat <<RECOVERY

Your previous installation is safe in:
- .claude.backup.$timestamp/
- knowzcode.backup.$timestamp/

To restore: \`/kc-rollback $timestamp\`

RECOVERY
)

## Next Steps

1. Review the error message above
2. Check that target path is a valid project directory
3. Ensure you have write permissions
4. Try again or contact support

**Note**: Partial installation files have been removed.
```

## Critical Rules

1. **NEVER overwrite without backup** (when --force used)
2. **ALWAYS preserve project data files** (tracker, log, workgroups, specs)
3. **VALIDATE arguments** before any operations
4. **CHECK installation structure** before reporting success
5. **CLEANUP on failure** to avoid partial installations
6. **PROVIDE clear next steps** for the user
7. **LOG all operations** for audit trail

## Usage Examples

Fresh installation:
```
/kc-install /Users/alex/Code/MyProject
```

Reinstall (update):
```
/kc-install /Users/alex/Code/MyProject --force
```

The update-coordinator will ensure safe, reliable installation of KnowzCode into your target project.
