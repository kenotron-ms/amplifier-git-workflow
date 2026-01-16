# Getting Started with amplifier-git-workflow

This guide will help you start using the git-workflow bundle in your projects.

## Quick Start

### 1. Install Prerequisites

```bash
# Install GitHub CLI
brew install gh

# Authenticate with GitHub
gh auth login

# Verify installation
gh --version
git --version
```

### 2. Add to Your Project (e.g., ampbox)

Edit your project's `bundle.md`:

```yaml
---
bundle:
  name: ampbox
  version: 1.0.0

includes:
  - bundle: git+https://github.com/microsoft/amplifier-foundation@main
  - bundle: git+https://github.com/kenotron-ms/amplifier-git-workflow@main#subdirectory=behaviors/git-workflow
---

# Ampbox Assistant

@ampbox:context/project-guidelines.md

---

@foundation:context/shared/common-system-base.md
```

### 3. Test the Workflow

In your project directory:

```bash
# Make some changes
echo "test" > test-file.txt

# Submit a PR (conversational)
amplifier run "Submit a PR for these test changes"

# Or use explicit recipe invocation
amplifier tool invoke recipes operation=execute \
  recipe_path=git-workflow:recipes/submit-pr.yaml
```

## Testing This Bundle Locally

Before publishing or using in other projects, test the bundle:

### 1. Test Recipes Independently

```bash
cd /Users/ken/workspace/amplifier-git-workflow

# Create a test branch
git checkout -b test-recipe-validation

# Make a small change
echo "# Test" > TEST.md
git add TEST.md

# Test the submit-pr recipe
amplifier tool invoke recipes operation=execute \
  recipe_path=./recipes/submit-pr.yaml
```

### 2. Test in Ampbox

```bash
cd /Users/ken/workspace/ampbox

# Edit bundle.md to include local path during development
# includes:
#   - bundle: /Users/ken/workspace/amplifier-git-workflow/behaviors/git-workflow.yaml

# Test it
amplifier run "Submit a PR for my changes"
```

## Development Workflow

### Making Changes to the Bundle

1. **Edit recipe files** in `recipes/`
2. **Update agents** in `agents/` if logic changes
3. **Test locally** using the path above
4. **Commit and push** to GitHub
5. **Update version** in `behaviors/git-workflow.yaml`
6. **Test in projects** using the git URL

### Adding New Features

To add a new recipe or agent:

1. Create the file in the appropriate directory
2. Add reference in `behaviors/git-workflow.yaml` if it's an agent
3. Document in `README.md`
4. Add usage instructions in `context/instructions.md`
5. Test thoroughly
6. Commit and push

## Repository Configuration

### Optional: PR Safety Configuration

Create `.git-pr-config.json` in repositories where you want to control PR targets:

```json
{
  "remote": {
    "origin": "kenotron-ms/ampbox",
    "target_branch": "develop"
  },
  "rules": {
    "allowed_base_branches": ["develop", "staging"],
    "forbidden_base_branches": ["main", "production"],
    "notes": [
      "This repo uses develop as the primary branch.",
      "Direct PRs to main are forbidden for safety."
    ]
  }
}
```

This prevents accidentally creating PRs to the wrong branch.

## Troubleshooting

### "gh CLI not found"

```bash
brew install gh
gh auth login
```

### "Recipe not found"

The bundle might not be loaded. Try:

```bash
# Explicit bundle load
amplifier run --bundle git+https://github.com/kenotron-ms/amplifier-git-workflow@main \
  "Submit a PR"
```

### "Tool recipes not found"

The recipes tool comes from the recipes bundle. Ensure foundation is included:

```yaml
includes:
  - bundle: git+https://github.com/microsoft/amplifier-foundation@main
```

### Recipe timeout during monitoring

The monitoring loop has a 120-iteration limit (1 hour). If it times out:
- Check if the PR is still pending checks
- You can resume the monitoring manually by re-running
- Or use `gh pr merge` manually if checks passed

## Next Steps

### Phase 1: Core Testing (Do This First)
- [ ] Test `submit-pr.yaml` in a real repository
- [ ] Verify branch creation works
- [ ] Verify commit message generation works
- [ ] Verify PR creation follows standards

### Phase 2: Monitoring Loop
- [ ] Test the monitoring cycle
- [ ] Verify it detects when PR is merged
- [ ] Verify it sleeps 30 seconds between checks
- [ ] Extend foreach array to 120 for production use

### Phase 3: Issue Fixing
- [ ] Create a PR that fails CI
- [ ] Verify `fix-pr-issues.yaml` is invoked
- [ ] Verify it downloads artifacts
- [ ] Verify it fixes and pushes
- [ ] Verify monitoring resumes

### Phase 4: Agent Specialization
- [ ] Test `pr-standards-checker` discovers conventions
- [ ] Test `conflict-resolver` handles merge conflicts
- [ ] Test `documentation-checker` updates docs
- [ ] Refine agent prompts based on real usage

### Phase 5: Production Use
- [ ] Use in ampbox for real PRs
- [ ] Collect feedback on workflow
- [ ] Iterate on recipes based on experience
- [ ] Consider additional features (draft PRs, label management, etc.)

## Common Patterns

### Using in Multiple Projects

Once published to GitHub, include in any project:

```yaml
includes:
  - bundle: git+https://github.com/kenotron-ms/amplifier-git-workflow@main#subdirectory=behaviors/git-workflow
```

### Version Pinning

Pin to specific version for stability:

```yaml
includes:
  - bundle: git+https://github.com/kenotron-ms/amplifier-git-workflow@v1.0.0#subdirectory=behaviors/git-workflow
```

### Local Development

Use local path while developing:

```yaml
includes:
  - bundle: /Users/ken/workspace/amplifier-git-workflow/behaviors/git-workflow.yaml
```

## Resources

- **Repository**: https://github.com/kenotron-ms/amplifier-git-workflow
- **Legacy amp command**: `~/workspace/amp/plugins/git/commands/submit-pr.md`
- **Recipe examples**: `amplifier:recipes/examples/`
- **Foundation docs**: `foundation:docs/BUNDLE_GUIDE.md`

## Support

Open an issue on GitHub if you encounter problems or have feature requests.
