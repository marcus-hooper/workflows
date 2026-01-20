# workflows

Reusable GitHub Actions workflows for Teams notifications and CI/CD utilities (commit history, Adaptive Cards). PowerShell 7, UTF‑8 safe.

## Available Workflows

### Get Commit Messages

Retrieves recent commit history formatted for Microsoft Adaptive Cards.

**Usage:**

```yaml
jobs:
  get-commits:
    uses: <owner>/workflows/.github/workflows/get-commit-messages.yml@main
    with:
      commit_count: '10'  # optional, default: 10
```

**Inputs:**

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `commit_count` | Number of recent commits to retrieve | No | `10` |

**Outputs:**

| Output | Description |
|--------|-------------|
| `commit_messages` | JSON array of commit objects with `title` (time ago) and `value` (linked message with author) |

**Output Example:**

```json
[
  {"title": "2h ago", "value": "[Fix bug](https://github.com/owner/repo/commit/abc123) (Author Name)"},
  {"title": "1d ago", "value": "[Add feature](https://github.com/owner/repo/commit/def456) (Author Name)"}
]
```

## Requirements

- Runs on `ubuntu-latest`
- Uses `jq` for JSON processing (pre-installed on GitHub runners)
