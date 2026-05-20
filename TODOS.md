# TODOS

## Reusable Workflows

### Runtime guard against pull_request vs pull_request_target misuse

**What:** Add a check at the start of the reusable `dependabot-auto-merge.yml` that warns or fails if the caller used `pull_request` instead of `pull_request_target`.

**Why:** Callers might accidentally use the wrong trigger, running the workflow with the wrong security model (untrusted PR code in checkout instead of base branch code). Documentation helps but doesn't prevent misconfiguration.

**Context:** GitHub doesn't expose the caller's trigger type to reusable workflows directly (`github.event_name` is always `workflow_call`). However, the `github.event` object varies by trigger — checking for `github.event.pull_request.base` existence or similar fields could serve as a proxy. Needs investigation into what event data is available inside `workflow_call` when the caller uses `pull_request` vs `pull_request_target`.

**Effort:** S
**Priority:** P2
**Depends on:** Phase 1 dependabot-auto-merge reusable extraction

### Teams Notification reusable workflow

**What:** Create a higher-level reusable workflow that orchestrates: (1) get-commit-messages, (2) format as Adaptive Card, (3) send via Teams webhook.

**Why:** This is the most common end-to-end pattern across consuming repos (used in WaterLog, TimeHub, and others). Currently each repo manually wires up all 3 steps (~40 lines of boilerplate per repo).

**Context:** Depends on `send-teams-notification` action (separate repo). The reusable workflow would accept inputs like `webhook-url`, `commit-count`, `card-title`, and optional `custom-facts`. Already listed as deferred in the v2.0.0 design doc (P2, M effort → S with CC). Makes sense after Phase 1 ships and get-commit-messages is stable with its security fixes.

**Effort:** S
**Priority:** P2
**Depends on:** Phase 1 completion (stable get-commit-messages.yml)

## Documentation

### Consuming repo migration guide

**What:** Create a step-by-step guide showing how to migrate each consuming repo from duplicated workflows to calling the new reusable versions.

**Why:** 8 repos need to adopt v2. Without a guide, each migration is ad-hoc and error-prone. Key gotchas include: correct trigger type for dependabot (`pull_request_target` required), `secrets: inherit` vs explicit secret passing, branch protection rule updates after workflow name changes.

**Context:** The design doc has example thin wrappers but no migration checklist. Guide should cover: (1) which reusable workflow replaces which local workflow, (2) thin wrapper template for each, (3) secrets/permissions requirements, (4) branch protection updates needed, (5) how to verify the migration worked. Write after Phase 1 ships so the guide references stable, tested workflows.

**Effort:** S
**Priority:** P1
**Depends on:** Phase 1 completion

## Completed
