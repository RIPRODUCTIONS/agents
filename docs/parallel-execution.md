# Parallel Execution Guide

How to run Navox agents simultaneously using Claude Code's async subagent capability.

---

## Why parallel matters

The handoff chain has two parallel stages by design. Running them sequentially wastes time and context. Claude Code's `Agent` tool launches subagents as independent processes — each gets its own context window, so agents don't pollute each other's reasoning.

---

## Pattern: Spawn parallel subagents

In your Claude Code session, ask for both agents in a single message. Claude Code will launch them as concurrent subagents using the `Agent` tool with `run_in_background: true`.

### Stage 1: UX + Security DESIGN-REVIEW (after Architect DESIGN)

```
Run these two agents in parallel:

1. Launch the UX agent: Read the architecture doc, then run FLOW → WIREFRAME → DESIGN → SPEC.
   Deliver component specs to .agency-workspace/ux-spec.md

2. Launch the Security agent: Read the architecture doc, then run DESIGN-REVIEW.
   Deliver auth constraints to .agency-workspace/security-constraints.md

Both agents should read from the Architect's output before starting.
```

### Stage 2: QA + Security CODE-AUDIT (after Full Stack BUILD + Local Review LGTM)

```
Run these two agents in parallel:

1. Launch the QA agent: Run TEST-RUN against the codebase.
   Write findings to .agency-workspace/qa-findings.md

2. Launch the Security agent: Run CODE-AUDIT against the codebase.
   Write findings to .agency-workspace/security-findings.md

Both agents should read the Architect's doc and Full Stack's code.
```

---

## Collecting results

After both subagents complete, Claude Code returns their results to your main session. Merge findings into a single handoff:

```
Read .agency-workspace/qa-findings.md and .agency-workspace/security-findings.md.
Combine all Critical and Important findings into a single list.
Hand off to Full Stack to fix everything before the next stage.
```

The Full Stack agent receives one consolidated list — not two separate conversations.

---

## Context isolation

Each subagent runs in its own context window. This is a feature, not a limitation:

- **QA stays focused on test coverage** — not distracted by security threat models
- **Security stays focused on vulnerabilities** — not distracted by test fixtures
- **UX stays focused on user flows** — not distracted by auth constraint details
- **No cross-contamination** — one agent's verbose output doesn't eat another's context budget

The tradeoff: subagents can't see each other's work in real time. This is why both write to `.agency-workspace/` — the main session merges results after both finish.

---

## When NOT to parallelize

- **Architect → anything**: The Architect must finish before any other agent starts. Every agent depends on the system design.
- **Full Stack BUILD**: Depends on both UX specs and Security constraints. Wait for the parallel stage to complete.
- **Security LAUNCH-AUDIT**: Must run after all fixes are applied. This is the final gate — never parallelize it with fixes.
- **DevOps PIPELINE/DEPLOY**: Can run in parallel with QA + Security CODE-AUDIT if the build is stable.
