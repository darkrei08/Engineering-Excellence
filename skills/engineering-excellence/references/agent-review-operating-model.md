# Agent review operating model

Use this reference when agent-written changes are reviewed at higher volume. It adapts the evidence-backed practices described in Anthropic's *How the Claude Code team does code review* without assuming Anthropic products or infrastructure.

## Objective

Minimize human review time without increasing regressions, reverts, incidents, or harmful fixes. Human attention is a scarce resource, not a ceremony to eliminate. The change's risk determines the evidence and approval bar.

## Layered change path

1. **Pre-PR verification**: run the smallest applicable tests, build, lint, browser/device check, or command and retain exact output. A fresh-context reviewer checks the diff against the requirement.
2. **Required repository checks**: run the repository's lint, type, test, build, packaging, platform, and security checks. A red required check blocks completion.
3. **Independent review**: inspect correctness, regression, security, and operational failure modes separately when their risks differ. Findings require file/line evidence and a severity.
4. **Risk-scaled human decision**: low-risk changes may use automated evidence alone when policy allows; medium-risk changes need one human; high-risk or protected paths need the required human owners. An agent never substitutes for a required human approval.
5. **Integration and recovery**: re-run checks against the integration target where available. Use feature flags, staged release, monitoring, and a fast revert or rollback path for changes that can reach users.

If a layer is unavailable, report `NOT RUN`, `BLOCKED`, or `UNVERIFIED`; do not infer a pass from another layer.

## Review convergence

- Verify findings independently before presenting them as blockers.
- Rank findings by impact; discard style-only noise unless the repository policy makes it blocking.
- Answer every actionable finding with a fix or a written disposition.
- Cap repeated fix/review rounds. After three rounds, step back and reconsider the approach instead of looping unchanged.
- After initial passes, review only the new diff and do not repeat already-posted findings.
- Treat approval as bound to the reviewed commit; new unverified commits require re-review.

## Risk and ownership

Classify from the changed paths and behavior, not the author's identity. Always elevate authentication, permissions, secrets, billing, data migration, CI/release, installers, public contracts, concurrency, and irreversible operations. Record `Risk`, `Testing`, and `How to revert` in the change handoff.

The person directing an agent owns the outcome. Small changes, reversible operations, flags, staged rollout, and fast detection reduce blast radius; they do not remove accountability.

## Reviewer improvement loop

Turn confirmed incidents, reverts, and harmful false positives into expert-checked evaluation cases. When changing a reviewer prompt, tool set, model, or policy:

1. add or update the case with the expected finding and causality;
2. run the reviewer against the corpus;
3. compare recall, false-positive noise, and review rounds;
4. keep the change only when the evidence improves or the trade-off is explicit.

Measure human approvals, comments, wait time, review rounds, revert rate, and incident rate when those metrics exist. Never fabricate metrics; a missing dashboard is an explicit unknown.

## Context and learning

Keep repository review contracts short. Put failure-oriented rules in `REVIEW.md` or the local equivalent, ask for evidence rather than guesses, and link deeper documentation. When human understanding matters, generate a focused explainer or teach-back instead of requiring a person to read routine diffs line by line.
