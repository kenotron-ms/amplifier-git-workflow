# Contributing to amplifier-git-workflow

Thank you for your interest in contributing!

## Development Setup

1. Clone the repository:
```bash
git clone https://github.com/kenotron-ms/amplifier-git-workflow.git
cd amplifier-git-workflow
```

2. Install prerequisites:
```bash
brew install gh
gh auth login
```

3. Test locally before submitting changes

## Making Changes

### Recipe Changes

When modifying recipes (`recipes/*.yaml`):

1. Update the recipe file
2. Test with a real git repository
3. Document changes in commit message
4. Update README.md if behavior changes

### Agent Changes

When modifying agents (`agents/*.md`):

1. Update the agent prompt/instructions
2. Test that the agent produces expected output
3. Consider if context/instructions.md needs updates
4. Update version in behaviors/git-workflow.yaml

### Documentation Changes

When updating docs:

1. Keep README.md, GETTING_STARTED.md, and context/instructions.md in sync
2. Provide examples for new features
3. Update troubleshooting section as needed

## Testing Guidelines

### Minimum Testing

Before submitting a PR:

- [ ] Test recipes locally using `amplifier tool invoke recipes`
- [ ] Verify recipes don't break existing functionality
- [ ] Check that agents produce expected output format
- [ ] Ensure documentation is accurate

### Comprehensive Testing

For significant changes:

- [ ] Test in multiple repositories
- [ ] Test with different git configurations
- [ ] Test failure scenarios (CI failures, conflicts)
- [ ] Test with and without auto-merge enabled

## Commit Message Format

Use conventional commits:

```
feat: add new feature
fix: resolve bug
docs: update documentation
refactor: improve code structure
test: add tests
chore: maintenance tasks
```

Include Amplifier attribution:

```
feat: add draft PR support

- Add --draft flag to submit-pr recipe
- Update documentation
- Add example usage

🤖 Generated with [Amplifier](https://github.com/microsoft/amplifier)

Co-Authored-By: Amplifier <240397093+microsoft-amplifier@users.noreply.github.com>
```

## Pull Request Process

1. Create a feature branch: `git checkout -b feature/your-feature`
2. Make your changes with tests
3. Update documentation
4. Commit with conventional commit format
5. Push to your fork
6. Open a PR with description of changes

### PR Requirements

- [ ] Clear description of what changed and why
- [ ] Documentation updated if needed
- [ ] Tested in real repository
- [ ] No breaking changes (or clearly documented)

## Code Style

### YAML Style

- Use 2 spaces for indentation
- Quote strings that contain special characters
- Keep lines under 100 characters when possible
- Add comments for complex logic

### Markdown Style

- Use headers hierarchically (don't skip levels)
- Keep line length reasonable
- Use code fences with language tags
- Include examples for clarity

## Questions?

Open an issue for discussion before starting major work.
