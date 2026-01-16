# Git Workflow System

You have access to automated git workflows via recipes.

## Available Workflows

### Submit PR: `git-workflow:recipes/submit-pr.yaml`

Fully autonomous PR submission workflow:
- Validates changes against PR standards
- Creates feature branch if on main/master
- Commits uncommitted changes with smart messages
- Ensures documentation compliance
- Discovers and follows repository PR conventions
- Creates PR with proper formatting
- Monitors CI status with 30-second polling
- Autonomously fixes issues when CI fails or reviews request changes
- Enables auto-merge and monitors until merge completes
- Cleans up local branches after merge

**Trigger patterns:**
- "Submit a PR"
- "Create a pull request"
- "Let's merge this"
- "Ready for review"
- "Open a PR for these changes"

**Usage:**
```bash
amplifier tool invoke recipes operation=execute \
  recipe_path=git-workflow:recipes/submit-pr.yaml
```

Or conversationally:
```
User: "Submit a PR for my changes"
Assistant: [Automatically invokes the submit-pr recipe]
```

## Available Agents

### pr-standards-checker
Validates PR quality before submission:
- Checks commit message format
- Validates PR description structure
- Ensures test coverage
- Verifies documentation updates

**Delegate to this agent when:** You need to validate changes meet PR standards before submission.

### conflict-resolver
Analyzes and resolves merge conflicts:
- Understands intent from commit history
- Categorizes conflicts (simple vs complex)
- Auto-resolves simple conflicts
- Requests human help for complex conflicts

**Delegate to this agent when:** Merge conflicts are detected during branch updates.

### documentation-checker
Ensures documentation compliance:
- Discovers repository documentation standards
- Identifies required documentation updates
- Runs required commands (lint, test, build)
- Updates documentation automatically

**Delegate to this agent when:** Code changes require documentation updates per repository standards.

## Configuration

### Repository PR Configuration

Create `.git-pr-config.json` in repository root to customize behavior:

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
      "This repo uses develop as the primary branch."
    ]
  }
}
```

This prevents accidental PRs to protected branches.

## External Requirements

These workflows require external tools (verify with `which` or `--version`):

- **`gh` CLI** - GitHub CLI for PR operations
  - Install: `brew install gh` or https://cli.github.com
  - Authenticate: `gh auth login`
  - Verify: `gh --version`

- **`git`** - Version control
  - Usually pre-installed
  - Verify: `git --version`

The recipes will check for required tools and fail gracefully with installation instructions if missing.

## Workflow Architecture

The submit-pr workflow uses a multi-recipe pattern with bounded monitoring:

1. **Prepare Stage**: Validation, branching, commits, compliance
2. **Create PR Stage**: Push, create PR, enable auto-merge
3. **Monitor Stage**: Foreach loop (max 120 iterations = 1 hour)
   - Each iteration: Check status → Fix if needed → Sleep 30s
   - Exits early when PR is merged
4. **Finalize Stage**: Cleanup and report

This architecture provides:
- ✅ Resumability via recipe checkpointing
- ✅ Autonomous issue fixing with bounded retries
- ✅ Early termination when merge completes
- ✅ Safety bounds (max 1 hour monitoring)
