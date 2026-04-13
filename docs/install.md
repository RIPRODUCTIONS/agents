# Installation Guide

## Global Install (recommended)

Install the agents globally so they're available in every Claude Code session:

```bash
# Clone the repo
git clone https://github.com/navox-labs/agents.git

# Create directories (safe if they already exist)
mkdir -p ~/.claude/agents ~/.claude/commands ~/.claude/templates

# Copy agents to your global Claude Code config
cp -r agents/.claude/agents/* ~/.claude/agents/

# Copy commands to your global Claude Code config
cp -r agents/.claude/commands/* ~/.claude/commands/

# Copy starter templates
cp -r agents/templates/* ~/.claude/templates/
```

## Project Install

Install the agents into a specific project only:

```bash
# From your project root
git clone https://github.com/navox-labs/agents.git /tmp/navox-agents

# Create directories
mkdir -p .claude/agents .claude/commands

# Copy agents into your project
cp -r /tmp/navox-agents/.claude/agents/* .claude/agents/

# Copy commands into your project
cp -r /tmp/navox-agents/.claude/commands/* .claude/commands/

# Clean up
rm -rf /tmp/navox-agents
```

## Plugin Install

If you hit an SSH error, run this first (one time):
```bash
git config --global url."https://github.com/".insteadOf "git@github.com:"
```

Then install:
```
/plugin marketplace add https://github.com/navox-labs/agents
/plugin install navox-agents
/reload-plugins
```

> **Note:** Plugin commands are namespaced. Use `/navox-agents:agency-run` and `/navox-agents:hire-team` instead of `/agency-run` and `/hire-team`.

## Verification

### Global/Project install

After installing, verify the agents are loaded:

```bash
# Check agent files are in place
ls ~/.claude/agents/
# Expected: architect.md  demo.md  devops.md  fullstack.md  installer.md  local-review.md  qa.md  security.md  ux.md

ls ~/.claude/commands/
# Expected: agency-run.md  hire-team.md
```

Then open Claude Code and run:
- `/hire-team` — should display the full team overview
- `/agency-run` — should prompt you for a task and orchestrate the team
- `/architect DIAGNOSE` — should activate the Architect agent

### Plugin install

Open Claude Code and run:
- `/navox-agents:hire-team` — should display the full team overview
- `/navox-agents:agency-run` — should prompt you for a task and orchestrate the team
- `/navox-agents:architect DIAGNOSE` — should activate the Architect agent

## Uninstall

### Global uninstall
```bash
rm ~/.claude/agents/architect.md
rm ~/.claude/agents/demo.md
rm ~/.claude/agents/devops.md
rm ~/.claude/agents/fullstack.md
rm ~/.claude/agents/installer.md
rm ~/.claude/agents/local-review.md
rm ~/.claude/agents/ux.md
rm ~/.claude/agents/qa.md
rm ~/.claude/agents/security.md
rm ~/.claude/commands/hire-team.md
rm ~/.claude/commands/agency-run.md
```

### Project uninstall
```bash
rm .claude/agents/architect.md
rm .claude/agents/demo.md
rm .claude/agents/devops.md
rm .claude/agents/fullstack.md
rm .claude/agents/installer.md
rm .claude/agents/local-review.md
rm .claude/agents/ux.md
rm .claude/agents/qa.md
rm .claude/agents/security.md
rm .claude/commands/hire-team.md
rm .claude/commands/agency-run.md
```

### Plugin uninstall
```
/plugin uninstall navox-agents
/reload-plugins
```
