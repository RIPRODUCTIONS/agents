# Human-in-the-Loop Guide

Navox agents pause at defined points so humans make the high-stakes decisions. Not every step carries the same risk — this guide defines exactly when and why agents stop.

---

## Checkpoint Types

### GATE — approve before proceeding

The agent has produced a deliverable that downstream agents depend on. Nothing continues until you approve.

| Gate | Agent | What you review |
|---|---|---|
| Architect DESIGN complete | Architect | System design, auth model, security model — this becomes the source of truth for every agent |
| Security DESIGN-REVIEW complete | Security | Auth constraints and threat model — Full Stack must follow these exactly |
| Local Review CHECKPOINT | Local Review | Running app in browser — visual and functional check before QA |
| Launch Audit verdict | Security | APPROVED / APPROVED WITH CONDITIONS / BLOCKED — final ship decision |

**How it works:** The agent outputs its deliverable, then prints:

```
⚠️ HITL REQUIRED — GATE
This is a gate checkpoint. Downstream agents depend on this output.
Please review and respond: APPROVED | REVISION NEEDED: [notes]
```

### CHECKPOINT — review output, then continue

The agent has finished a stage. You scan the output for obvious issues before the next stage runs. Lower stakes than a gate — the work continues either way, but you can catch problems early.

| Checkpoint | Agent | What you scan |
|---|---|---|
| Full Stack BUILD complete | Full Stack | Code structure, obvious gaps, auth implementation before QA + Security run |
| QA TEST-RUN findings | QA | Triage Critical vs Important — decide what must be fixed vs acceptable risk |
| DevOps PIPELINE complete | DevOps | CI/CD config, secret injection, deploy strategy before first deploy |

**How it works:** The agent outputs its deliverable, then prints:

```
⚠️ HITL REQUIRED — CHECKPOINT
Review complete. Scan the output above for issues.
Respond: CONTINUE | FEEDBACK: [notes]
```

### ESCALATION — agent self-pauses

The agent has hit a decision it cannot or should not make alone. Ambiguous requirements, conflicting constraints, or anything outside the agent's defined scope.

**Triggers:**
- Architect's design is missing or contradicts the request
- Auth model is undefined and the agent needs it to proceed
- Two valid approaches exist and the tradeoff is business-level, not technical
- The agent detects a potential security issue outside its expertise
- Any destructive action (deleting data, dropping tables, force-pushing)

**How it works:** The agent stops immediately and prints:

```
⚠️ HITL REQUIRED — ESCALATION
I've stopped because: [specific reason]
Decision needed: [exact question with options]
Context: [what I know and what I don't]
```

The agent does not guess, does not pick a default, and does not continue.

---

## Dangerous Command Interception

Commands matching these patterns should always surface for human approval before execution:

| Pattern | Risk |
|---|---|
| `rm`, `rm -rf` | File or directory deletion |
| `drop`, `truncate`, `delete` | Database data loss |
| `--force`, `--hard` | Irreversible git or system operations |
| `production`, `prod` | Production environment access |
| `deploy` | Deployment to live infrastructure |

Configure these in your Claude Code settings or hooks to intercept before execution.

---

## The Rule

Every agent in this team follows one rule:

> **Never proceed past a GATE checkpoint without explicit human approval.**

If in doubt, escalate. The cost of pausing is zero. The cost of a wrong decision at a gate is rework, security gaps, or shipping broken software.
