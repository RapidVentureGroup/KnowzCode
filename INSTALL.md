# Installing KnowzCode

## Prerequisites

Before installing KnowzCode:
- You should have prepared your project vision (blueprint, project overview, architecture)
- You should have built an initial prototype based on those plans
- You should have Git initialized in your project

## Installation

### 1. Download KnowzCode

Download the latest release: [knowzcode-starter.zip](https://github.com/kaithoughtarchitect/knowzcode/releases/download/v2.0.0/knowzcode.starter.zip)

### 2. Add to Your Project

Extract the ZIP and add the `knowzcode` folder to your project:
- Drag and drop the folder into your project
- Upload the folder to your codebase (for cloud IDEs)
- Or extract directly in your project directory

That's it! You now have the KnowzCode framework in your project.

### 3. Run Automated Installation

The easiest way to install KnowzCode automation is using the built-in installer:

```
/kc-install
```

This command will:
- ✅ Copy all Claude Code automation files to `.claude/` directory
- ✅ Configure hooks for automatic quality enforcement
- ✅ Validate installation succeeded
- ✅ Provide clear next steps

**What gets installed:**
- **6 slash commands:** `/kc`, `/kc-step`, `/kc-audit`, `/kc-plan`, `/kc-microfix`, `/kc-install`
- **17+ skills:** Automation building blocks for tracker updates, spec validation, etc.
- **15+ subagents:** AI personas for specific tasks (spec-chief, implementation-lead, etc.)
- **5 hooks:** Pre/post execution validation (blocks incomplete specs, validates environment)

**Manual Installation (Advanced):**

If you prefer manual setup or `/kc-install` isn't available:

1. Copy the `claude/` directory to `.claude/` in your project root:
   ```bash
   cp -r claude/ .claude/
   ```

2. Create `.claude/settings.json` with hook configuration:
   ```json
   {
     "hooks": {
       "pre-command": {
         "kc-step": ["validate-environment", "confirm-change-set", "validate-specs"],
         "kc": ["validate-environment"]
       },
       "post-command": {
         "kc-step": ["update-caches", "git-status"]
       }
     }
   }
   ```

3. Verify installation:
   ```bash
   ls .claude/commands  # Should show 6 .yaml files
   ls .claude/skills    # Should show 17+ .json files
   cat .claude/settings.json  # Should show hook configuration
   ```

### 4. Run Install and Reconcile
Give your AI assistant this prompt:
```
knowzcode/prompts/ND__Install_And_Reconcile.md
```

### 5. Run System Audit
Verify everything is ready:
```
knowzcode/prompts/ND__Post_Installation_Audit.md
```
This ensures 100% architectural completeness and system readiness.

### 6. Review and Approve
When the AI pauses:
- Review audit report for any issues
- Address any critical gaps identified
- Confirm development readiness percentage

## Post-Installation
Once audit shows 100% readiness:
```
knowzcode/prompts/ND__Start_Work_Session.md
```

## What's Included

```
your-project/
└── knowzcode/                    # Everything inside!
    ├── knowzcode_project.md
    ├── knowzcode_architecture.md
    ├── knowzcode_tracker.md
    ├── knowzcode_log.md
    ├── knowzcode_loop.md
    ├── environment_context.md
    ├── specs/
    ├── planning/
    └── prompts/
```

## Documentation

- **Quick Start**: See [Getting Started Guide](./docs/knowzcode_getting_started.md)
- **Concepts**: See [Understanding KnowzCode](./docs/understanding-knowzcode.md)
- **Commands**: See [Prompts Guide](./docs/knowzcode_prompts_guide.md)

## Troubleshooting

### Common Issues

**Environment commands don't work**
- The `knowzcode/environment_context.md` file must be filled out during installation
- Test commands manually in your terminal
- Update the file with working commands

**AI seems confused**
- Ensure you've completed the full installation process
- Run `knowzcode/prompts/ND__Start_Work_Session.md` to sync
- Check that all files were properly updated

**Hooks not working**
- Verify `.claude/settings.json` exists and contains hook configuration
- Check that hook files exist in `.claude/hooks/pre-command/` and `.claude/hooks/post-command/`
- Try reinstalling: `/kc-install force=true`

**Installation failed**
- Ensure `claude/` template directory exists in your project
- Check that you have write permissions
- Try manual installation steps above

### Support

For issues or questions:
- Check the documentation links above
- Review the [Getting Started Guide](./docs/knowzcode_getting_started.md) for detailed instructions
- Open an issue on GitHub

---

*Note: KnowzCode is installed AFTER your initial build to document what actually exists, not just what was planned.*
