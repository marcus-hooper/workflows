# workflows

[![CI](https://github.com/marcus-hooper/workflows/actions/workflows/ci.yml/badge.svg)](https://github.com/marcus-hooper/workflows/actions/workflows/ci.yml)
[![CodeQL](https://github.com/marcus-hooper/workflows/actions/workflows/codeql.yml/badge.svg)](https://github.com/marcus-hooper/workflows/actions/workflows/codeql.yml)
[![Security](https://github.com/marcus-hooper/workflows/actions/workflows/security.yml/badge.svg)](https://github.com/marcus-hooper/workflows/actions/workflows/security.yml)
[![OpenSSF Scorecard](https://api.scorecard.dev/projects/github.com/marcus-hooper/workflows/badge)](https://scorecard.dev/viewer/?uri=github.com/marcus-hooper/workflows)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

Reusable GitHub Actions workflows for Teams notifications and CI/CD utilities.

## Features

- Retrieves commit history formatted for Microsoft Adaptive Cards
- Time-ago formatting (Xs, Xm, Xh, Xd) for human-readable timestamps
- UTF-8 safe JSON construction using `jq`
- Designed for Microsoft Teams integration via webhooks
- Reusable via `workflow_call` trigger

## Quick Start

```yaml
jobs:
  get-commits:
    uses: marcus-hooper/workflows/.github/workflows/get-commit-messages.yml@v1
    with:
      commit_count: '5'
```

## Available Workflows

### Get Commit Messages

Retrieves recent commit history formatted for Microsoft Adaptive Cards.

#### Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `commit_count` | No | `10` | Number of recent commits to retrieve |

#### Outputs

| Output | Description |
|--------|-------------|
| `commit_messages` | JSON array of commit objects with `title` (time ago) and `value` (linked message with author) |

#### Output Format

```json
[
  {"title": "2h ago", "value": "[Fix bug](https://github.com/owner/repo/commit/abc123) (Author Name)"},
  {"title": "1d ago", "value": "[Add feature](https://github.com/owner/repo/commit/def456) (Author Name)"}
]
```

### Complete Workflow Example

Here's a complete deployment workflow using `get-commit-messages` with Teams notification:

```yaml
name: Deploy and Notify

on:
  push:
    branches: [main]

jobs:
  get-commits:
    uses: marcus-hooper/workflows/.github/workflows/get-commit-messages.yml@v1
    with:
      commit_count: '5'

  deploy:
    runs-on: ubuntu-latest
    needs: get-commits
    steps:
      - uses: actions/checkout@v4

      - name: Deploy to production
        run: |
          # Your deployment steps here
          echo "Deploying application..."

      - name: Send Teams notification
        if: ${{ always() }}
        uses: marcus-hooper/send-teams-notification@v1
        with:
          job_status: ${{ job.status }}
          environment: production
          commit_messages: ${{ needs.get-commits.outputs.commit_messages }}
          webhook_url: ${{ secrets.TEAMS_WEBHOOK_URL }}
```

## How It Works

1. Checks out the repository with shallow clone (`fetch-depth` matching `commit_count`)
2. Extracts commit data using `git log` (SHA, date, message, author)
3. Calculates relative time (seconds, minutes, hours, days ago)
4. Builds JSON array using `jq` for safe character escaping
5. Outputs JSON via `GITHUB_OUTPUT` heredoc syntax

## Requirements

- Runs on `ubuntu-latest`
- Uses `jq` for JSON processing (pre-installed on GitHub runners)
- Caller must have repository access (automatic for same-repo calls)

## Limitations

| Limitation | Details |
|------------|---------|
| Linux only | Workflow runs on `ubuntu-latest`; date commands use GNU syntax |
| Shallow clone | Only fetches commits up to `commit_count`; deeper history not available |
| Single-line title | Only first line of commit message is included |
| Time precision | Time-ago rounds down (e.g., 119 seconds shows as "1m ago") |

## Troubleshooting

### Common Issues

| Issue | Cause | Solution |
|-------|-------|----------|
| Empty `commit_messages` output | Repository has no commits or `fetch-depth: 0` in caller | Ensure commits exist and don't override `fetch-depth` |
| Missing commits | `commit_count` too low or shallow clone in caller | Increase `commit_count` or check caller's checkout step |
| Invalid JSON in downstream action | Special characters in commit messages | This workflow uses `jq` to escape; check downstream parsing |
| Time shows "0s ago" for all commits | System clock issue on runner | Rare; retry the workflow |

### Debug Tips

1. **Check workflow logs** - Expand the "Extract commit history" step to see raw git output
2. **Verify commit count** - Ensure `commit_count` is passed as a string (YAML type requirement)
3. **Test JSON output** - Add a step to echo `${{ needs.get-commits.outputs.commit_messages }}`
4. **Check repository history** - Run `git log -n 5` locally to verify commits exist

## Project Structure

```
workflows/
├── .github/
│   ├── dependabot.yml              # Dependency updates
│   ├── labels.yml                  # Repository label definitions
│   ├── ISSUE_TEMPLATE/
│   │   ├── bug_report.yml          # Bug report form
│   │   ├── feature_request.yml     # Feature request form
│   │   └── config.yml              # Issue template chooser
│   ├── PULL_REQUEST_TEMPLATE.md    # PR template
│   └── workflows/
│       ├── get-commit-messages.yml # Main reusable workflow
│       ├── ci.yml                  # CI validation
│       ├── codeql.yml              # CodeQL security analysis
│       ├── dependabot-auto-merge.yml
│       ├── labels.yml              # Label synchronization
│       ├── release.yml             # Version tag management
│       ├── schedule.yml            # Scheduled maintenance
│       ├── scorecard.yml           # OpenSSF Scorecard
│       └── security.yml            # Security scanning
├── CHANGELOG.md                    # Version history
├── CONTRIBUTING.md                 # Contribution guidelines
├── README.md                       # This file
└── SECURITY.md                     # Security policy
```

## Contributing

Contributions are welcome! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for detailed guidelines.

Quick start:

1. Check existing [issues](https://github.com/marcus-hooper/workflows/issues) or open a new one
2. Fork the repository
3. Create a feature branch (`git checkout -b feature/my-feature`)
4. Make your changes
5. Ensure CI passes
6. Submit a pull request

See the issue templates for [bug reports](.github/ISSUE_TEMPLATE/bug_report.yml) and [feature requests](.github/ISSUE_TEMPLATE/feature_request.yml).

## Related Projects

- [send-teams-notification](https://github.com/marcus-hooper/send-teams-notification) - Teams Adaptive Card notifications (pairs with this workflow)
- [deployment-notification-o365](https://github.com/marcus-hooper/deployment-notification-o365) - Email notifications via Microsoft Graph API
- [Adaptive Cards Designer](https://adaptivecards.io/designer/) - Visual card designer tool

## Security

See [SECURITY.md](SECURITY.md) for security policy and reporting vulnerabilities.

## Changelog

See [CHANGELOG.md](CHANGELOG.md) for version history.

## License

MIT License - see [LICENSE](LICENSE) for details.
