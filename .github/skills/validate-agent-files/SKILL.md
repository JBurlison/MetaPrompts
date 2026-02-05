---
name: validate-agent-files
description: Validates AI coding assistant customization files (agents, skills, prompts, instructions) for correct format and structure. Works with GitHub Copilot, Claude Code, Codex, OpenCode, and other providers. Use when checking if agent files are properly configured, troubleshooting agent issues, or before committing new customization files.
---

# Validate Agent Files

Validates that agent, skill, prompt, and instruction files follow the correct format and structure.

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

- Before committing new agents, skills, prompts, or instructions
- When an agent isn't behaving as expected
- To audit existing customization files for issues
- After modifying any `.github` customization files

## Validation Process

### Step 1: Identify File Type

Determine the type based on location and extension:
- `<provider>/agents/*.md` → Agent file (user-invokable)
- `<provider>/agents/*.subagent.agent.md` → Sub-agent file (workflow component)
- `<provider>/skills/*/SKILL.md` → Skill file
- `<provider>/prompts/*.prompt.md` → Prompt file
- `<provider>/instructions/*.instructions.md` → Instruction file

### Step 2: Apply Type-Specific Validation

## Agent File Validation (`<provider>/agents/*.md`)

**Required Structure:**
```yaml
---
name: agent-name
description: When to use this agent (should include examples)
user-invokable: true  # Optional, defaults to true
---

[System prompt body]
```

**Supported Frontmatter Attributes:**
- `name` (required) - Agent identifier
- `description` (required) - When/how to use, with examples
- `user-invokable` (optional) - Set to `false` for sub-agents (default: `true`)
- `tools` - List of allowed tools
- `model` - Specific model to use
- `handoffs` - Other agents this can delegate to

**Checks:**
1. ✓ YAML frontmatter present with `---` delimiters
2. ✓ `name` field exists and is non-empty
3. ✓ `description` field exists (recommend 50+ characters with examples)
4. ✓ Body content exists after frontmatter
5. ✓ If `tools` specified, they are valid tool names
6. ✓ If filename contains `.subagent.agent.md`, verify `user-invokable: false` is set

**Naming Convention Checks:**
- User-facing agents: `<name>.agent.md` or `<name>.md`
- Sub-agents: `<name>.subagent.agent.md` with `user-invokable: false`

**Common Issues:**
- Missing `---` delimiters
- Empty or minimal description
- No usage examples in description
- Body content missing or too brief
- Sub-agent missing `user-invokable: false`
- Sub-agent not using `.subagent.agent.md` naming convention

## Skill File Validation (`<provider>/skills/*/SKILL.md`)

**Required Structure:**
```yaml
---
name: skill-name
description: What this skill does and when to use it.
---

[Skill instructions body]
```

**Supported Frontmatter Attributes:**
- `name` (required) - Must match parent directory name, lowercase with hyphens
- `description` (required) - Max 1024 chars, describes function and triggers
- `license` (optional) - License information
- `compatibility` (optional) - Environment requirements
- `metadata` (optional) - Key-value pairs for additional info
- `allowed-tools` (optional) - Space-delimited pre-approved tools

**Checks:**
1. ✓ File is named `SKILL.md` inside a directory
2. ✓ `name` matches parent directory name exactly
3. ✓ `name` is lowercase, alphanumeric with hyphens only
4. ✓ `name` doesn't start/end with hyphen or have consecutive hyphens
5. ✓ `description` is 1-1024 characters
6. ✓ Body content provides clear instructions

**Common Issues:**
- `name` doesn't match directory name
- Uppercase characters in name
- Description too vague (should include trigger keywords)
- Missing instructions in body

## Prompt File Validation (`<provider>/prompts/*.prompt.md`)

**Required Structure:**
```yaml
---
mode: agent
description: What this prompt does
---

[Prompt template with {{variables}}]
```

**Supported Frontmatter Attributes:**
- `mode` (optional) - One of: `agent` (default), `ask`, `edit`, `generate`
- `tools` (optional) - Available tools for this prompt
- `description` (optional but recommended) - What the prompt accomplishes

**Checks:**
1. ✓ File has `.prompt.md` extension
2. ✓ If `mode` present, it's a valid value
3. ✓ Variables use `{{variableName}}` syntax
4. ✓ Body content exists (the prompt itself)

**Common Issues:**
- Wrong extension (`.md` instead of `.prompt.md`)
- Invalid `mode` value
- Undefined variables in template

## Instruction File Validation (`<provider>/instructions/*.instructions.md`)

**Required Structure:**
```yaml
---
applyTo: "**/*.ts"
---

[Contextual instructions]
```

**Supported Frontmatter Attributes:**
- `applyTo` (required) - Glob pattern(s) for when instructions apply

**Checks:**
1. ✓ File has `.instructions.md` extension
2. ✓ `applyTo` field exists
3. ✓ `applyTo` contains valid glob pattern(s)
4. ✓ Body content provides meaningful guidance

**Common Issues:**
- Wrong extension
- Missing `applyTo` field
- Invalid glob syntax
- Empty or minimal instructions

## Output Format

```markdown
## Validation: [filename]

**Type:** [Agent|Skill|Prompt|Instruction]
**Status:** ✅ Valid | ⚠️ Warnings | ❌ Invalid

### Issues
- [Issue 1 with line number if applicable]
- [Issue 2]

### Recommendations
- [Suggestion for improvement]
```

## Batch Validation

When validating all files, provide summary:

```markdown
## Validation Summary

| Type | Total | Valid | Warnings | Invalid |
|------|-------|-------|----------|---------|
| Agents | X | X | X | X || Sub-Agents | X | X | X | X || Skills | X | X | X | X |
| Prompts | X | X | X | X |
| Instructions | X | X | X | X |

### Files Requiring Attention
- [List files with issues]
```
