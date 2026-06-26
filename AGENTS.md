# Agents

Agents are specialized, autonomous workers that orchestrate complex, multi-step workflows in the MFSE Software Factory. Unlike skills which focus on domain-specific knowledge, agents coordinate other agents, invoke skills, and guide users through decision-making processes.

## Organization

Agents are organized flat in the `agents/` directory with no bucket folders (unlike skills). Each agent is a single `.agent.md` file.

```
agents/
├── mfse-plan-on-roids.agent.md
├── mfse-tdd.agent.md
├── mfse-tdd-red.agent.md
├── mfse-tdd-green.agent.md
└── mfse-tdd-refactor.agent.md
```

## Naming Convention

All agents follow the `mfse-` prefix naming convention, indicating they are part of the MFSE Software Factory.

- `mfse-plan-on-roids` — orchestrates design interviews and planning
- `mfse-tdd` — coordinates the full TDD cycle
- `mfse-tdd-red` — TDD red phase (write failing tests)
- `mfse-tdd-green` — TDD green phase (write minimal implementation)
- `mfse-tdd-refactor` — TDD refactor phase (improve code)

## Agent Structure

Every `.agent.md` file contains YAML frontmatter and markdown instructions:

```yaml
---
name: agent-name
description: One-line description of what the agent does
argument-hint: Optional hint for what the user should provide
agents: [list of sub-agents]
tools: [list of tools the agent uses]
handoffs:
  - label: Label for handoff
    agent: target-agent-name
    prompt: Instructions to pass
    send: false
---

Agent instructions and workflow...
```

### Key Fields

- **name** — Unique identifier, used when invoking the agent via `runSubagent`
- **description** — One-line summary of purpose and expertise
- **argument-hint** — Guidance for what input the agent expects
- **agents** — List of sub-agents this agent can invoke or coordinate
- **tools** — List of available tools (VS Code APIs, file operations, etc.)
- **handoffs** — Transition points to other agents with prompts and contexts

## User-Invoked vs Model-Invoked

All agents in this repository are **model-invoked**:

- **Model-invoked** — Claude can autonomously invoke these agents based on the user's request and context
- They are discoverable via the agents list in system instructions
- The model determines when to delegate work to an agent versus handling it directly

There are no user-invoked-only agents in this repository (analogous to `disable-model-invocation: true` in skills).

## Relationship to Skills

Agents coordinate and invoke skills:

- **Agents** — Orchestrate workflows, manage TDD cycles, interview users, and delegate to sub-agents
- **Skills** — Domain-specific knowledge packs (code patterns, testing practices, architecture practices)
- Skills are imported into agent tools and can be discovered by agents during execution

Agents may invoke skills from either the `mfse-softwarefactory/skills/` folder or the shared `../skills/` repository.

## Registration

Agents must be registered in system instructions so the model is aware of them and can invoke them appropriately. Each agent entry includes:

```
<agent>
  <name>agent-name</name>
  <description>Description of purpose and expertise</description>
  <argumentHint>What input the agent expects</argumentHint>
</agent>
```

## Guidelines

### Creating a New Agent

1. Create a new `.agent.md` file in the `agents/` directory with the `mfse-` prefix
2. Define YAML frontmatter with: `name`, `description`, `argument-hint`, `agents` (if any), and `tools`
3. Write clear workflow instructions in markdown below the frontmatter
4. If the agent should invoke sub-agents, define `handoffs` with labels, target agents, and transition prompts
5. Register the agent in system instructions once it's ready for discovery

### Invoking an Agent

Use the `runSubagent` tool with the agent name:

```
runSubagent({
  agentName: "mfse-plan-on-roids",
  prompt: "Detailed task description and context",
  description: "Short description of work"
})
```

### Agent Coordination

Agents can coordinate with other agents through:

- **Handoff UI** — Present users with labeled next-step options that transition to other agents
- **Direct invocation** — Call sub-agents via `runSubagent` with new prompts
- **Memory sharing** — Use session and repository memory to pass context between agents

## Examples

### Planning Workflow

`mfse-plan-on-roids` interviews the user about a feature, then offers a `mfse-tdd` handoff:

```yaml
handoffs:
  - label: Implement
    agent: mfse-tdd
    prompt: Start implementation
    send: false
```

### TDD Orchestration

`mfse-tdd` coordinates a full cycle by invoking `mfse-tdd-red`, `mfse-tdd-green`, and `mfse-tdd-refactor` in sequence:

```yaml
agents: ["mfse-tdd-red", "mfse-tdd-green", "mfse-tdd-refactor"]
```

## See Also

- [Skills](./skills/) — Domain-specific knowledge packs
- [Prompts](./prompts/) — Reusable prompt templates
- [CLAUDE.md](../skills/CLAUDE.md) — Skills repository organization
