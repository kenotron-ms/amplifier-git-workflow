---
bundle:
  name: amplifier-git-workflow
  version: 1.0.0
  description: |
    Automated git workflow for PR submission and monitoring.
    Provides autonomous PR creation, CI monitoring, and issue fixing.

includes:
  - bundle: git+https://github.com/microsoft/amplifier-foundation@main
  - bundle: ./behaviors/git-workflow.yaml

providers: {}
tools: {}
agents: {}
context: {}
---

# Amplifier Git Workflow Bundle

This bundle provides automated git workflows for PR submission and monitoring.

## Features

- Autonomous PR submission from uncommitted changes to merge
- Smart branch management and safety checks
- Documentation compliance automation
- PR standards discovery from repository conventions
- Continuous CI/CD monitoring with autonomous issue fixing
- Auto-merge support with cleanup

## Usage

This is a thin wrapper bundle that includes the foundation bundle and the git-workflow behavior.

For reusable installation in other projects, use the behavior directly:

```yaml
includes:
  - bundle: git+https://github.com/your-org/amplifier-git-workflow@main#subdirectory=behaviors/git-workflow
```

## Requirements

External tools (must be installed on your system):
- `gh` CLI - GitHub CLI for PR operations
- `git` - Version control

See README.md for installation instructions.
