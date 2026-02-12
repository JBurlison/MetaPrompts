# Custom Agent Files (`.agent.md`)

> **Source:** [VS Code Custom Agents Documentation](https://code.visualstudio.com/docs/copilot/customization/custom-agents)

Custom agents enable you to configure the AI to adopt different personas
tailored to specific development roles and tasks. Each agent defines its own
behavior, available tools, language model preferences, and can orchestrate
multi-step workflows through handoffs.

> Custom agents were previously known as "custom chat modes." If you have
> existing `.chatmode.md` files, rename them to `.agent.md`.

---

## File Location

| Scope | Location |
|-------|----------|
| Workspace | `<provider>/agents/*.agent.md` or `<provider>/agents/*.md` |
| Sub-agents | `<provider>/agents/*.subagent.agent.md` |
| User profile | Current VS Code profile folder (available across workspaces) |
| Organization | GitHub organization level (shared across repos) |

Additional locations can be configured with the `chat.agentFilesLocations`
setting.

> **Provider note:** `<provider>` is your provider's base folder (`.github/`,
> `.claude/`, `.codex/`, `.config/opencode/`). VS Code detects any `.md` file
> in `.github/agents/` as a custom agent.

---

## File Structure

```markdown
---
name: agent-name
description: Brief description shown as placeholder in chat input
user-invokable: true
argument-hint: Optional hint for user input
tools: ['tool1', 'tool2']
agents: ['*']
model: Claude Sonnet 4
disable-model-invocation: false
handoffs:
  - label: Button Text
    agent: target-agent
    prompt: Prompt to send
    send: false
    model: GPT-5 (copilot)
---

[Agent instructions body — Markdown content]
```

---

## Frontmatter Attributes

| Attribute | Required | Type | Default | Description |
|-----------|----------|------|---------|-------------|
| `name` | No | string | filename | Agent name. If not specified, the file name is used. |
| `description` | Recommended | string | — | Brief description shown as placeholder text in the chat input field. |
| `user-invokable` | No | boolean | `true` | Whether the agent appears in the agents dropdown. Set to `false` for sub-agents that should only be accessible programmatically or via handoffs. |
| `argument-hint` | No | string | — | Hint text shown in the chat input field to guide users on what to type. |
| `tools` | No | string[] | — | List of tool or tool set names available to this agent. Can include built-in tools, tool sets, MCP tools, or extension-contributed tools. Use `<server>/*` to include all tools from an MCP server. |
| `agents` | No | string[] | — | List of agent names available as subagents. Use `*` for all agents, `[]` for none. **Requires the `agent` tool to be included in `tools`.** |
| `model` | No | string or string[] | selected model | AI model to use. Specify a single model name or a prioritized array (system tries each in order until one is available). |
| `disable-model-invocation` | No | boolean | `false` | Prevents this agent from being invoked as a subagent by other agents. |
| `infer` | No | boolean | — | **Deprecated.** Use `user-invokable` and `disable-model-invocation` instead. Previously controlled both picker visibility and subagent availability. |
| `target` | No | string | — | Target environment: `vscode` or `github-copilot`. |
| `mcp-servers` | No | object | — | MCP server configuration JSON for use with `target: github-copilot`. |
| `handoffs` | No | object[] | — | List of handoff configurations for workflow transitions between agents. See [Handoff Configuration](#handoff-configuration) below. |

---

## Naming Conventions

| Type | Pattern | Example |
|------|---------|---------|
| User-facing agent | `<name>.agent.md` or `<name>.md` | `planner.agent.md` |
| Sub-agent (workflow component) | `<name>.subagent.agent.md` | `due-diligence.subagent.agent.md` |

- Sub-agents should set `user-invokable: false` so they don't appear in the
  agent picker.
- Any `.md` file in the `<provider>/agents/` folder is detected as a custom
  agent.

---

## Handoff Configuration

Handoffs create guided sequential workflows between agents. After a chat
response completes, handoff buttons appear to let users transition to the next
agent with relevant context.

### Use Cases

- **Planning → Implementation:** Generate a plan, then hand off to start coding.
- **Implementation → Review:** Complete code, then switch to a review agent.
- **Write Failing Tests → Implement:** Generate tests first, then hand off to
  make them pass.

### Syntax

```yaml
handoffs:
  - label: "Display text for button"
    agent: "target-agent-name"
    prompt: "Prompt text to send to target agent"
    send: false
    model: "GPT-5 (copilot)"
```

### Handoff Attributes

| Attribute | Required | Type | Default | Description |
|-----------|----------|------|---------|-------------|
| `label` | Yes | string | — | Display text shown on the handoff button. |
| `agent` | Yes | string | — | Target agent identifier to switch to. |
| `prompt` | No | string | — | Prompt text to send to the target agent. |
| `send` | No | boolean | `false` | Auto-submit the prompt if `true`. If `false`, the prompt is pre-filled only. |
| `model` | No | string | — | Language model for the handoff. Use the format `Model Name (vendor)`, e.g., `GPT-5 (copilot)` or `Claude Sonnet 4.5 (copilot)`. |

---

## Body Content

The body contains Markdown-formatted instructions that define the agent's
behavior. These instructions are prepended to the user's chat prompt whenever
the agent is selected.

### Capabilities

- **Markdown formatting:** Full Markdown including headers, lists, code blocks.
- **File references:** Link to other files with standard Markdown links
  (e.g., `[standards](../instructions/react.instructions.md)`).
- **Tool references:** Reference tools with `#tool:<tool-name>` syntax
  (e.g., `#tool:githubRepo`).
- **Instruction reuse:** Link to `.instructions.md` files to avoid
  duplicating guidelines.

---

## Tool List Priority

When both a custom agent and a prompt file specify tools, the priority order is:

1. Tools specified in the prompt file (if any)
2. Tools from the referenced custom agent in the prompt file (if any)
3. Default tools for the selected agent (if any)

If a specified tool is not available, it is silently ignored.

---

## Example: Planning Agent

```markdown
---
name: planner
description: Generate an implementation plan for new features
tools: ['search', 'fetch', 'githubRepo', 'usages']
model: Claude Sonnet 4
handoffs:
  - label: Implement Plan
    agent: agent
    prompt: Implement the plan outlined above.
    send: false
---

# Planning Instructions

You are in planning mode. Generate implementation plans without making code
edits.

## Plan Structure

- **Overview:** Brief description of the feature
- **Requirements:** List of functional and non-functional requirements
- **Implementation Steps:** Detailed, actionable steps
- **Testing:** Required test cases and coverage
```

---

## Example: Sub-Agent

```markdown
---
name: due-diligence
user-invokable: false
description: Deep analysis of requirements and integration points
tools: ['search', 'fetch', 'usages']
---

# Due Diligence Analysis

You perform deep analysis on requirements before planning begins.
Identify integration points, dependencies, risks, and clarifications needed.
```

---

## Sharing and Organization

- **Workspace agents** are stored in `<provider>/agents/` and shared via
  version control.
- **User profile agents** are stored in the current VS Code profile and
  available across all workspaces.
- **Organization agents** are defined at the GitHub organization level and
  automatically detected when `github.copilot.chat.organizationCustomAgents.enabled`
  is set to `true`.
- Agents can be reused in
  [background agents](https://code.visualstudio.com/docs/copilot/agents/background-agents)
  and [cloud agents](https://code.visualstudio.com/docs/copilot/agents/cloud-agents).

---

## Diagnostics

If an agent isn't working as expected:

1. Select **Configure Custom Agents** from the agents dropdown.
2. Use the chat customization diagnostics view: right-click in the Chat view →
   **Diagnostics**.
3. Check for syntax errors, invalid configurations, or loading issues.
