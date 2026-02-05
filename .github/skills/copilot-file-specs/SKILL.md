---
name: copilot-file-specs
description: Contains the complete specifications for AI coding assistant customization files including agents, skills, prompts, and instructions. Works with GitHub Copilot, Claude Code, Codex, OpenCode, and other providers. Use this skill when you need to reference the correct file format, required fields, supported attributes, or file locations for any customization file.
---

# AI Coding Assistant Customization File Specifications

This skill contains the authoritative specifications for all AI coding assistant customization file types. These specifications work across multiple providers.

## Provider Folder Mapping

Different AI coding assistant providers use similar folder structures in their respective configuration directories:

| Provider | Base Folder | Notes |
|----------|-------------|-------|
| GitHub Copilot | `.github/` | Most common, widely documented |
| Claude Code | `.claude/` | Anthropic's Claude in VS Code |
| Codex | `.codex/` | OpenAI Codex-based tools |
| OpenCode | `.config/opencode/` | Open-source alternatives |

**Throughout this document, `<provider>/` represents your chosen provider's base folder.** Replace with the appropriate directory for your environment.

## File Types Overview

| Type | Extension | Location | Purpose |
|------|-----------|----------|---------|
| Agent | `.agent.md` or `.md` | `<provider>/agents/` | Custom AI personas with specialized behaviors |
| Sub-Agent | `.subagent.agent.md` | `<provider>/agents/` | Workflow component agents (not user-invokable) |
| Skill | `SKILL.md` | `<provider>/skills/<name>/` | Reusable capabilities (directory-based) |
| Prompt | `.prompt.md` | `<provider>/prompts/` | Reusable prompt templates |
| Instruction | `.instructions.md` | `<provider>/instructions/` | Contextual guidance for file types |

---

## Agent Files (`.agent.md`)

### Location
- Workspace: `<provider>/agents/*.agent.md` or `<provider>/agents/*.md`
- Sub-agents: `<provider>/agents/*.subagent.agent.md`
- User profile: Available across workspaces

### File Structure
```markdown
---
name: agent-name
description: Brief description shown as placeholder in chat input
user-invokable: true
argument-hint: Optional hint for user input
tools: ['tool1', 'tool2']
agents: ['*']  # or specific agent names, or [] for none
model: Claude Sonnet 4  # or array for fallback: ['Claude Sonnet 4', 'GPT-4o']
disable-model-invocation: false
handoffs:
  - label: Button Text
    agent: target-agent
    prompt: Prompt to send
    send: false
    model: GPT-5 (copilot)
---

[Agent instructions body - Markdown content]
```

### Frontmatter Attributes

| Attribute | Required | Description |
|-----------|----------|-------------|
| `name` | No | Agent name. If not specified, filename is used |
| `description` | Yes (recommended) | Brief description shown as placeholder text in chat input field |
| `user-invokable` | No | Set to `false` for sub-agents that shouldn't appear in agent picker (default: `true`) |
| `argument-hint` | No | Hint text shown in chat input to guide users |
| `tools` | No | List of tool/tool set names available to this agent. Use `<server>/*` for all MCP server tools |
| `agents` | No | List of agent names available as subagents. Use `*` for all, `[]` for none. Requires `agent` tool in tools list |
| `model` | No | AI model to use. Can be a string or array (prioritized fallback list). If not specified, uses currently selected model |
| `disable-model-invocation` | No | Set to `true` to prevent this agent from being invoked as a subagent by other agents (default: `false`) |
| `infer` | No | **Deprecated.** Use `user-invokable` and `disable-model-invocation` instead |
| `target` | No | Target environment: `vscode` or `github-copilot` |
| `mcp-servers` | No | MCP server configs for GitHub Copilot target |
| `handoffs` | No | List of handoff configurations for workflow transitions |

### Naming Conventions

- **User-facing agents:** `<name>.agent.md` or `<name>.md`
- **Sub-agents (workflow components):** `<name>.subagent.agent.md` with `user-invokable: false`

### Handoff Configuration

```yaml
handoffs:
  - label: "Display text for button"
    agent: "target-agent-name"
    prompt: "Prompt text to send to target agent"
    send: false  # true to auto-submit, false to pre-fill only
    model: "GPT-5 (copilot)"  # Optional: model for this handoff
```

| Attribute | Required | Description |
|-----------|----------|-------------|
| `label` | Yes | Display text shown on the handoff button |
| `agent` | Yes | Target agent identifier to switch to |
| `prompt` | No | Prompt text to send to the target agent |
| `send` | No | Auto-submit the prompt if `true` (default: `false`) |
| `model` | No | Language model for the handoff. Use format `Model Name (vendor)`, e.g., `GPT-5 (copilot)` |

### Body Content
- Markdown formatted instructions
- Reference other files with Markdown links
- Reference tools with `#tool:<tool-name>` syntax
- Prepended to user chat prompt when agent is selected

### Example
```markdown
---
name: planner
description: Generate an implementation plan for new features
tools: ['fetch', 'githubRepo', 'search', 'usages']
model: Claude Sonnet 4
handoffs:
  - label: Implement Plan
    agent: agent
    prompt: Implement the plan outlined above.
    send: false
---

# Planning Instructions

You are in planning mode. Generate implementation plans without making code edits.

## Plan Structure
- Overview: Brief description
- Requirements: List of requirements
- Implementation Steps: Detailed steps
- Testing: Required tests
```

### Sub-Agent Example
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

## Skill Files (SKILL.md)

### Location
- Workspace: `<provider>/skills/<skill-name>/SKILL.md`
- Each skill is a **directory** containing at minimum a `SKILL.md` file

### Directory Structure
```
skill-name/
├── SKILL.md           # Required - skill definition
├── scripts/           # Optional - executable code
├── references/        # Optional - additional documentation
└── assets/            # Optional - static resources (templates, images, data)
```

### File Structure
```markdown
---
name: skill-name
description: What this skill does and when to use it.
license: Apache-2.0
compatibility: Requires specific tools or environment
metadata:
  author: org-name
  version: "1.0"
allowed-tools: Bash(git:*) Read
---

[Skill instructions body - Markdown content]
```

### Frontmatter Attributes

| Attribute | Required | Constraints |
|-----------|----------|-------------|
| `name` | Yes | 1-64 chars, lowercase alphanumeric + hyphens, must match directory name |
| `description` | Yes | 1-1024 chars, describes function and trigger keywords |
| `license` | No | License name or reference to bundled file |
| `compatibility` | No | 1-500 chars, environment requirements |
| `metadata` | No | Key-value pairs for additional info |
| `allowed-tools` | No | Space-delimited pre-approved tools (experimental) |

### Name Validation Rules
- Must be 1-64 characters
- Lowercase letters, numbers, and hyphens only (`a-z`, `0-9`, `-`)
- Cannot start or end with hyphen
- Cannot contain consecutive hyphens (`--`)
- **Must match parent directory name exactly**

### Body Content
- Markdown formatted instructions
- No format restrictions
- Recommended sections:
  - Step-by-step instructions
  - Examples of inputs and outputs
  - Common edge cases
- Keep under 500 lines; split longer content into reference files

### Progressive Disclosure
1. **Metadata** (~100 tokens): `name` and `description` loaded at startup
2. **Instructions** (<5000 tokens recommended): Full body loaded when activated
3. **Resources** (as needed): Files in subdirectories loaded on demand

### Example
```markdown
---
name: code-review
description: Performs thorough code review focusing on security, performance, and best practices. Use when reviewing pull requests, checking code quality, or identifying potential issues.
---

# Code Review Skill

Analyzes code for quality, security, and adherence to best practices.

## Review Process

1. Check for security vulnerabilities
2. Identify performance issues
3. Verify coding standards compliance
4. Suggest improvements

## Output Format

Provide findings organized by severity: Critical, Warning, Info.
```

---

## Prompt Files (`.prompt.md`)

### Location
- Workspace: `<provider>/prompts/*.prompt.md`
- User profile: Available across workspaces

### File Structure
```markdown
---
name: prompt-name
description: What this prompt accomplishes
argument-hint: Guide for user input
agent: agent-name
model: Claude Sonnet 4
tools: ['tool1', 'tool2']
---

[Prompt template body with variables]
```

### Frontmatter Attributes

| Attribute | Required | Description |
|-----------|----------|-------------|
| `name` | No | Prompt name used after `/` in chat. Defaults to filename |
| `description` | No | Short description of the prompt |
| `argument-hint` | No | Hint text for user input guidance |
| `agent` | No | Agent to use: `ask`, `edit`, `agent`, or custom agent name |
| `model` | No | Language model to use. Defaults to selected model |
| `tools` | No | List of available tools for this prompt |

### Body Content
- Markdown formatted prompt template
- Reference workspace files with relative Markdown links
- Reference tools with `#tool:<tool-name>` syntax

### Variables

| Variable Type | Syntax | Examples |
|---------------|--------|----------|
| Workspace | `${workspaceFolder}`, `${workspaceFolderBasename}` | Project paths |
| Selection | `${selection}`, `${selectedText}` | Editor selection |
| File Context | `${file}`, `${fileBasename}`, `${fileDirname}`, `${fileBasenameNoExtension}` | Current file info |
| Input | `${input:varName}`, `${input:varName:placeholder}` | User-provided values |

### Example
```markdown
---
name: create-react-form
description: Generate a React form component with validation
agent: agent
tools: ['editFiles']
---

# Create React Form Component

Generate a React form component named ${input:formName:MyForm} with the following requirements:

- Use TypeScript
- Include form validation
- Follow project conventions in [coding standards](../instructions/react.instructions.md)

## Form Fields
${input:fields:Describe the form fields needed}
```

---

## Instruction Files (`.instructions.md`)

### Location
- Workspace: `<provider>/instructions/*.instructions.md`
- User profile: Available across workspaces

### File Structure
```markdown
---
name: Friendly Name
description: What these instructions cover
applyTo: "**/*.ts"
---

[Instruction content - Markdown]
```

### Frontmatter Attributes

| Attribute | Required | Description |
|-----------|----------|-------------|
| `name` | No | Display name in UI. Defaults to filename |
| `description` | No | Short description of the instructions |
| `applyTo` | No* | Glob pattern(s) for automatic application |

*If `applyTo` is not specified, instructions won't apply automatically but can be manually attached.

### ApplyTo Patterns
- Single pattern: `"**/*.ts"`
- Multiple patterns: `["**/*.ts", "**/*.tsx"]`
- Use `**` to apply to all files
- Patterns are relative to workspace root
- Applied when creating/modifying files (not read operations)

### Body Content
- Markdown formatted guidelines
- Reference tools with `#tool:<tool-name>` syntax
- Reference other files with Markdown links

### Example
```markdown
---
name: Python Standards
description: Coding standards for Python files
applyTo: "**/*.py"
---

# Python Coding Standards

- Follow PEP 8 style guide
- Always include type hints
- Write docstrings for all public functions
- Use 4 spaces for indentation
- Prefer f-strings over .format() or % formatting
```

---

## Other Instruction Types

### Global Instructions (`<provider>/copilot-instructions.md`)
- Single file at provider folder root  
- Applies to ALL chat requests automatically
- Enable with `github.copilot.chat.codeGeneration.useInstructionFiles` setting (for GitHub Copilot)
- Also recognized by GitHub Copilot in Visual Studio and GitHub.com

### AGENTS.md
- Place at workspace root
- Applies to all chat requests
- Useful for multi-agent workspaces
- Enable with `chat.useAgentsMdFile` setting
- Nested `AGENTS.md` files supported (experimental) with `chat.useNestedAgentsMdFiles`
- When nested files enabled, VS Code searches recursively in subfolders

### Organization-Level Instructions
- Share instructions across multiple workspaces and repositories within a GitHub organization
- Defined at the GitHub organization level
- Enable with `github.copilot.chat.organizationInstructions.enabled` setting
- Automatically detected and shown alongside personal/workspace instructions

### Instruction Settings for Specific Scenarios

You can configure custom instructions for specialized scenarios via VS Code settings:

| Setting | Purpose |
|---------|---------||
| `github.copilot.chat.reviewSelection.instructions` | Code review instructions |
| `github.copilot.chat.commitMessageGeneration.instructions` | Commit message generation |
| `github.copilot.chat.pullRequestDescriptionGeneration.instructions` | PR title/description generation |

**Format:** Array of objects with `text` (inline) or `file` (reference) property:
```json
{
  "github.copilot.chat.reviewSelection.instructions": [
    { "text": "Always check for security vulnerabilities." },
    { "file": "guidance/review-guidelines.md" }
  ]
}
```

---

## Tool Reference Syntax

In any body content, reference tools using:
```
#tool:<tool-name>
```

Example: "Use #tool:githubRepo to access repository information."

---

## File Location Summary

```
<provider>/                              # .github/, .claude/, .codex/, .config/opencode/, etc.
├── copilot-instructions.md             # Global instructions (single file)
├── agents/
│   ├── my-agent.agent.md               # User-facing agent
│   ├── another-agent.md                # User-facing agent (also valid)
│   └── helper.subagent.agent.md        # Sub-agent (not user-invokable)
├── skills/
│   └── my-skill/
│       ├── SKILL.md                    # Required skill definition
│       ├── scripts/                    # Optional executable code
│       ├── references/                 # Optional documentation
│       └── assets/                     # Optional static resources
├── prompts/
│   └── my-prompt.prompt.md             # Prompt template
└── instructions/
    └── python.instructions.md          # Contextual instructions
```

---

## VS Code Settings Reference

### Core Settings

| Setting | Purpose |
|---------|---------||
| `github.copilot.chat.codeGeneration.useInstructionFiles` | Enable `<provider>/copilot-instructions.md` |
| `chat.instructionsFilesLocations` | Additional instruction file folders |
| `chat.promptFilesLocations` | Additional prompt file folders |
| `chat.agentFilesLocations` | Additional agent file folders |
| `chat.useAgentsMdFile` | Enable `AGENTS.md` file |
| `chat.useNestedAgentsMdFiles` | Enable nested `AGENTS.md` files |
| `chat.useAgentSkills` | Enable skills in `.claude/skills/` or `.github/skills/` |

### Instruction Behavior Settings

| Setting | Purpose |
|---------|---------||
| `chat.includeApplyingInstructions` | Enable instructions with `applyTo` patterns |
| `chat.includeReferencedInstructions` | Enable instructions referenced via Markdown links |
| `github.copilot.chat.organizationInstructions.enabled` | Enable organization-level instructions |
| `github.copilot.chat.organizationCustomAgents.enabled` | Enable organization-level custom agents |

---

## Tips for Defining Custom Instructions

- Keep instructions short and self-contained - each should be a single, simple statement
- For task or language-specific instructions, use multiple `.instructions.md` files with selective `applyTo` patterns
- Store project-specific instructions in your workspace to share with team members
- Reuse and reference instructions files in prompt files and custom agents to avoid duplication
- Instructions are applied when creating/modifying files, typically not for read operations
