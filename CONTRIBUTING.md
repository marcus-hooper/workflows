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

## Running CI Locally

Before pushing, validate your workflow files:

```bash
# YAML syntax validation (install: https://github.com/mikefarah/yq)
yq eval '.' .github/workflows/*.yml > /dev/null

# GitHub Actions lint (install: https://github.com/rhysd/actionlint)
# Includes ShellCheck integration for bash scripts in run: blocks
actionlint
```

> **Note**: `actionlint` automatically runs ShellCheck on embedded bash scripts when ShellCheck is installed, so separate ShellCheck invocation is unnecessary.

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
echo "output_name<<EOF" >> "$GITHUB_OUTPUT"
echo "$json_content" >> "$GITHUB_OUTPUT"
echo "EOF" >> "$GITHUB_OUTPUT"
```

> **Note**: Always quote `"$GITHUB_OUTPUT"` to handle paths with spaces. Never use the deprecated `::set-output` syntax.

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
- Pin actions to full commit SHA with version comment (e.g., `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.2.2`)
- Use appropriate `fetch-depth`: `0` for full history (tags, blame), specific number for recent commits only

### Security Practices

Every workflow in this repository must follow these security requirements:

| Practice | Requirement |
|----------|-------------|
| **Permissions** | Start with `permissions: {}` at workflow level, grant minimal permissions per job |
| **Harden Runner** | First step in every job: `step-security/harden-runner` with `egress-policy: block` |
| **Action Pinning** | Pin to full commit SHA, never tags (supply chain security) |
| **Checkout** | Always set `persist-credentials: false` on `actions/checkout` |
| **Timeouts** | Always set `timeout-minutes` on jobs to prevent runaway workflows |
| **Concurrency** | Use `concurrency.group` to prevent duplicate runs |

Example secure job structure:

```yaml
permissions: {}  # At workflow level

jobs:
  example:
    runs-on: ubuntu-latest
    timeout-minutes: 10
    permissions:
      contents: read  # Minimal per-job permissions
    steps:
      - name: Harden Runner
        uses: step-security/harden-runner@0634a2670c59f64b4a01f0f96f84700a4088b9f0 # v2.12.0
        with:
          egress-policy: block
          allowed-endpoints: >
            github.com:443

      - name: Checkout
        uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.2.2
        with:
          persist-credentials: false
```

## Coding Standards

### Bash Best Practices

```bash
# Use set -e for error handling
set -e

# Quote variables to handle spaces/special characters
echo "$variable"

# Use [[ ]] for conditionals (bash-specific, safer)
if [[ -z "$var" ]]; then
  echo "var is empty"
fi

# Check command success explicitly
if ! command -v jq &> /dev/null; then
  echo "jq is required but not installed"
  exit 1
fi
```

### Common Bash Pitfalls

**Variable scope in loops**: `while read` in a pipe runs in a subshell, so variables set inside won't persist:

```bash
# WRONG - $count will be 0 after the loop
count=0
echo -e "a\nb\nc" | while read -r line; do
  ((count++))
done
echo "$count"  # Outputs: 0

# CORRECT - use process substitution to preserve variables
count=0
while read -r line; do
  ((count++))
done < <(echo -e "a\nb\nc")
echo "$count"  # Outputs: 3
```

### Project-Specific Guidelines

| Topic | Guideline |
|-------|-----------|
| **UTF-8 encoding** | Handle special characters in commit messages, branch names |
| **JSON construction** | Always use `jq` for escaping, never string concatenation |
| **Output format** | Microsoft Adaptive Cards for Teams integration |
| **Shellcheck** | All bash scripts should pass `shellcheck -s bash` |

### Adding New Workflows

When adding a new reusable workflow:

1. Use `workflow_call` trigger with documented inputs/outputs
2. Add comprehensive input descriptions with defaults where appropriate
3. Use `jq` for all JSON construction
4. Follow all [Security Practices](#security-practices) (harden-runner, pinned actions, minimal permissions)
5. Set `timeout-minutes` and `concurrency` on all jobs
6. Document the workflow in README.md with usage examples
7. Test from another repository before submitting PR

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
| `test` | Adding or updating tests |
| `ci` | CI/workflow changes |
| `deps` | Dependency updates |
| `security` | Security improvements |
| `chore` | Other maintenance tasks |
| `perf` | Performance improvement |

### Examples

```
feat: add workflow for PR labeling

fix: handle empty commit history gracefully

docs: update usage examples in README

ci: pin actions/checkout to SHA

deps: update actions/checkout to v4

security: add input validation for webhook URLs
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

   Use branch prefixes that match your commit type:

   | Branch Prefix | Use For |
   |---------------|---------|
   | `feature/` | New features |
   | `fix/` | Bug fixes |
   | `docs/` | Documentation changes |
   | `refactor/` | Code refactoring |
   | `ci/` | CI/workflow changes |
   | `security/` | Security improvements |

2. **Run CI locally** (see above)

3. **Test your workflow** from another repository

4. **Update CHANGELOG.md** under `[Unreleased]`

5. **Update README.md** with new workflow documentation

### PR Requirements

| Requirement | Description |
|-------------|-------------|
| Tested | Workflow called successfully from test repository |
| Documented | README.md updated with usage, inputs, and outputs |
| Changelog | Entry added under `[Unreleased]` |

**Security Checks** (run automatically):

| Check | Workflow | Purpose |
|-------|----------|---------|
| CodeQL | codeql.yml | Static security analysis for Actions |
| OSSF Scorecard | scorecard.yml | Supply chain security analysis |
| Dependency Review | On PR | Flags vulnerable dependencies |

**Security Review Checklist** (for reviewers):

- [ ] Actions pinned to full SHA (not tags)
- [ ] `permissions: {}` at workflow level with minimal per-job grants
- [ ] `step-security/harden-runner` as first step in each job
- [ ] `persist-credentials: false` on all checkout steps
- [ ] `timeout-minutes` set on all jobs
- [ ] No secrets exposed in logs or outputs
- [ ] `jq --arg` used for all dynamic JSON construction

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

1. All CI checks pass on `main`
2. CHANGELOG.md updated with version and date
3. Tag created: `git tag -a v1.0.0 -m "Release v1.0.0"`
4. Tag pushed: `git push origin v1.0.0`
5. GitHub Actions creates release and updates major version tag (`v1`)

## Getting Help

- **Questions**: Open a [Discussion](../../discussions)
- **Bugs**: Open an [Issue](../../issues) using the bug report template
- **Features**: Open an [Issue](../../issues) using the feature request template
- **Security**: See [SECURITY.md](SECURITY.md) for responsible disclosure

## License

By contributing, you agree that your contributions will be licensed under the [MIT License](LICENSE).
