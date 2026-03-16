---
name: agency-run
description: Orchestrate a coordinated engineering team from .claude/agents/ to complete a task end-to-end. One command triggers Architect → parallel UX+Security → Fullstack → parallel QA+Security → Launch Audit. Use when a task requires more than one agent or when you're not sure which agent to pick.
---

You are the engineering team orchestrator for this project. Your job is to assemble the right agents from .claude/agents/ and run them in the correct handoff sequence to complete the task.

## Your task

$ARGUMENTS

## Step 1 — Read project memory

Before selecting any agents, read the project memory file if it exists:
```bash
cat .claude/project-memory.md 2>/dev/null || echo "No project memory yet"
```

This tells you what has already been decided, built, and why.

## Step 2 — Read available agents
```bash
ls .claude/agents/
```

For each agent, read its frontmatter description to understand what it does.

## Step 3 — Select your team

Based on the task and project memory, select 2–5 agents from what's available. Document your selection:
SELECTED TEAM:

[agent-id] — [specific role in this run]
[agent-id] — [specific role in this run]
...

EXECUTION ORDER:
Group 1 (sequential): [agent-id]
Group 2 (parallel):   [agent-id], [agent-id]
Group 3 (sequential): [agent-id]
SKIPPED: [any agents and why they're not needed]
## Step 4 — Set up workspace
```bash
mkdir -p .agency-workspace
```

## Step 5 — Run each agent via Task tool

For each group in order:

1. Use the Task tool to invoke the agent
2. Construct the prompt using this structure:
Project context
[paste contents of .claude/project-memory.md if it exists]
Task
[the original task from $ARGUMENTS]
Your role in this run
[what specifically this agent must contribute right now]
Context from agents already run
[paste relevant outputs from previous agents in this run]
Deliver
[specific artifact expected: design doc / code files / test results / audit report]
3. After each agent completes, write their output:
```bash
echo "[agent output]" > .agency-workspace/[agent-id]-[timestamp].md
```

For parallel groups, spawn all Task calls before waiting for results.

## Step 6 — Update project memory

After all agents complete, update .claude/project-memory.md:
```bash
cat >> .claude/project-memory.md << 'EOF'

## Run: [date] — [one-line task summary]

### Decisions made
- [key decision and why]

### Files created or modified
- [filepath]: [what it does]

### Agents run
- [agent-id]: [what they produced]

### Context for next run
- [anything the next engineer needs to know]
EOF
```

## Step 7 — Summary

Print a clean summary:
- Task completed
- Agents run and what each produced
- Files written to .agency-workspace/
- Key decisions recorded in .claude/project-memory.md

## Rules
- Always read project memory before starting — never repeat work already done
- Always update project memory after completing — this is the team's institutional memory
- If an agent run fails, note it in the summary and continue with remaining agents
- Never ask for clarification before starting — make a reasonable assumption and state it
