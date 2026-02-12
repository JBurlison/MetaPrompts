# VS Code Settings Reference

> **Source:** [VS Code Copilot Customization Overview](https://code.visualstudio.com/docs/copilot/customization/overview)

This reference covers all VS Code settings that control the discovery, loading,
and behavior of AI coding assistant customization files (agents, skills, prompts,
and instructions).

---

## Core Discovery Settings

These settings control where VS Code looks for customization files and whether
they are enabled.

| Setting | Type | Default | Description |
|---------|------|---------|-------------|
| `github.copilot.chat.codeGeneration.useInstructionFiles` | boolean | `true` | Enable the `<provider>/copilot-instructions.md` global instructions file. |
| `chat.instructionsFilesLocations` | string[] | `[".github/instructions"]` | Additional folders where VS Code searches for `*.instructions.md` files. |
| `chat.promptFilesLocations` | string[] | `[".github/prompts"]` | Additional folders where VS Code searches for `*.prompt.md` files. |
| `chat.agentFilesLocations` | string[] | `[".github/agents"]` | Additional folders where VS Code searches for `*.agent.md` files. |
| `chat.agentSkillsLocations` | string[] | — | Additional folders where VS Code searches for skill directories. |
| `chat.useAgentsMdFile` | boolean | — | Enable `AGENTS.md` file at workspace root. |
| `chat.useNestedAgentsMdFiles` | boolean | — | Enable nested `AGENTS.md` files in subfolders (experimental). |
| `chat.useAgentSkills` | boolean | — | Enable skills in `.claude/skills/` or `.github/skills/`. |

---

## Instruction Behavior Settings

These settings control how instructions are matched and applied.

| Setting | Type | Default | Description |
|---------|------|---------|-------------|
| `chat.includeApplyingInstructions` | boolean | — | Enable instructions with `applyTo` glob patterns to be applied automatically when matching files are involved. |
| `chat.includeReferencedInstructions` | boolean | — | Enable instructions referenced via Markdown links in other files to be included in context. |

---

## Organization and Sharing Settings

These settings enable organization-wide customization sharing via GitHub.

| Setting | Type | Default | Description |
|---------|------|---------|-------------|
| `github.copilot.chat.organizationInstructions.enabled` | boolean | `false` | Enable discovery and use of organization-level custom instructions defined at the GitHub organization level. |
| `github.copilot.chat.organizationCustomAgents.enabled` | boolean | `false` | Enable discovery and use of organization-level custom agents defined at the GitHub organization level. |

---

## Prompt Behavior Settings

| Setting | Type | Default | Description |
|---------|------|---------|-------------|
| `chat.promptFilesRecommendations` | boolean | — | Show prompts as recommended actions when starting a new chat session. |

---

## Scenario-Specific Instruction Settings

Configure custom instructions for specific VS Code scenarios. These accept an
array of objects with `text` (inline instruction) or `file` (path to Markdown
file) properties.

| Setting | Purpose |
|---------|---------|
| `github.copilot.chat.reviewSelection.instructions` | Code review instructions |
| `github.copilot.chat.commitMessageGeneration.instructions` | Commit message generation |
| `github.copilot.chat.pullRequestDescriptionGeneration.instructions` | PR title and description generation |

### Format

```json
{
  "github.copilot.chat.reviewSelection.instructions": [
    { "text": "Always check for security vulnerabilities." },
    { "file": "guidance/review-guidelines.md" }
  ]
}
```

> Settings-based instructions may be removed in the future. Prefer file-based
> instructions where possible.

---

## Sync Settings

User-level prompt and instruction files can be synced across devices using
[Settings Sync](https://code.visualstudio.com/docs/configure/settings-sync).

1. Enable Settings Sync.
2. Run **Settings Sync: Configure** from the Command Palette.
3. Select **Prompts and Instructions** from the list.

---

## Diagnostics

To troubleshoot customization issues:

1. Select **Configure Chat** (gear icon) → **Diagnostics** in the Chat view.
2. Review all loaded custom agents, prompt files, instruction files, and skills.
3. Check for syntax errors, invalid configurations, or loading issues.
4. See [Troubleshooting AI in VS Code](https://code.visualstudio.com/docs/copilot/troubleshooting)
   for more details.
