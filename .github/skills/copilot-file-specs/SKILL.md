---
name: copilot-file-specs
description: Contains the complete specifications for VS Code GitHub Copilot customization files including agents, skills, prompts, and instructions. Use this skill when you need to reference the correct file format, required fields, supported attributes, or file locations for any Copilot customization file.
---

# VS Code Copilot Customization File Specifications

This skill contains the authoritative specifications for all GitHub Copilot customization file types in VS Code.

## File Types Overview

| Type | Extension | Location | Purpose |
|------|-----------|----------|---------|
| Agent | `.agent.md` or `.md` | `.github/agents/` | Custom AI personas with specialized behaviors |
| Skill | `SKILL.md` | `.github/skills/<name>/` | Reusable capabilities (directory-based) |
| Prompt | `.prompt.md` | `.github/prompts/` | Reusable prompt templates |
| Instruction | `.instructions.md` | `.github/instructions/` | Contextual guidance for file types |

---

## Agent Files (`.agent.md`)

### Location
- Workspace: `.github/agents/*.agent.md` or `.github/agents/*.md`
- User profile: Available across workspaces

### File Structure
```markdown
---
name: agent-name
description: Brief description shown as placeholder in chat input
argument-hint: Optional hint for user input
tools: ['tool1', 'tool2']
model: Claude Sonnet 4
handoffs:
  - label: Button Text
    agent: target-agent
    prompt: Prompt to send
    send: false
---

[Agent instructions body - Markdown content]
```

### Frontmatter Attributes

| Attribute | Required | Description |
|-----------|----------|-------------|
| `name` | No | Agent name. If not specified, filename is used |
| `description` | Yes (recommended) | Brief description shown as placeholder text in chat input field |
| `argument-hint` | No | Hint text shown in chat input to guide users |
| `tools` | No | List of tool/tool set names available to this agent. Use `<server>/*` for all MCP server tools |
| `model` | No | AI model to use. If not specified, uses currently selected model |
| `infer` | No | Boolean to enable use as subagent (default: true) |
| `target` | No | Target environment: `vscode` or `github-copilot` |
| `mcp-servers` | No | MCP server configs for GitHub Copilot target |
| `handoffs` | No | List of handoff configurations for workflow transitions |

### Handoff Configuration

```yaml
handoffs:
  - label: "Display text for button"
    agent: "target-agent-name"
    prompt: "Prompt text to send to target agent"
    send: false  # true to auto-submit, false to pre-fill only
```

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

---

## Skill Files (SKILL.md)

### Location
- Workspace: `.github/skills/<skill-name>/SKILL.md`
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
- Workspace: `.github/prompts/*.prompt.md`
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
- Workspace: `.github/instructions/*.instructions.md`
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

### Global Instructions (`.github/copilot-instructions.md`)
- Single file at workspace root
- Applies to ALL chat requests automatically
- Enable with `github.copilot.chat.codeGeneration.useInstructionFiles` setting

### AGENTS.md
- Place at workspace root
- Applies to all chat requests
- Useful for multi-agent workspaces
- Enable with `chat.useAgentsMdFile` setting
- Nested `AGENTS.md` files supported (experimental) with `chat.useNestedAgentsMdFiles`

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
.github/
├── copilot-instructions.md          # Global instructions (single file)
├── agents/
│   ├── my-agent.agent.md            # Custom agent (preferred extension)
│   └── another-agent.md             # Custom agent (also valid)
├── skills/
│   └── my-skill/
│       ├── SKILL.md                 # Required skill definition
│       ├── scripts/                 # Optional executable code
│       ├── references/              # Optional documentation
│       └── assets/                  # Optional static resources
├── prompts/
│   └── my-prompt.prompt.md          # Prompt template
└── instructions/
    └── python.instructions.md       # Contextual instructions
```

---

## VS Code Settings Reference

| Setting | Purpose |
|---------|---------|
| `github.copilot.chat.codeGeneration.useInstructionFiles` | Enable `.github/copilot-instructions.md` |
| `chat.instructionsFilesLocations` | Additional instruction file folders |
| `chat.promptFilesLocations` | Additional prompt file folders |
| `chat.useAgentsMdFile` | Enable `AGENTS.md` file |
| `chat.useNestedAgentsMdFiles` | Enable nested `AGENTS.md` files |
| `chat.useAgentSkills` | Enable skills in `.claude/skills/` or `.github/skills/` |
