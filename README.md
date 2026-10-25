# Code Doctor 🩺

An AI coding skill for better code quality, automated testing, security auditing, penetration testing, and performance optimization.

## Features

- 🧪 Test Driven Development (TDD)
- ✅ Unit, integration, end to end, and regression testing
- 🛡️ Independent security reviews
- 🔍 Authorized penetration testing
- ⚡ Load testing with simulated concurrent users
- 🔧 Bug fixing and regression verification
- 📊 Performance analysis and bottleneck detection
- 📝 Evidence based reports and prioritized improvements

## Installation

Run this single command from your project's root directory to install Code Doctor without cloning the repository.

```bash
mkdir -p .agents/skills/code-doctor && curl -fsSL https://raw.githubusercontent.com/FasihAhmedRaza/code-doctor/main/code-doctor/SKILL.md -o .agents/skills/code-doctor/SKILL.md
```

This downloads only the `SKILL.md` file into your project's `.agents/skills/code-doctor/` directory.

**Requirements:** `curl` and `mkdir`, available by default on most macOS and Linux systems. Your coding agent must support this skill directory or be configured to load it.

## How to Use

Ask your coding agent:

> Follow the Code Doctor skill for this task. Use TDD wherever practical, run unit, integration, end to end, and regression tests, perform an independent security review and authorized penetration testing, and load test safely when appropriate. Fix confirmed issues, rerun tests, and report actual results, vulnerabilities, performance metrics, and prioritized improvements. Never claim a test passed unless it was actually executed.

## Quality Workflow

1. Understand the codebase and requirements.
2. Write tests before implementation wherever practical.
3. Implement and verify the change.
4. Run relevant automated tests.
5. Review security and attempt safe, authorized attacks.
6. Simulate increasing concurrent users when appropriate.
7. Fix confirmed issues and repeat verification.
8. Report evidence, remaining risks, and recommended improvements.

## Safety

- Test only systems you own or are authorized to assess.
- Use staging environments and synthetic data for security and load testing.
- Never run destructive or high-volume tests against production without explicit approval.
- Never invent test results or performance metrics.
- Clearly report failed, blocked, and unexecuted checks.

## Final Report

Every task should include implementation details, test results, security findings, load test results when applicable, and prioritized improvements.

Final status: **PASS**, **PASS WITH LIMITATIONS**, **FAIL**, or **NOT VERIFIED**.

## Repository

GitHub: https://github.com/FasihAhmedRaza/code-doctor

**Build it. Test it. Break it safely. Fix it. Verify it.**
