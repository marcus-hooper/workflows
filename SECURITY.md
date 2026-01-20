# Security Policy

## Supported Versions

| Version | Supported          |
| ------- | ------------------ |
| main    | :white_check_mark: |

> **Note:** This repository provides reusable workflows. Users should pin to a specific commit SHA or tag for stability.

## Reporting a Vulnerability

If you discover a security vulnerability in this project, please report it responsibly.

### How to Report

1. **Do not** open a public GitHub issue for security vulnerabilities
2. Use [GitHub's private vulnerability reporting](https://github.com/marcus-hooper/workflows/security/advisories/new) to submit a report
3. Include as much detail as possible:
   - Description of the vulnerability
   - Steps to reproduce
   - Potential impact
   - Suggested fix (if any)

### Disclosure Timeline

- Acknowledgment of your report within 48 hours
- Initial assessment within 7 days
- Target resolution within 90 days for critical vulnerabilities
- Regular updates on the progress of addressing the vulnerability
- Credit in the security advisory (unless you prefer to remain anonymous)

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

- Vulnerabilities in upstream dependencies (report to the respective project). However, if you notice we're using a vulnerable version, please let us know and we'll update our pinned dependencies promptly.
- Vulnerabilities in GitHub Actions platform (report to GitHub)
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
| **Secret Scanning** | Detects hardcoded credentials in code |
| **Pinned Actions** | GitHub Actions pinned to specific versions |
| **Dependabot** | Automated dependency updates |

## Security Considerations

These reusable workflows execute in the context of calling repositories:

1. **Runs in caller's context** - Workflows execute with the caller's permissions and secrets
2. **Processes git data** - Reads commit messages, authors, and timestamps from the calling repository
3. **Generates JSON output** - Produces Adaptive Card-formatted JSON for Teams integration

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
