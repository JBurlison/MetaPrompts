# MetaPrompts

This repo contains GitHub Copilot customization files meant to be copied into your own repo, centered on the meta agent `ai-builder`.

## Use the meta agent in VS Code

1. Copy the .github folder into your repo.
2. Open your repo in VS Code.
3. Open the Copilot Chat view.
4. Select the agent `ai-builder` from the agent picker, or type @ai-builder in the chat.
5. Ask for help creating or maintaining Copilot customizations.

Example requests:
- Create a new agent for database schema design.
- Validate my agent and skill files.
- Generate documentation for the current agents.

## Meta agent capabilities

The `ai-builder` agent can:

- Design and create agents, skills, prompts, and instructions.
- Recommend the right customization type for a request (agent vs prompt vs instructions vs workflow).
- Build multi-agent workflows with handoffs and review points.
- Validate and troubleshoot customization files for format issues.
- Analyze overlaps and redundancies across agents, skills, prompts, and instructions.
- Generate documentation and usage guides for your customizations.

## What to copy

- [​.github/agents/ai-builder.md](.github/agents/ai-builder.md) — the meta agent definition
- [​.github/skills](.github/skills) — reusable procedures referenced by agents
- [​.github/prompts](.github/prompts) — reusable prompt templates
- [​.github/instructions](.github/instructions) — auto-applied instructions by file pattern

## Notes

- Keep reusable procedures in skills and reference them from agents.
- Add project-wide guidance in instructions so it applies automatically.
- Commit changes to share the configuration across the team.
