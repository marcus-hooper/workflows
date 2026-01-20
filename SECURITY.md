# Security Policy

> **Last reviewed:** January 2026

## Supported Versions

This repository provides reusable workflows. The `main` branch is the only supported version. Users should pin to a specific commit SHA or tag for stability rather than referencing `main` directly.

## Reporting a Vulnerability

If you discover a security vulnerability in this project, please report it responsibly.

### How to Report

1. **Do not** open a public GitHub issue for security vulnerabilities
2. Use [GitHub's private vulnerability reporting](https://github.com/marcus-hooper/workflows/security/advisories/new) to submit a report
3. **Backup contact:** If GitHub's reporting system is unavailable, email security concerns to the repository owner via their GitHub profile
4. Include as much detail as possible:
   - Description of the vulnerability
   - Steps to reproduce
   - Potential impact
   - Suggested fix (if any)

### Disclosure Timeline

- Acknowledgment of your report within 48 hours
- Initial assessment and severity classification within 7 days
- Target resolution timelines by severity:
  - **Critical:** 90 days
  - **High:** 90 days
  - **Medium:** 180 days
  - **Low:** Best effort, addressed in regular maintenance
- Weekly updates on the progress of addressing the vulnerability
- Credit in the security advisory (unless you prefer to remain anonymous)

### Bug Bounty

This project does not currently offer a bug bounty program. However, we deeply appreciate responsible disclosure and will publicly credit security researchers in our advisories (with permission).

### Safe Harbor

We consider security research conducted in accordance with this policy to be:

- Authorized concerning any applicable anti-hacking laws
- Authorized concerning any relevant anti-circumvention laws
- Exempt from restrictions in our Terms of Service that would interfere with conducting security research

We will not pursue civil action or initiate a complaint to law enforcement for accidental, good-faith violations of this policy. We consider security research conducted consistent with this policy to be "authorized" conduct under the Computer Fraud and Abuse Act.

We understand that many systems and services interconnect with third-party systems. While researching this project, ensure you do not access or modify third-party systems without authorization.

### Scope

The following are considered security vulnerabilities:

- Command injection or code execution in bash scripts
- Unsafe handling of git data or user inputs
- JSON injection vulnerabilities in Adaptive Card outputs
- Issues that could compromise calling CI/CD pipelines
- Exposure of sensitive data through workflow outputs

Out of scope:

- Vulnerabilities in upstream dependencies (report to the respective project). However, if you notice we're using a vulnerable version, please let us know and we'll update promptly.
- Vulnerabilities in GitHub Actions platform (report to GitHub)
- Vulnerabilities in Microsoft Teams or the Incoming Webhooks platform (report to Microsoft)
- Issues requiring physical access or social engineering

### Security Notifications

Security fixes are announced via:

- [GitHub Security Advisories](https://github.com/marcus-hooper/workflows/security/advisories)
- Release notes for patched versions

Dependencies are monitored automatically via Dependabot.

## Security Infrastructure

This project employs multiple layers of automated security:

| Measure | Description |
|---------|-------------|
| **CodeQL** | Static analysis for security vulnerabilities |
| **OSSF Scorecard** | Supply chain security assessment published to OpenSSF |
| **Dependency Review** | Scans PRs for vulnerable dependencies |
| **Hardened Runners** | Workflows use `step-security/harden-runner` with egress blocking |
| **Secret Scanning** | Gitleaks detects hardcoded credentials in code |
| **ShellCheck** | Static analysis for bash scripts |
| **Unsafe Pattern Detection** | Flags potentially dangerous bash patterns (eval, curl piping, etc.) |
| **Pinned Actions** | All GitHub Actions pinned to full commit SHAs |
| **Dependabot** | Automated dependency updates |

## Security Considerations

These reusable workflows execute in the context of calling repositories:

1. **Runs in caller's context** - Workflows execute with the caller's permissions and secrets
2. **Processes git data** - Reads commit messages, authors, and timestamps from the calling repository
3. **Generates JSON output** - Produces Adaptive Card-formatted JSON for Teams integration
4. **Least privilege** - Workflows request minimal permissions (`contents: read` by default)

### Network Endpoints

These reusable workflows do **not** make external network requests. All data processing occurs locally within the GitHub Actions runner. The calling workflow is responsible for any external communication (e.g., sending the generated JSON to Teams webhooks).

### Best Practices for Users

1. **Pin to a specific commit SHA** - Use a full commit SHA (e.g., `@a1b2c3d...`) for maximum security, or a tagged release rather than `@main`
2. **Review workflow permissions** - Grant only necessary permissions to your workflow
3. **Validate outputs** - If passing outputs to external systems, validate the JSON structure
4. **Use ephemeral runners** - Consider using ephemeral or isolated runners for sensitive pipelines

### Data Handling

These workflows:

- Do **not** access or transmit secrets from calling repositories
- Do **not** store any data beyond the workflow execution
- Do **not** send data to external services
- Process only public git metadata (commits, authors, timestamps)
- Use `jq` for safe JSON construction to prevent injection
