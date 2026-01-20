# Changelog

All notable changes to workflows are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [1.1.0] - 2026-01-19

### Added

- CI workflow with CHANGELOG validation and actionlint (includes YAML and ShellCheck)
- CodeQL workflow for static application security testing (SAST)
- OSSF Scorecard workflow for supply chain security analysis
- Dependabot auto-merge workflow for patch and minor updates
- Release workflow with major version tag updates, SBOM generation, and attestation
- Scheduled health check workflow with automatic issue creation on failure
- Security workflow with Gitleaks secret scanning and unsafe pattern detection
- Label sync workflow for automatic repository label management
- Dependency review configuration with license allow-list
- Issue templates converted to YAML form-based format
- Pull request template with checklist and structured sections
- Repository labels configuration file with type, priority, status, and area labels

### Changed

- Expanded Dependabot configuration with grouped updates, timezone, and rebase strategy

### Security

- Fix token permissions and prevent code injection vulnerability in `get-commit-messages` workflow
- Pin all GitHub Actions to commit SHAs for supply chain security
- Add step-security/harden-runner with egress blocking to all workflows
- Network egress restricted to only required endpoints per workflow
- All workflow checkout actions use `persist-credentials: false`

## [1.0.0] - 2026-01-19

### Added

- Initial release of reusable GitHub Actions workflows
- `get-commit-messages` reusable workflow for retrieving recent commit history
  - Configurable `commit_count` input parameter (default: 10)
  - JSON output formatted for Microsoft Adaptive Cards `FactSet` elements
  - Commit messages linked to GitHub commit URLs
  - Author attribution included in each commit entry
  - Time-ago formatting for timestamps (Xs, Xm, Xh, Xd)
  - Safe JSON construction using `jq` to handle special characters
- `workflow_call` trigger for cross-repository reusability

---

<!--
## [X.Y.Z] - YYYY-MM-DD

### Added
- New features

### Changed
- Changes to existing functionality
- **BREAKING**: Description of breaking change

### Deprecated
- Features to be removed in future versions

### Removed
- Removed features

### Fixed
- Bug fixes

### Security
- Security improvements or vulnerability fixes
-->

[Unreleased]: https://github.com/marcus-hooper/workflows/compare/v1.1.0...HEAD
[1.1.0]: https://github.com/marcus-hooper/workflows/compare/v1.0.0...v1.1.0
[1.0.0]: https://github.com/marcus-hooper/workflows/releases/tag/v1.0.0
