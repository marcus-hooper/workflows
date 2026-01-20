## Summary

<!-- Brief description of what this PR does -->

## Related Issues

<!-- Link to related issues using GitHub keywords: Closes #123, Fixes #456, Resolves #789 -->

## Changes

<!-- List the key changes made as bullet points -->

## Type of Change

<!-- Check all that apply -->

- [ ] New feature (non-breaking change that adds functionality)
- [ ] Bug fix (non-breaking change that fixes an issue)
- [ ] Refactoring (no functional changes)
- [ ] Documentation update
- [ ] CI/CD changes
- [ ] Security fix
- [ ] Dependency update
- [ ] Breaking change (fix or feature that would cause existing functionality to change)

### Breaking Change Details

<!-- If breaking change, describe migration steps for callers -->

## Testing

<!-- Describe how this was tested -->

- [ ] Tested by calling workflow from another repository
- [ ] Verified output format is valid for Adaptive Cards (if applicable)
- [ ] Manual testing performed
- [ ] Link to test run: <!-- paste GitHub Actions run URL -->

## Security Considerations

<!-- If this PR doesn't touch secrets, inputs, or shell commands, check N/A and skip the rest. -->

- [ ] **N/A** - No security-relevant changes

<!-- If security-relevant, leave N/A unchecked and verify all applicable items: -->

- [ ] Secrets are not exposed in workflow logs or outputs
- [ ] User inputs are sanitized before use in shell commands
- [ ] No sensitive data included in workflow outputs
- [ ] Supply chain security reviewed (actions pinned to SHAs)

## Checklist

- [ ] My code follows the project's conventions
- [ ] JSON construction uses `jq` for escaping (not string concatenation)
- [ ] Special characters and UTF-8 are handled properly
- [ ] Workflow inputs have appropriate defaults
- [ ] Multi-line outputs use heredoc syntax with EOF delimiters
- [ ] I have updated documentation if needed
- [ ] I have updated README.md if workflow inputs/outputs changed
- [ ] I have updated CHANGELOG.md under `[Unreleased]`
- [ ] Commit messages use conventional prefixes (`feat:`, `fix:`, `docs:`, `security:`, etc.)

### Dependency Updates

<!-- Check if this PR modifies third-party actions -->

- [ ] N/A - No dependency changes
- [ ] Third-party actions pinned to full commit SHAs
- [ ] Verified actions are from trusted sources (official or verified publishers)

## Screenshots / Output

<!-- If applicable, add sample output or Adaptive Card preview -->
