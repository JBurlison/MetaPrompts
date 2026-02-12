# Prompt Files (`.prompt.md`)

> **Source:** [VS Code Prompt Files Documentation](https://code.visualstudio.com/docs/copilot/customization/prompt-files)

Prompt files (also known as slash commands) let you encode common tasks as
standalone Markdown files that you invoke directly in chat. Each prompt file
includes task-specific context, variable placeholders, and guidelines for how
the task should be performed.

Unlike custom instructions that are applied automatically, prompt files are
invoked manually by typing `/` followed by the prompt name.

---

## File Location

| Scope | Location |
|-------|----------|
| Workspace | `<provider>/prompts/*.prompt.md` |
| User profile | `prompts` folder of the current VS Code profile |

Additional locations can be configured with the `chat.promptFilesLocations`
setting.

> **Provider note:** `<provider>` is your provider's base folder (`.github/`,
> `.claude/`, `.codex/`, `.config/opencode/`).

---

## File Structure

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

---

## Frontmatter Attributes

| Attribute | Required | Type | Default | Description |
|-----------|----------|------|---------|-------------|
| `name` | No | string | filename | Prompt name used after `/` in chat. If not specified, the filename is used. |
| `description` | No | string | — | Short description of the prompt. |
| `argument-hint` | No | string | — | Hint text shown in the chat input field to guide users on how to interact with the prompt. |
| `agent` | No | string | current agent | Agent to use for running the prompt: `ask`, `edit`, `agent`, `plan`, or the name of a custom agent. If `tools` are specified and no agent is set, defaults to `agent`. |
| `model` | No | string | selected model | Language model to use when running the prompt. If not specified, uses the currently selected model. |
| `tools` | No | string[] | — | List of tool or tool set names available for this prompt. Can include built-in tools, tool sets, MCP tools, or extension-contributed tools. Use `<server>/*` for all MCP server tools. |

> If a specified tool is not available when running the prompt, it is silently
> ignored.

---

## Body Content

The body contains the prompt text in Markdown format. It supports:

- **Markdown formatting:** Full Markdown including headers, lists, code blocks.
- **File references:** Link to workspace files with relative Markdown links
  (paths are relative to the prompt file location).
- **Tool references:** Reference tools with `#tool:<tool-name>` syntax
  (e.g., `#tool:githubRepo`).
- **Variables:** Dynamic placeholders using `${variableName}` syntax.

---

## Variables

Prompt files support several categories of variables:

### Workspace Variables

| Variable | Description |
|----------|-------------|
| `${workspaceFolder}` | Full path to the workspace root folder |
| `${workspaceFolderBasename}` | Name of the workspace root folder |

### Selection Variables

| Variable | Description |
|----------|-------------|
| `${selection}` | Currently selected text in the editor |
| `${selectedText}` | Same as `${selection}` |

### File Context Variables

| Variable | Description |
|----------|-------------|
| `${file}` | Full path to the currently open file |
| `${fileBasename}` | Filename with extension (e.g., `index.ts`) |
| `${fileDirname}` | Directory path of the current file |
| `${fileBasenameNoExtension}` | Filename without extension (e.g., `index`) |

### Input Variables

| Variable | Description |
|----------|-------------|
| `${input:varName}` | Prompts user for a value with label `varName` |
| `${input:varName:placeholder}` | Same as above, with placeholder hint text |

---

## Tool List Priority

When both a prompt file and a custom agent specify tools, the priority is:

1. Tools specified in the prompt file (highest)
2. Tools from the referenced custom agent in the prompt file
3. Default tools for the selected agent (lowest)

---

## Using Prompt Files

There are multiple ways to invoke a prompt file:

1. **Slash command:** Type `/` followed by the prompt name in chat
   (e.g., `/create-react-form`). You can append extra input after the command.
2. **Command Palette:** Run `Chat: Run Prompt` and select from the list.
3. **Editor play button:** Open the `.prompt.md` file in the editor and press
   the play button. Useful for testing and iterating.

Use the `chat.promptFilesRecommendations` setting to show prompts as
recommended actions when starting a new chat session.

---

## Example: React Form Component

```markdown
---
name: create-react-form
description: Generate a React form component with validation
agent: agent
tools: ['editFiles']
---

# Create React Form Component

Generate a React form component named ${input:formName:MyForm} with the
following requirements:

- Use TypeScript
- Include form validation
- Follow project conventions in [coding standards](../instructions/react.instructions.md)

## Form Fields

${input:fields:Describe the form fields needed}
```

---

## Example: Security Review

```markdown
---
name: security-review
description: Perform a security review of a REST API endpoint
agent: ask
tools: ['search', 'usages']
---

# Security Review

Review the selected code for security vulnerabilities:

${selection}

## Check For

- SQL injection and NoSQL injection
- Authentication and authorization bypass
- Input validation issues
- Sensitive data exposure
- Rate limiting concerns

## Output Format

Provide findings organized by severity: Critical, High, Medium, Low.
```

---

## Tips for Effective Prompts

- Clearly describe what the prompt should accomplish and what output format is
  expected.
- Provide examples of expected input and output to guide the AI.
- Use Markdown links to reference custom instructions rather than duplicating
  guidelines in each prompt.
- Use built-in variables like `${selection}` and input variables to make prompts
  flexible.
- Use the editor play button to quickly test and iterate on prompts.
- Store workspace prompt files in version control to share with team members.

---

## Syncing Across Devices

User prompt files can be synced across devices using VS Code Settings Sync:

1. Enable [Settings Sync](https://code.visualstudio.com/docs/configure/settings-sync).
2. Run **Settings Sync: Configure** from the Command Palette.
3. Select **Prompts and Instructions** from the sync list.

---

## Diagnostics

If a prompt file isn't working:

1. Use the chat customization diagnostics view: right-click in Chat →
   **Diagnostics**.
2. Check that the file is in a recognized location
   (`chat.promptFilesLocations`).
3. Verify the YAML frontmatter syntax is valid.
