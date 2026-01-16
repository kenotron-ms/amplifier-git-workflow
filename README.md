# Amplifier Git Workflow Bundle

Automated git workflow for PR submission and monitoring, translated from the legacy amp `/submit-pr` command.

## Features

- **Autonomous PR Submission**: From uncommitted changes to merged PR
- **Smart Branch Management**: Auto-creates feature branches if on main/master
- **Documentation Compliance**: Automatically discovers and follows repo standards
- **Parallel Standards Discovery**: Learns PR conventions from existing PRs
- **Continuous Monitoring**: Polls PR status every 30 seconds
- **Autonomous Issue Fixing**: Downloads CI artifacts, fixes failures, pushes updates
- **Auto-merge Support**: Enables GitHub auto-merge and monitors until complete
- **Resumable Workflows**: Recipe checkpointing allows interruption recovery

## Requirements

### Foundation Tools (inherited)
- `bash` - For git commands (from foundation bundle)
- File system access (from foundation bundle)

### External CLI Tools (must be installed)
- **`gh` CLI** - GitHub CLI for PR operations
  - Install: `brew install gh` or visit https://cli.github.com
  - Auth: `gh auth login`
- **`git`** - Version control (usually pre-installed)

## Installation

### As a Behavior in Your Project

Add to your project's `bundle.md`:

```yaml
---
bundle:
  name: my-project
  version: 1.0.0

includes:
  - bundle: git+https://github.com/microsoft/amplifier-foundation@main
  - bundle: git+https://github.com/kenotron-ms/amplifier-git-workflow@v1.0.0#subdirectory=behaviors/git-workflow.yaml
---

# Your Project Assistant

...
```

### Standalone Usage

```bash
amplifier run --bundle git+https://github.com/kenotron-ms/amplifier-git-workflow@v1.0.0 \
  "Submit a PR for my changes"
```

## Usage

### Submit PR Workflow

```bash
# Conversational
amplifier run "Submit a PR for these changes"

# Explicit recipe invocation
amplifier tool invoke recipes operation=execute \
  recipe_path=git-workflow:recipes/submit-pr.yaml
```

The workflow will:
1. Verify PR safety configuration
2. Check git state and create feature branch if needed
3. Commit any uncommitted changes
4. Ensure documentation compliance (parallel)
5. Discover PR standards (parallel)
6. Push to remote
7. Create PR following discovered conventions
8. Monitor CI/CD status (polling every 30s)
9. Autonomously fix issues if CI fails
10. Clean up after merge completes

### Custom Configuration

Create `.git-pr-config.json` in your repo root to customize behavior:

```json
{
  "remote": {
    "origin": "your-org/your-repo",
    "target_branch": "develop"
  },
  "rules": {
    "allowed_base_branches": ["develop", "staging"],
    "forbidden_base_branches": ["main", "production"],
    "notes": [
      "This repo uses develop as the primary branch.",
      "Direct PRs to main are forbidden."
    ]
  }
}
```

## Architecture

### Recipes

- **`submit-pr.yaml`** - Main orchestration recipe
- **`monitor-cycle.yaml`** - Single monitoring iteration (30s check)
- **`fix-pr-issues.yaml`** - Autonomous issue resolution

### Agents

- **`pr-standards-checker`** - Validates PR quality before submission
- **`conflict-resolver`** - Analyzes and resolves merge conflicts
- **`documentation-checker`** - Ensures documentation is updated

### Pattern

Uses multi-recipe with bounded iteration (`foreach` 1-120) for resumable monitoring loops with checkpointing.

## Development

### Project Structure

```
amplifier-git-workflow/
├── bundle.md                      # Main bundle (thin wrapper)
├── behaviors/
│   └── git-workflow.yaml          # Reusable behavior
├── recipes/
│   ├── submit-pr.yaml             # Main recipe
│   ├── monitor-cycle.yaml         # Monitoring iteration
│   └── fix-pr-issues.yaml         # Issue fixing
├── agents/
│   ├── pr-standards-checker.md    # PR validation agent
│   ├── conflict-resolver.md       # Conflict resolution agent
│   └── documentation-checker.md   # Documentation compliance agent
├── context/
│   └── instructions.md            # Usage instructions
├── docs/
│   ├── PR_STANDARDS.md            # PR standards reference
│   └── WORKFLOW_GUIDE.md          # Detailed workflow guide
└── README.md
```

### Testing

```bash
# Test in a feature branch
cd your-test-repo
git checkout -b test-pr-workflow

# Make some changes
echo "test" > test.txt

# Run the workflow
amplifier tool invoke recipes operation=execute \
  recipe_path=git-workflow:recipes/submit-pr.yaml
```

## Migration from amp

This replaces the legacy amp `/submit-pr` command with:
- ✅ Modular recipes instead of 1132-line bash script
- ✅ Resumable workflows with checkpointing
- ✅ Specialized agents with loaded context
- ✅ Native parallel execution
- ✅ Testable sub-recipes
- ✅ Git-based distribution (composable)

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for development guidelines.

## License

MIT License - see [LICENSE](LICENSE)
