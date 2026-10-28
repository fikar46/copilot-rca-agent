# RCA Analyst: GitHub Copilot Agent

`rca-analyst` is a custom GitHub Copilot agent that performs **root cause analysis** on bugs, failing tests, stack traces, and incidents. It finds *why* something broke, backed by evidence, instead of just patching the symptom.

## What it does
- Investigates an error, test failure, regression, or incident using the code, git history, and logs.
- Separates symptom, trigger, and root cause, and tries to disprove alternative causes.
- Never edits files (it reads, searches, and runs inspection commands only).
- Returns a structured report: summary, evidence (`file:line`), 5 Whys, ruled-out causes, fix / mitigation / prevention, and confidence.

## Components
| File | Role |
|---|---|
| `.github/agents/rca-analyst.agent.md` | The agent: role, rules, tools |
| `.github/skills/rca-method/SKILL.md` | The method and report template the agent follows |

## Usage
CLI (run inside the project you want to investigate):
```
copilot --agent rca-analyst --prompt "Investigate why @tests/login.test.ts fails intermittently since last week"
```
Or in the CLI use `/agent` and pick `rca-analyst`. In VS Code, pick it in the agent selector in Chat.

Prompt template (Context + Goal + Constraints + Acceptance):
```
@logs/app.log @src/payment/service.ts
CONTEXT: HTTP 500 on /pay since release 1.4.2.
GOAL: find the root cause.
CONSTRAINTS: read-only, cite file:line.
ACCEPTANCE: RCA report with ranked causes and a recommended fix.
```

## Example report
```
## Summary
/pay returns 500 because the new currency lookup returns undefined for IDR.
## Evidence
- src/payment/service.ts:88 | rates[currency].value, no null check
- git log: commit abc123 removed the IDR default (release 1.4.2)
## Root cause
...
## Recommendation
- Fix: restore default / add null check
- Mitigation: hotfix config
- Prevention: unit test for unsupported currencies
## Confidence
High
```

## Install
Copy `.github/agents/rca-analyst.agent.md` and `.github/skills/rca-method/` into your repo's `.github/` folder, or into `~/.copilot/agents` and `~/.copilot/skills` for personal use.

## More
How agents, skills, and instructions work in Copilot: [docs/SPIKE.md](docs/SPIKE.md).
