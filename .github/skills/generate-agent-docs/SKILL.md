---
name: generate-agent-docs
description: Generates documentation and usage guides for agents, skills, prompts, and instructions. Works with GitHub Copilot, Claude Code, Codex, OpenCode, and other providers. Use when onboarding team members, creating README files for your customizations, or generating usage examples for existing agents.
---

# Generate Agent Documentation

Creates user-friendly documentation for AI coding assistant customization files.

## Provider Folder Reference

This skill works across multiple AI coding assistant providers:

| Provider | Base Folder |
|----------|-------------|
| GitHub Copilot | `.github/` |
| Claude Code | `.claude/` |
| Codex | `.codex/` |
| OpenCode | `.config/opencode/` |

**Throughout this document, `<provider>/` represents your chosen provider's base folder.**

## When to Use

- Onboarding new team members to your agent ecosystem
- Creating a catalog of available agents and skills
- Generating usage examples for specific agents
- Documenting your customization setup for reference

## Documentation Types

### Individual Item Documentation

Generate detailed docs for a single agent, skill, prompt, or instruction.

### Catalog Documentation

Generate a comprehensive overview of all customization files.

## Output Templates

### Agent Documentation

```markdown
# Agent: [Agent Name]

## Overview
[What this agent does and why it exists]

## When to Use This Agent

Use `@[agent-name]` when:
- [Scenario 1]
- [Scenario 2]
- [Scenario 3]

**Don't use** when:
- [Anti-pattern 1]
- [Anti-pattern 2]

## How It Behaves

This agent will:
- [Behavior 1]
- [Behavior 2]
- [Behavior 3]

## Example Conversations

### Example 1: [Scenario Title]

**You:** [Example user message]

**Agent:** [How agent responds - summarized]

### Example 2: [Scenario Title]

**You:** [Example user message]

**Agent:** [How agent responds - summarized]

## Tips for Best Results

- [Tip 1 for effective usage]
- [Tip 2 for effective usage]
- [Common mistake to avoid]

## Related

- **[Related Agent]**: Use for [distinction]
- **[Related Skill]**: This agent uses this for [purpose]
```

### Skill Documentation

```markdown
# Skill: [Skill Name]

## Purpose
[What this skill accomplishes]

## Triggers
This skill activates when:
- [Trigger keyword/scenario 1]
- [Trigger keyword/scenario 2]

## What It Does
[Step-by-step of what the skill does]

## Used By
- [Agent 1] - for [purpose]
- [Agent 2] - for [purpose]

## Example

**Scenario:** [Description]

**Input:** [What's provided]

**Output:** [What's produced]
```

### Prompt Documentation

```markdown
# Prompt: [Prompt Name]

## Purpose
[What task this prompt accomplishes]

## Mode
`[mode]` - [explanation of what this mode does]

## Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{{var1}}` | [what it's for] | [example value] |

## How to Use

1. [Step 1]
2. [Step 2]

## Example

**With these values:**
- `{{var1}}` = [value]

**Produces:**
[Example output]
```

### Instruction Documentation

```markdown
# Instructions: [Name]

## Applies To
Files matching: `[glob pattern]`

## Purpose
[What guidance these instructions provide]

## Key Rules

1. [Rule 1]
2. [Rule 2]
3. [Rule 3]

## When Active
These instructions automatically apply when you're working with files that match the pattern above.

## Examples of Affected Files
- `[example path 1]`
- `[example path 2]`
```

### Catalog Documentation

```markdown
# AI Coding Assistant Customizations

This document catalogs all custom agents, skills, prompts, and instructions configured for this project.

## Provider
[Specify which provider folder is used: `.github/`, `.claude/`, `.codex/`, `.config/opencode/`]

## Quick Reference

### Agents (User-Invokable)

| Agent | Purpose | Invoke With |
|-------|---------|-------------|
| [name] | [brief purpose] | `@[name]` |

### Sub-Agents (Workflow Components)

| Sub-Agent | Purpose | Used By |
|-----------|---------|---------|
| [name] | [brief purpose] | [parent workflow agent] |

### Skills

| Skill | Purpose | Triggers |
|-------|---------|----------|
| [name] | [brief purpose] | [keywords] |

### Prompts

| Prompt | Mode | Purpose |
|--------|------|---------|
| [name] | [mode] | [brief purpose] |

### Instructions

| Instructions | Applies To | Purpose |
|--------------|------------|---------|
| [name] | [pattern] | [brief purpose] |

## Detailed Documentation

[Full documentation for each item]

## Usage Guidelines

[General guidance on how to use these customizations effectively]
```

## Generation Process

### Step 1: Read Source File
Load the agent/skill/prompt/instruction file.

### Step 2: Extract Key Information
- Name and description from frontmatter
- Behaviors and rules from body
- Examples if present
- Related items (skills, handoffs)

### Step 3: Enhance with Context
- Generate additional examples based on purpose
- Identify related items from the ecosystem
- Add tips based on common patterns

### Step 4: Format Output
Apply appropriate template based on item type.

## Quality Guidelines

Generated documentation should be:

1. **Clear** - No jargon without explanation
2. **Practical** - Real, actionable examples
3. **Complete** - Covers all key aspects
4. **Concise** - No unnecessary padding
5. **Current** - Reflects actual file contents

## Additional Examples

When generating examples beyond those in the source:

- Cover different use case variations
- Show edge cases and how they're handled
- Demonstrate integration with other items
- Illustrate common mistakes to avoid
