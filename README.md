# Engineering Quality Gate

A reusable agent skill for disciplined software delivery: TDD, unit/integration/end-to-end tests, independent security review, authorized penetration testing, load testing, remediation, and evidence-based release reporting.

## Install

Copy the `engineering-quality-gate` folder into your agent's skills directory. For agents that support the Agent Skills convention, the skill entry point is `SKILL.md`.

Typical repository layout:

```text
your-repo/
└── .agents/
    └── skills/
        └── engineering-quality-gate/
            └── SKILL.md
```

Some agents use a different skills directory (for example `.claude/skills/` or another configured path). Follow your agent's documentation and place the folder there.

## Use

Ask your coding agent to:

> Follow the engineering-quality-gate skill for this task. Inspect the repo instructions first, use TDD where practical, run the relevant tests, perform an independent security review and authorized penetration testing, load-test safely when warranted, fix and retest confirmed findings, and provide the required evidence-based final report. Do not claim checks passed unless they actually ran.

## Safety

Load testing and penetration testing must be limited to systems you own or are explicitly authorized to assess. Use staging, synthetic data, safe ramp-up, and clear stop conditions. The skill intentionally does not claim that every project must support a fixed number of users; set targets from requirements and available infrastructure.
