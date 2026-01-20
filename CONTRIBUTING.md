# Contributing to workflows

Thank you for your interest in contributing to workflows!

## Development Setup

### Prerequisites

- Git
- Basic understanding of GitHub Actions workflow syntax
- Familiarity with bash and `jq` for JSON processing

### Clone

**For external contributors** (fork first):

```bash
# Fork the repo on GitHub, then:
git clone https://github.com/YOUR-USERNAME/workflows.git
cd workflows
git remote add upstream https://github.com/marcus-hooper/workflows.git
```

**For maintainers** (direct access):

```bash
git clone https://github.com/marcus-hooper/workflows.git
cd workflows
```

### Testing

Workflows are tested by calling them from other repositories. No local test harness exists.

To test changes:

1. Push your branch to your fork
2. Call the workflow from a test repository using `uses: YOUR-USERNAME/workflows/.github/workflows/workflow-name.yml@your-branch`
3. Verify the outputs match expected behavior

## Workflow Conventions

### Trigger Pattern

All reusable workflows use `workflow_call`:

```yaml
on:
  workflow_call:
    inputs:
      input_name:
        description: 'Description of the input'
        required: false
        default: '10'
        type: string
    outputs:
      output_name:
        description: 'Description of the output'
        value: ${{ jobs.job-name.outputs.output_name }}
```

### Output Format

Use heredoc syntax with EOF delimiters for multi-line `GITHUB_OUTPUT`:

```bash
echo "output_name<<EOF" >> $GITHUB_OUTPUT
echo "$json_content" >> $GITHUB_OUTPUT
echo "EOF" >> $GITHUB_OUTPUT
```

### JSON Construction

Always use `jq` for safe JSON construction. Never use string concatenation:

```bash
# Correct - jq handles escaping
commit_json=$(jq -n --arg title "$time_ago" --arg value "$message" \
  '{title: $title, value: $value}')

# Incorrect - unsafe string concatenation
commit_json="{\"title\": \"$time_ago\", \"value\": \"$message\"}"
```

### Time-Ago Formatting

Format timestamps as relative time: `Xs`, `Xm`, `Xh`, `Xd` (seconds, minutes, hours, days).

### Runner and Compatibility

- Use `ubuntu-latest` for runners
- Ensure scripts are UTF-8 safe (handle special characters properly)
- Use `actions/checkout@v4` with appropriate `fetch-depth`

## Commit Messages

Use [Conventional Commits](https://www.conventionalcommits.org/) format:

```
<type>: <description>

[optional body]

[optional footer]
```

### Types

| Type | Description |
|------|-------------|
| `feat` | New feature |
| `fix` | Bug fix |
| `docs` | Documentation only |
| `refactor` | Code change that neither fixes a bug nor adds a feature |
| `ci` | CI/workflow changes |
| `chore` | Other maintenance tasks |

### Examples

```
feat: add workflow for PR labeling

fix: handle empty commit history gracefully

docs: update usage examples in README

ci: pin actions/checkout to SHA
```

### Breaking Changes

For breaking changes, use `!` after the type or add a `BREAKING CHANGE:` footer:

```
feat!: change output format from array to object

Output structure changed to support additional metadata.
```

## Pull Request Process

### Before Opening a PR

1. **Create a branch** from `main`:
   ```bash
   git checkout -b <type>/short-description
   ```

2. **Test your workflow** from another repository

3. **Update CHANGELOG.md** under `[Unreleased]`

4. **Update README.md** with new workflow documentation

### PR Requirements

| Requirement | Description |
|-------------|-------------|
| Tested | Workflow called successfully from test repository |
| Documented | README.md updated with usage, inputs, and outputs |
| Changelog | Entry added under `[Unreleased]` |

### PR Description

Include:

- Summary of changes
- Related issue (if any)
- Testing performed (which repository tested the workflow)

### Code Review

- All PRs require review before merge
- Address review feedback promptly
- Squash merge to `main`

## Issue Guidelines

### Bug Reports

Use the [bug report template](.github/ISSUE_TEMPLATE/bug_report.yml). Include:

- Workflow name and version/ref used
- Calling workflow snippet
- Expected vs actual behavior
- Relevant error messages from workflow logs

### Feature Requests

Use the [feature request template](.github/ISSUE_TEMPLATE/feature_request.yml). Include:

- Problem statement
- Proposed solution
- Example usage

## Release Process

Releases are managed by maintainers:

1. CHANGELOG.md updated with version and date
2. Tag created: `git tag -a v1.0.0 -m "Release v1.0.0"`
3. Tag pushed: `git push origin v1.0.0`

## Getting Help

- **Questions**: Open an [Issue](../../issues) with the `question` label
- **Bugs**: Open an [Issue](../../issues) using the bug report template
- **Features**: Open an [Issue](../../issues) using the feature request template

## License

By contributing, you agree that your contributions will be licensed under the [MIT License](LICENSE).
