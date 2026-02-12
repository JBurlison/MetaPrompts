# Agent Skill Files (`SKILL.md`)

> **Sources:**
> - [Agent Skills Specification (agentskills.io)](https://agentskills.io/specification)
> - [VS Code Agent Skills Documentation](https://code.visualstudio.com/docs/copilot/customization/agent-skills)

Agent Skills are directory-based capabilities that provide the AI with
specialized workflows, scripts, and resources. Skills are an
[open standard](https://agentskills.io/) that works across multiple AI agents
including VS Code, GitHub Copilot CLI, and GitHub Copilot coding agent.

---

## Directory Structure

A skill is a **directory** containing at minimum a `SKILL.md` file:

```
skill-name/
├── SKILL.md           # Required — skill definition and instructions
├── scripts/           # Optional — executable code
├── references/        # Optional — additional documentation
└── assets/            # Optional — static resources (templates, images, data)
```

> **Key principle:** Splitting content across subdirectories keeps the main
> `SKILL.md` lightweight and enables progressive disclosure. Avoid dumping
> everything into `SKILL.md` — use `references/`, `scripts/`, and `assets/`
> for detailed content.

---

## File Location

| Scope | Location |
|-------|----------|
| Workspace (project) | `<provider>/skills/<skill-name>/SKILL.md` |
| Personal (user profile) | `~/.copilot/skills/`, `~/.claude/skills/`, `~/.agents/skills/` |

Additional locations can be configured with the `chat.agentSkillsLocations`
setting.

> **Provider note:** `<provider>` is your provider's base folder (`.github/`,
> `.claude/`, `.codex/`, `.config/opencode/`).

---

## SKILL.md Format

The `SKILL.md` file must contain YAML frontmatter followed by Markdown content. This should ideally be under 5000 tokens in total (frontmatter + body) to ensure efficient loading. Consider moving detailed instructions and resources to `references/` files if you exceed this.

### Minimal Example

```markdown
---
name: skill-name
description: A description of what this skill does and when to use it.
---

# Skill Instructions

Your detailed instructions, guidelines, and examples go here...
```

### Full Example with Optional Fields

```markdown
---
name: pdf-processing
description: Extracts text and tables from PDF files, fills PDF forms, and merges multiple PDFs. Use when working with PDF documents or when the user mentions PDFs, forms, or document extraction.
license: Apache-2.0
compatibility: Requires Python 3.9+ and the PyPDF2 package
metadata:
  author: example-org
  version: "1.0"
allowed-tools: Bash(python:*) Read
---

# PDF Processing

Step-by-step instructions for working with PDFs...
```

---

## Frontmatter Attributes

| Attribute | Required | Constraints | Description |
|-----------|----------|-------------|-------------|
| `name` | **Yes** | 1–64 chars, lowercase `a-z`, `0-9`, `-` only | Unique identifier for the skill. Must match the parent directory name exactly. |
| `description` | **Yes** | 1–1024 chars | Describes what the skill does **and when to use it**. Should include trigger keywords that help agents identify relevant tasks. |
| `license` | No | — | License name or reference to a bundled license file (e.g., `Apache-2.0`, `Proprietary. LICENSE.txt has complete terms`). |
| `compatibility` | No | 1–500 chars if provided | Environment requirements: intended product, required system packages, network access needs, etc. Most skills do not need this. |
| `metadata` | No | key-value map (strings) | Arbitrary key-value pairs for additional metadata. Recommend making key names reasonably unique to avoid conflicts. |
| `allowed-tools` | No | space-delimited list | Pre-approved tools the skill may use. **Experimental** — support varies between agent implementations. |

---

## Name Validation Rules

The `name` field has strict validation:

| Rule | Valid | Invalid |
|------|-------|---------|
| Lowercase only | `pdf-processing` | `PDF-Processing` |
| No leading/trailing hyphens | `my-skill` | `-my-skill`, `my-skill-` |
| No consecutive hyphens | `my-skill` | `my--skill` |
| Alphanumeric + hyphens only | `data-analysis` | `data_analysis`, `data.analysis` |
| 1–64 characters | `a` through 64 chars | empty or 65+ chars |
| **Must match directory name** | `skills/my-skill/` → `name: my-skill` | mismatch between dir and name |

---

## Description Best Practices

The `description` field is critical — it determines when agents activate the
skill.

**Good description:**
```yaml
description: Extracts text and tables from PDF files, fills PDF forms, and merges multiple PDFs. Use when working with PDF documents or when the user mentions PDFs, forms, or document extraction.
```

**Poor description:**
```yaml
description: Helps with PDFs.
```

Include:
- **What** the skill does (capabilities)
- **When** to use it (trigger conditions/keywords)
- Specific action verbs and domain terms

---

## Body Content

The Markdown body after the frontmatter contains the skill instructions. There
are no format restrictions — write whatever helps agents perform the task
effectively.

### Recommended Sections

- **Step-by-step instructions** for the primary workflow
- **Examples** of inputs and outputs
- **Common edge cases** and how to handle them
- **File references** pointing to scripts, templates, or detailed docs

### Size Guidelines

- Keep the main `SKILL.md` body **under 500 lines**
- Target **< 5000 tokens** for the body content
- Move detailed reference material to `references/` files
- Move executable code to `scripts/`
- Move templates and data to `assets/`

---

## Optional Directories

### `scripts/`

Contains executable code that agents can run. Scripts should:

- Be self-contained or clearly document dependencies
- Include helpful error messages
- Handle edge cases gracefully

Supported languages depend on the agent implementation. Common options include
Python, Bash, and JavaScript.

### `references/`

Contains additional documentation that agents can read on demand:

- `REFERENCE.md` — Detailed technical reference
- `FORMS.md` — Form templates or structured data formats
- Domain-specific files (`finance.md`, `legal.md`, etc.)

Keep individual reference files focused. Agents load these on demand, so
smaller files mean less context usage.

### `assets/`

Contains static resources:

- Templates (document templates, configuration templates)
- Images (diagrams, examples)
- Data files (lookup tables, schemas)

---

## Progressive Disclosure

Skills use a three-level loading system for efficient context usage:

| Level | Content | When Loaded | Size Target |
|-------|---------|-------------|-------------|
| **1. Metadata** | `name` and `description` from frontmatter | Always (at startup) | ~100 tokens |
| **2. Instructions** | Full `SKILL.md` body | When skill is activated (description matches task) | < 5000 tokens |
| **3. Resources** | Files in `scripts/`, `references/`, `assets/` | On demand (when agent references them) | As needed |

This architecture means you can install many skills without consuming context.
Only relevant content loads for each task.

---

## File References

When referencing files in your skill, use relative paths from the skill root:

```markdown
See [the reference guide](references/REFERENCE.md) for details.

Run the extraction script:
scripts/extract.py
```

Keep file references **one level deep** from `SKILL.md`. Avoid deeply nested
reference chains.

---

## Validation

Use the [skills-ref](https://github.com/agentskills/agentskills/tree/main/skills-ref)
reference library to validate your skills:

```bash
skills-ref validate ./my-skill
```

This checks that your `SKILL.md` frontmatter is valid and follows all naming
conventions.

---

## Example: Web Application Testing Skill

```
webapp-testing/
├── SKILL.md
├── scripts/
│   └── run-tests.sh
├── references/
│   └── test-patterns.md
└── assets/
    └── test-template.js
```

**SKILL.md:**
```markdown
---
name: webapp-testing
description: Runs and debugs web application tests using Jest and Playwright. Use when testing web apps, writing test cases, or debugging test failures.
---

# Web Application Testing

## Running Tests

1. Identify the test framework in use (Jest, Playwright, or both)
2. Run the appropriate test command
3. Analyze failures and suggest fixes

## Test Patterns

See [test patterns reference](references/test-patterns.md) for common patterns.

## Writing New Tests

Use the [test template](assets/test-template.js) as a starting point.
```

---

## Skills vs Custom Instructions

| Feature | Agent Skills | Custom Instructions |
|---------|-------------|-------------------|
| Purpose | Specialized capabilities and workflows | Coding standards and guidelines |
| Portability | Open standard — works across VS Code, CLI, coding agent | VS Code and GitHub.com only |
| Content | Instructions, scripts, examples, resources | Instructions only |
| Scope | Task-specific, loaded on-demand | Always applied or via glob patterns |
| Standard | [agentskills.io](https://agentskills.io/) | VS Code-specific |

---

## Community Resources

- [Agent Skills Specification](https://agentskills.io/specification)
- [Awesome Copilot (community skills)](https://github.com/github/awesome-copilot)
- [Anthropic Reference Skills](https://github.com/anthropics/skills)
- [skills-ref Validation Tool](https://github.com/agentskills/agentskills/tree/main/skills-ref)
