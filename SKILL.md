---
name: engineering-quality-gate
description: Enforces a repeatable engineering quality workflow for coding agents: TDD, unit/integration/end-to-end/regression tests, independent security review and authorized penetration testing, load testing, evidence-based fixes, and a clear release-readiness report. Use after building or changing application code, APIs, database schemas, integrations, infrastructure, or workflows.
---

# Engineering Quality Gate

## Purpose

Do not consider a coding task complete merely because code was written. Build the change, verify it, attempt to break it safely, address confirmed issues, and report evidence and remaining risks.

Apply this skill to new features, bug fixes, refactors, API changes, database changes, integrations, configuration changes, and infrastructure changes. Scale the depth to the risk and size of the change, but never silently skip a required check.

## Operating rules

- Inspect the repository instructions, architecture, existing tests, package scripts, CI configuration, and relevant code before changing anything.
- Define acceptance criteria and a small implementation plan.
- Prefer the repository's existing tools and conventions. Do not add dependencies or restructure unrelated code without a reason.
- Use TDD where practical: write a test that fails for the intended reason (RED), implement the smallest fix (GREEN), then refactor and rerun tests.
- For bug fixes, add a regression test reproducing the bug whenever feasible.
- Never fabricate test results, vulnerabilities, coverage, performance metrics, or user counts.
- Distinguish checks that passed, failed, were blocked, and were not run.
- Never weaken security controls merely to make a test pass.
- Do not run destructive tests, load tests, migrations, or security probes against production without explicit authorization and an agreed safety plan.
- Use only systems the user owns or is explicitly authorized to test. Use synthetic accounts and data.
- If a check cannot be run, explain the limitation, impact, and exact next step.

## Workflow

### 1. Reconnaissance and scope

1. Read project-level instructions (for example, `AGENTS.md`, `CONTRIBUTING.md`, README, and relevant package-specific instructions).
2. Inspect the affected code, dependencies, data model, API contracts, authentication/authorization, deployment configuration, and existing tests.
3. Identify expected behavior, acceptance criteria, backwards-compatibility requirements, likely side effects, and risk.
4. Break work into testable units.
5. Choose appropriate test and security tools already available in the repository.
6. Record a baseline where useful, including existing test failures and performance.

Do not claim a pre-existing failure was introduced by the change without evidence.

### 2. Test-driven implementation

For each meaningful behavior:

1. **RED:** Add or update a focused test and run it. Confirm it fails for the expected reason, not because of a broken test setup.
2. **GREEN:** Implement the smallest reasonable change and run the focused test.
3. **REFACTOR:** Improve clarity and maintainability without changing behavior; rerun tests.
4. **VERIFY:** Run related tests and inspect the diff.

When TDD is impractical (for example, a UI-only exploratory change or an environment with no runnable test harness), state why and use the strongest feasible alternative. Do not add brittle or meaningless tests solely to satisfy the process.

### 3. Functional verification

Select checks based on the changed surface area.

**Unit tests**
- Normal behavior and business rules.
- Invalid, missing, empty, and boundary inputs.
- Exceptions and failure handling.
- Permissions and validation.
- Regression cases and edge cases.

**Integration tests**
- Frontend/backend and API contracts.
- Database reads/writes, constraints, transactions, and isolation.
- Authentication and authorization middleware.
- External services, webhooks, queues, scheduled jobs, storage, email, push, and real-time messaging as applicable.
- Retries, timeouts, duplicate events, and partial failures.

Use mocks for isolated behavior where appropriate, but do not treat mocked tests as proof that a real integration works. Use test services, sandbox credentials, or controlled test doubles for external systems.

**End-to-end and regression tests**
- Exercise complete user journeys for affected workflows.
- Check happy paths, invalid actions, authorization failures, refresh/retry behavior, interrupted operations, and persisted state.
- Run existing relevant regression tests and the full suite when practical.
- Run linting, type checks, build checks, and static analysis where the project supports them.

Report exact commands and actual outcomes. If tests fail, diagnose whether the cause is the change, an existing issue, or the environment.

### 4. Independent security review

After implementation and functional testing, perform a separate security pass. If subagents are available, assign a reviewer who did not implement the change. Otherwise, switch to an explicitly independent review pass and inspect the actual diff and surrounding code.

Review relevant risks, including:

- Authentication, authorization, access control, IDOR/BOLA, privilege escalation, tenant isolation.
- Injection, XSS, CSRF, SSRF, unsafe deserialization, and path traversal where applicable.
- Input validation, output encoding, file uploads, file processing, and storage permissions.
- Secrets, tokens, sensitive logs, error messages, session and cookie settings.
- Rate limiting, brute-force protection, abuse cases, and resource exhaustion.
- CORS, security headers, dependency vulnerabilities, unsafe defaults, and configuration.
- Database row-level security and server-side enforcement of permissions.
- Webhook signatures, replay protection, duplicate processing, race conditions, and business-logic abuse.
- Account recovery, password reset, email changes, and sensitive administrative actions.

Use applicable OWASP guidance and existing repository security tools. Do not claim exhaustive security or compliance from a checklist or clean scanner output.

For each confirmed finding, record:
- Severity: Critical, High, Medium, Low, or Informational.
- Affected file, route, component, configuration, or workflow.
- Evidence and a realistic impact/attack scenario.
- Reproduction steps that are safe and authorized.
- Recommended remediation and retest status.

Separate confirmed findings from hypotheses and false positives.

### 5. Authorized penetration testing

Test changed features and relevant trust boundaries using safe, non-destructive techniques in local or staging environments.

Where applicable, attempt to:
- Call protected APIs without credentials or with insufficient privileges.
- Change object identifiers to check for cross-user or cross-tenant access.
- Bypass client-side validation by making direct API requests.
- Manipulate request bodies, query parameters, headers, and upload metadata.
- Reuse expired/revoked credentials and replay requests or webhooks.
- Submit duplicate, concurrent, or out-of-order operations.
- Bypass workflow approvals or invoke unauthorized administrative actions.
- Trigger rate limits and observe graceful failure and recovery.
- Check for sensitive data exposure in responses, errors, and logs.

Do not exfiltrate data, target third-party systems without authorization, disrupt services, or use uncontrolled exploit payloads. Avoid destructive actions unless explicitly authorized and safely isolated. Do not use real customer data.

### 6. Remediate and verify

For each confirmed defect within scope:

1. Establish the root cause.
2. Prioritize by severity, exploitability, impact, and likelihood.
3. Fix it if authorized and feasible.
4. Add or update a regression test.
5. Rerun focused tests, relevant integration/end-to-end tests, and applicable static/security checks.
6. Have the independent reviewer verify the fix where possible.
7. Document any accepted risk or unresolved issue.

Do not silently leave Critical or High findings unresolved. State clearly if a release should be blocked and request a decision where necessary.

### 7. Performance and load testing

Load-test only when the change or application risk warrants it, and only in a controlled environment that can safely handle the load. Do not launch a high-volume test against production without explicit approval, an agreed plan, monitoring, and stop conditions.

**Set the target first.** Determine the expected traffic, workload mix, duration, infrastructure limits, and acceptable service-level objectives with the user or from documented project requirements. If unknown, state assumptions and use a conservative exploratory test.

Use an existing tool or a suitable one such as k6 or Locust. Increase load gradually, for example 10, 50, 100, 250, 500, 1,000, and higher concurrent virtual users only if safe and relevant. These are optional stages, not a promise that the system must reach them.

Use realistic user journeys, think times, reads and writes, authentication, background jobs, and relevant integrations. Use synthetic data and prevent real emails, payments, notifications, or irreversible actions.

Measure and report, where available:
- Concurrent virtual users and test duration.
- Request rate / throughput.
- p50, p95, and p99 latency.
- HTTP error rate, timeouts, and failed checks.
- CPU, memory, database latency/connections, cache behavior, and queue depth.
- The load level where degradation began and the observed bottleneck.
- Recovery behavior after load decreases.

Do not equate virtual users with requests per second or real human users. Never claim thousands of users were simulated unless the test actually ran and produced evidence.

Stop the test if agreed thresholds are exceeded, the environment becomes unstable, or unintended external effects occur. If a bottleneck is found, document evidence, fix it when authorized, and repeat the same workload for a fair comparison.

### 8. Code quality and release safety

Check applicable items:
- Readability, maintainability, duplication, types, lint, formatting, and static analysis.
- Dependency and runtime compatibility.
- Logging, observability, error handling, cleanup, retries, and recovery.
- Database migration safety, rollback plan, and data integrity.
- Backwards compatibility and API contract changes.
- Configuration and environment variables.
- Documentation for changed behavior and APIs.

Inspect the final diff for accidental changes, debug code, secrets, generated files, and unrelated modifications.

## Required final report

Use this report format, adapting sections to the task. Mark irrelevant checks as “Not applicable” with a short reason; mark unavailable checks as “Not run” or “Blocked,” not “Passed.”

### 1. Implementation
- Summary of what changed.
- Relevant files, routes, components, migrations, and integrations.
- Acceptance criteria met or unmet.

### 2. Test evidence
- TDD: RED/GREEN/REFACTOR evidence, or reason not practical.
- Unit, integration, end-to-end, and regression results.
- Lint, type-check, build, and static-analysis results.
- Commands executed and concise output/results.
- Failures and whether they are pre-existing, change-related, or environment-related.

### 3. Security review
- Findings grouped by severity.
- Evidence and affected areas.
- Fixes and independent retest results.
- Remaining risks and scope limitations.

### 4. Penetration testing
- Scenarios actually tested.
- Confirmed findings and evidence.
- Unverified attack surfaces and limitations.

### 5. Performance/load testing
- Environment, workload, concurrency, duration, and tool.
- Throughput, p50/p95/p99, error rate, and resource observations where collected.
- Failure/degradation threshold and bottlenecks.
- Before/after comparison if optimization was performed.
- Clearly identify tests not run.

### 6. Improvement backlog
Prioritize improvements:
- **P0:** Critical security, data-loss, or integrity risks.
- **P1:** High-risk vulnerabilities, severe defects, or release blockers.
- **P2:** Important reliability, performance, or maintainability improvements.
- **P3:** Lower-priority enhancements.

For each item, give the problem, impact, proposed action, and effort estimate only when there is enough evidence to estimate responsibly.

### 7. Readiness
Choose one:
- **PASS:** Required checks for the defined scope passed with evidence.
- **PASS WITH LIMITATIONS:** Core checks passed, with clearly documented limitations.
- **FAIL:** Important tests failed or unresolved risks block release.
- **NOT VERIFIED:** Evidence or environment access is insufficient.

Never mark the overall task PASS if a required test failed or a release-blocking finding remains unresolved.

## Definition of done

The task is done only when:
- The change meets its acceptance criteria.
- Relevant tests have been run and results recorded.
- Regression risk has been checked.
- Security review and authorized attack testing are completed to the agreed scope, or clearly marked as not run/blocked.
- Load testing is performed when warranted, or the reason for not running it is stated.
- Confirmed in-scope issues are fixed and retested, or explicitly documented as unresolved.
- The final report accurately distinguishes evidence from assumptions.

**Core principle: Build it. Test it. Break it safely. Fix it. Test it again. Report the evidence honestly.**
