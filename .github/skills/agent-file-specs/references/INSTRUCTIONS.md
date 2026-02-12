# Instruction Files (`.instructions.md`)

> **Source:** [VS Code Custom Instructions Documentation](https://code.visualstudio.com/docs/copilot/customization/custom-instructions)

Custom instructions define common guidelines and rules that automatically
influence how AI generates code and handles development tasks. Instead of
manually including context in every chat prompt, specify custom instructions in
Markdown files for consistent, convention-aligned responses.

---

## Types of Instructions

VS Code supports two categories of custom instructions. When multiple
instruction files exist, VS Code combines them — no specific order is
guaranteed.

### Always-On Instructions

Automatically included in every chat request.

| Type | File | Notes |
|------|------|-------|
| Global instructions | `<provider>/copilot-instructions.md` | Single file at provider folder root. Applies to all chat requests automatically. |
| AGENTS.md | `AGENTS.md` at workspace root | Useful for multi-agent workspaces. Enable with `chat.useAgentsMdFile` setting. |
| Nested AGENTS.md | `AGENTS.md` in subfolders | Experimental. Enable with `chat.useNestedAgentsMdFiles`. VS Code searches recursively. |
| Organization-level | Defined at GitHub org level | Shared across repos. Enable with `github.copilot.chat.organizationInstructions.enabled`. |

### File-Based Instructions

Applied dynamically based on file patterns or description matching.

| Type | File | Notes |
|------|------|-------|
| Pattern-matched | `*.instructions.md` with `applyTo` | Automatically applied when working on files matching the glob pattern. |
| Description-matched | `*.instructions.md` with `description` | Semantically matched to current task based on description content. |
| Manual | `*.instructions.md` without `applyTo` | Not applied automatically; can be manually attached to a chat request. |

---

## File Location

| Scope | Location |
|-------|----------|
| Workspace | `<provider>/instructions/*.instructions.md` |
| User profile | `prompts` folder of the current VS Code profile |

Additional locations can be configured with the `chat.instructionsFilesLocations`
setting.

> **Provider note:** `<provider>` is your provider's base folder (`.github/`,
> `.claude/`, `.codex/`, `.config/opencode/`).

---

## File Structure

```markdown
---
name: Friendly Name
description: What these instructions cover
applyTo: "**/*.ts"
---

[Instruction content — Markdown]
```

---

## Frontmatter Attributes

| Attribute | Required | Type | Default | Description |
|-----------|----------|------|---------|-------------|
| `name` | No | string | filename | Display name shown in the UI. |
| `description` | No | string | — | Short description shown on hover. Also used for semantic matching to determine when to apply instructions. |
| `applyTo` | No* | string or string[] | — | Glob pattern(s) for automatic application. Patterns are relative to workspace root. |

> *If `applyTo` is not specified, instructions are not applied automatically.
> They can still be manually attached to a chat request or matched via
> description.

---

## ApplyTo Patterns

| Pattern | Matches |
|---------|---------|
| `"**/*.ts"` | All TypeScript files in the workspace |
| `"**/*.py"` | All Python files |
| `["**/*.ts", "**/*.tsx"]` | TypeScript and TSX files |
| `"**"` | All files in the workspace |
| `"src/frontend/**"` | All files under `src/frontend/` |
| `"**/test/**/*.ts"` | All TypeScript files in any `test/` directory |

- Patterns are relative to the workspace root.
- Instructions with `applyTo` are applied when **creating or modifying** files
  matching the pattern — not for read operations.
- Enable pattern-based instructions with the `chat.includeApplyingInstructions`
  setting.

---

## Body Content

The body contains guidelines in Markdown format:

- **Markdown formatting:** Full Markdown including headers, lists, code blocks.
- **Tool references:** Reference tools with `#tool:<tool-name>` syntax.
- **File references:** Link to other files with Markdown links. Enable with
  `chat.includeReferencedInstructions`.
- **Code examples:** Show preferred and avoided patterns.

---

## Global Instructions (`copilot-instructions.md`)

A single `<provider>/copilot-instructions.md` file applies to ALL chat requests
in the workspace automatically.

### When to Use

- Coding style and naming conventions that apply across the project
- Technology stack declarations and preferred libraries
- Architectural patterns to follow or avoid
- Security requirements and error handling approaches
- Documentation standards

### Example

```markdown
# Project Guidelines

- Use TypeScript strict mode for all files
- Prefer functional components with hooks over class components
- Use date-fns instead of moment.js (moment is deprecated)
- All API responses must follow the ApiResponse<T> interface
- Error handling: always use the custom AppError class
```

### Settings

| Setting | Purpose |
|---------|---------|
| `github.copilot.chat.codeGeneration.useInstructionFiles` | Enable `copilot-instructions.md` |

---

## AGENTS.md

Place at workspace root for always-on instructions recognized by multiple AI
agents.

### When to Use

- You work with multiple AI coding agents and want shared instructions
- You want subfolder-level instructions for different parts of a monorepo

### Settings

| Setting | Purpose |
|---------|---------|
| `chat.useAgentsMdFile` | Enable `AGENTS.md` file |
| `chat.useNestedAgentsMdFiles` | Enable nested `AGENTS.md` files in subfolders (experimental) |

---

## Organization-Level Instructions

Share instructions across multiple workspaces and repositories within a GitHub
organization.

- Defined at the GitHub organization level
- Automatically detected and shown alongside personal/workspace instructions
- Enable with `github.copilot.chat.organizationInstructions.enabled`

---

## Instruction Priority

When multiple instruction types exist, all are provided to the AI. Higher
priority takes precedence in conflicts:

1. **Personal instructions** (user-level) — highest priority
2. **Repository instructions** (`copilot-instructions.md` or `AGENTS.md`)
3. **Organization instructions** — lowest priority

---

## Scenario-Specific Instruction Settings

Configure custom instructions for specialized scenarios via VS Code settings:

| Setting | Purpose |
|---------|---------|
| `github.copilot.chat.reviewSelection.instructions` | Code review instructions |
| `github.copilot.chat.commitMessageGeneration.instructions` | Commit message generation |
| `github.copilot.chat.pullRequestDescriptionGeneration.instructions` | PR title/description generation |

**Format:** Array of objects with `text` (inline) or `file` (reference)
property:

```json
{
  "github.copilot.chat.reviewSelection.instructions": [
    { "text": "Always check for security vulnerabilities." },
    { "file": "guidance/review-guidelines.md" }
  ]
}
```

> Support for settings-based instructions may be removed in the future. Prefer
> file-based instructions.

---

## Example: Language-Specific Instructions

```markdown
---
name: Python Standards
description: Coding standards for Python files
applyTo: "**/*.py"
---

# Python Coding Standards

- Follow the PEP 8 style guide
- Use type hints for all function signatures
- Write docstrings for all public functions
- Use 4 spaces for indentation
- Prefer f-strings over .format() or % formatting
```

---

## Example: Framework-Specific Instructions

```markdown
---
name: React Conventions
description: React component conventions and patterns
applyTo: ["**/*.tsx", "**/*.jsx"]
---

# React Conventions

- Use functional components with hooks (no class components)
- Use React.FC<Props> for component typing
- Keep components under 200 lines; extract sub-components
- Use custom hooks for shared logic (use* prefix)
- Prefer named exports over default exports
```

---

## Tips for Effective Instructions

- **Keep instructions short and self-contained.** Each should be a single,
  simple statement.
- **Include reasoning behind rules.** The AI makes better decisions when it
  understands why (e.g., "Use `date-fns` instead of `moment.js` — moment.js is
  deprecated and increases bundle size.").
- **Show preferred and avoided patterns** with concrete code examples.
- **Focus on non-obvious rules.** Skip conventions that linters or formatters
  already enforce.
- **Use multiple `.instructions.md` files** per topic with selective `applyTo`
  patterns for better organization.
- **Store project-specific instructions in your workspace** to share with team
  members via version control.
- **Reuse instructions** by referencing them in prompt files and custom agents
  via Markdown links.
- **Whitespace between instructions** is ignored — format for readability.

---

## Diagnostics

If an instructions file isn't being applied:

1. Verify the file is in a recognized location (`chat.instructionsFilesLocations`).
2. Check the `applyTo` glob pattern matches the files you're working on.
3. Verify relevant settings are enabled:
   - `chat.includeApplyingInstructions` for pattern-based instructions
   - `chat.includeReferencedInstructions` for Markdown-linked instructions
   - `chat.useAgentsMdFile` for `AGENTS.md`
4. Use the chat customization diagnostics view: right-click in Chat →
   **Diagnostics**.
5. Check the References section in the chat response to see which instructions
   were applied.
