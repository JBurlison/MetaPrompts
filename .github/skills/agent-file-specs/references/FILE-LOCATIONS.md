# File Locations and Directory Structure

> **Sources:**
> - [VS Code Copilot Customization Overview](https://code.visualstudio.com/docs/copilot/customization/overview)
> - [Agent Skills Specification (agentskills.io)](https://agentskills.io/specification)

This reference covers where customization files are stored, how provider
directories map, naming conventions, and the overall project layout.

---

## Provider Folder Mapping

Different AI coding assistant providers use the same file formats but store
them in their own base folder:

| Provider | Base Folder | Notes |
|----------|-------------|-------|
| GitHub Copilot | `.github/` | Most common; widely documented |
| Claude Code | `.claude/` | Anthropic's Claude in VS Code |
| Codex | `.codex/` | OpenAI Codex-based tools |
| OpenCode | `.config/opencode/` | Open-source alternatives |

Throughout this documentation, `<provider>/` represents your chosen provider's
base folder. Replace with the appropriate directory for your environment.

---

## Complete Directory Structure

```
<provider>/                              # .github/, .claude/, .codex/, etc.
├── copilot-instructions.md             # Global always-on instructions (single file)
├── agents/
│   ├── my-agent.agent.md               # User-facing agent
│   ├── another-agent.md                # User-facing agent (also valid)
│   └── helper.subagent.agent.md        # Sub-agent (not user-invokable)
├── skills/
│   └── my-skill/                       # Each skill is a directory
│       ├── SKILL.md                    # Required — skill definition
│       ├── scripts/                    # Optional — executable code
│       ├── references/                 # Optional — additional documentation
│       └── assets/                     # Optional — templates, images, data
├── prompts/
│   └── my-prompt.prompt.md             # Prompt template (slash command)
└── instructions/
    └── python.instructions.md          # File-based contextual instructions
```

### Workspace Root Files

```
<workspace-root>/
├── AGENTS.md                           # Always-on instructions (multi-agent)
└── <subfolder>/
    └── AGENTS.md                       # Nested AGENTS.md (experimental)
```

---

## File Type Locations

### Agent Files

| Scope | Location | Extension |
|-------|----------|-----------|
| Workspace | `<provider>/agents/` | `*.agent.md` or `*.md` |
| Sub-agents | `<provider>/agents/` | `*.subagent.agent.md` |
| User profile | Current VS Code profile folder | `*.agent.md` |
| Organization | GitHub organization level | `*.agent.md` |
| Custom paths | `chat.agentFilesLocations` setting | — |

### Skill Directories

| Scope | Location |
|-------|----------|
| Workspace | `<provider>/skills/<skill-name>/SKILL.md` |
| Personal | `~/.copilot/skills/`, `~/.claude/skills/`, `~/.agents/skills/` |
| Custom paths | `chat.agentSkillsLocations` setting |

### Prompt Files

| Scope | Location | Extension |
|-------|----------|-----------|
| Workspace | `<provider>/prompts/` | `*.prompt.md` |
| User profile | `prompts` folder of current VS Code profile | `*.prompt.md` |
| Custom paths | `chat.promptFilesLocations` setting | — |

### Instruction Files

| Scope | Location | Extension |
|-------|----------|-----------|
| Workspace | `<provider>/instructions/` | `*.instructions.md` |
| User profile | `prompts` folder of current VS Code profile | `*.instructions.md` |
| Custom paths | `chat.instructionsFilesLocations` setting | — |

---

## Naming Conventions

### Agent Files

| Type | Pattern | Example |
|------|---------|---------|
| User-facing agent | `<name>.agent.md` | `planner.agent.md` |
| User-facing agent (alt) | `<name>.md` | `planner.md` |
| Sub-agent | `<name>.subagent.agent.md` | `due-diligence.subagent.agent.md` |

> Any `.md` file in `<provider>/agents/` is treated as a custom agent.

### Skill Directories

| Rule | Example |
|------|---------|
| Directory name = `name` field | `skills/code-review/` → `name: code-review` |
| Lowercase, hyphens only | `my-skill` (not `My_Skill`) |
| 1–64 characters | `a` through 64 chars max |
| No leading/trailing hyphens | `my-skill` (not `-my-skill`) |
| No consecutive hyphens | `my-skill` (not `my--skill`) |

### Prompt Files

| Pattern | Example |
|---------|---------|
| `<name>.prompt.md` | `create-react-form.prompt.md` |

### Instruction Files

| Pattern | Example |
|---------|---------|
| `<name>.instructions.md` | `python.instructions.md` |

---

## Tool Reference Syntax

In all body content (agents, skills, prompts, instructions), reference tools
using:

```
#tool:<tool-name>
```

Example: `Use #tool:githubRepo to access repository information.`

---

## Settings for Custom Locations

You can extend the default search paths for all file types:

| Setting | Default Path | Purpose |
|---------|-------------|---------|
| `chat.agentFilesLocations` | `<provider>/agents/` | Additional agent file folders |
| `chat.agentSkillsLocations` | `<provider>/skills/` | Additional skill folders |
| `chat.promptFilesLocations` | `<provider>/prompts/` | Additional prompt file folders |
| `chat.instructionsFilesLocations` | `<provider>/instructions/` | Additional instruction file folders |

This is useful for:
- Sharing files across projects from a central location
- Keeping personal customizations separate from workspace files
- Organizing large projects with multiple configuration folders
