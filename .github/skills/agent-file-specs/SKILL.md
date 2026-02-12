---
name: agent-file-specs
description: Contains the complete specifications for AI coding assistant customization files including agents, skills, prompts, and instructions. Works with GitHub Copilot, Claude Code, Codex, OpenCode, and other providers. Use this skill when you need to reference the correct file format, required fields, supported attributes, file locations, or VS Code settings for any customization file. Follows the Agent Skills open standard (agentskills.io).
---

# AI Coding Assistant Customization File Specifications

This skill provides the authoritative, up-to-date specifications for all AI
coding assistant customization file types. Each file type has its own dedicated
reference document with complete attribute tables, validation rules, examples,
and best practices.

> **Standard:** These specs align with the [Agent Skills open standard](https://agentskills.io/specification)
> and the [VS Code Copilot customization documentation](https://code.visualstudio.com/docs/copilot/customization/overview).

## How to Use This Skill

1. Read the **Quick Reference** below to identify the file type you need.
2. Follow the link to the dedicated reference document for full details.
3. Use the [File Locations](references/FILE-LOCATIONS.md) reference when you
   need to know where files go for a specific provider.
4. Use the [Settings](references/SETTINGS.md) reference for VS Code
   configuration.

> **Important:** Detailed specifications live in the `references/` folder —
> not in this file. This keeps the main skill file lightweight for efficient
> context loading per the
> [progressive disclosure](https://agentskills.io/specification) model.

---

## Quick Reference

| Type | Extension | Purpose | Reference |
|------|-----------|---------|-----------|
| **Agent** | `.agent.md` / `.md` | Custom AI personas with specialized behaviors, tool restrictions, and handoff workflows | [AGENTS.md](references/AGENTS.md) |
| **Skill** | `SKILL.md` (directory-based) | Reusable, portable capabilities with scripts and resources — follows the Agent Skills open standard | [SKILLS.md](references/SKILLS.md) |
| **Prompt** | `.prompt.md` | Reusable prompt templates invoked as slash commands | [PROMPTS.md](references/PROMPTS.md) |
| **Instruction** | `.instructions.md` | Contextual guidance applied automatically by file type or description | [INSTRUCTIONS.md](references/INSTRUCTIONS.md) |
| **File Locations** | — | Provider folder mapping, directory structure, naming conventions | [FILE-LOCATIONS.md](references/FILE-LOCATIONS.md) |
| **VS Code Settings** | — | Settings that control customization file discovery and behavior | [SETTINGS.md](references/SETTINGS.md) |

---

## Provider Support

These specifications work across multiple AI coding assistant providers. Each
provider uses its own base folder but the file formats are consistent:

| Provider | Base Folder |
|----------|-------------|
| GitHub Copilot | `.github/` |
| Claude Code | `.claude/` |
| Codex | `.codex/` |
| OpenCode | `.config/opencode/` |

See [FILE-LOCATIONS.md](references/FILE-LOCATIONS.md) for the complete
directory structure and provider-specific details.

---

## File Type Selection Guide

Use this decision tree to choose the right file type:

- **"I want coding standards applied everywhere"** → Use always-on instructions
  (`.github/copilot-instructions.md` or `AGENTS.md`).
  See [INSTRUCTIONS.md](references/INSTRUCTIONS.md).

- **"I want different rules for different file types"** → Use file-based
  instructions (`.instructions.md` with `applyTo` patterns).
  See [INSTRUCTIONS.md](references/INSTRUCTIONS.md).

- **"I have a reusable task I run repeatedly"** → Use a prompt file
  (`.prompt.md`) invoked as a slash command.
  See [PROMPTS.md](references/PROMPTS.md).

- **"I need a multi-step workflow with scripts and resources"** → Use an Agent
  Skill (directory with `SKILL.md`). Portable across tools.
  See [SKILLS.md](references/SKILLS.md).

- **"I need a specialized AI persona with tool restrictions"** → Use a custom
  agent (`.agent.md`). Supports handoffs for multi-step workflows.
  See [AGENTS.md](references/AGENTS.md).

---

## Authoritative Sources

- [VS Code Copilot Customization Overview](https://code.visualstudio.com/docs/copilot/customization/overview)
- [VS Code Custom Agents](https://code.visualstudio.com/docs/copilot/customization/custom-agents)
- [VS Code Agent Skills](https://code.visualstudio.com/docs/copilot/customization/agent-skills)
- [VS Code Prompt Files](https://code.visualstudio.com/docs/copilot/customization/prompt-files)
- [VS Code Custom Instructions](https://code.visualstudio.com/docs/copilot/customization/custom-instructions)
- [Agent Skills Specification (agentskills.io)](https://agentskills.io/specification)
- [Awesome Copilot (community examples)](https://github.com/github/awesome-copilot)
- [Anthropic Reference Skills](https://github.com/anthropics/skills)
