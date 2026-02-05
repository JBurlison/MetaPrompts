# MetaPrompts

This repo contains AI coding assistant customization files meant to be copied into your own repo, centered on the meta agent `ai-builder`. Works with GitHub Copilot, Claude Code, Codex, OpenCode, and other providers using the same folder structure.

## Provider Folder Structure

These customization files work across multiple AI coding assistant providers. Copy to the appropriate folder for your environment:

| Provider | Base Folder |
|----------|-------------|
| GitHub Copilot | `.github/` |
| Claude Code | `.claude/` |
| Codex | `.codex/` |
| OpenCode | `.config/opencode/` |

The internal structure (`agents/`, `skills/`, `prompts/`, `instructions/`) is the same across all providers.

## Use the meta agent in VS Code

1. Copy the `.github` folder (or rename to your provider's folder) into your repo.
2. Open your repo in VS Code.
3. Open the Copilot Chat view.
4. Select the agent `ai-builder` from the agent picker, or type @ai-builder in the chat.
5. Ask for help creating or maintaining AI assistant customizations.

Example requests:
- Create a new agent for database schema design.
- Validate my agent and skill files.
- Generate documentation for the current agents.
- Help me understand what agents, prompts, and instructions are.

## Meta agent capabilities

The `ai-builder` agent can:

- Design and create agents, skills, prompts, and instructions.
- Recommend the right customization type for a request (agent vs prompt vs instructions vs workflow).
- Build multi-agent workflows with handoffs and review points (following Requirements → Due Diligence → Plan → Implement → Review pattern).
- Create sub-agents with proper naming (`*.subagent.agent.md`) and `user-invokable: false`.
- Explain the framework to users unfamiliar with agents, prompts, skills, and instructions.
- Validate and troubleshoot customization files for format issues.
- Analyze overlaps and redundancies across agents, skills, prompts, and instructions.
- Generate documentation and usage guides for your customizations.

## What to copy

Replace `<provider>` with your provider's folder (`.github`, `.claude`, `.codex`, `.config/opencode`):

- `<provider>/agents/ai-builder.md` — the meta agent definition
- `<provider>/skills/` — reusable procedures referenced by agents
- `<provider>/prompts/` — reusable prompt templates
- `<provider>/instructions/` — auto-applied instructions by file pattern

## Folder structure

```
<provider>/                          # .github/, .claude/, .codex/, etc.
├── agents/
│   ├── my-agent.agent.md            # User-facing agent
│   └── helper.subagent.agent.md     # Sub-agent (workflow component)
├── skills/
│   └── skill-name/
│       └── SKILL.md                 # Skill definition
├── prompts/
│   └── my-prompt.prompt.md          # Prompt template
└── instructions/
    └── coding.instructions.md       # Auto-applied instructions
```

## Notes

- Keep reusable procedures in skills and reference them from agents.
- Add project-wide guidance in instructions so it applies automatically.
- Use `.subagent.agent.md` naming and `user-invokable: false` for workflow sub-agents.
- Commit changes to share the configuration across the team.
