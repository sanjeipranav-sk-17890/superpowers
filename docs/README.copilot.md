# Superpowers for GitHub Copilot

Complete guide for using Superpowers with GitHub Copilot across VS Code, Copilot CLI, and github.com.

## How It Works

GitHub Copilot picks up custom instructions from specific files in the repository. Superpowers uses three mechanisms:

| File | Scope | Used By |
|---|---|---|
| `.github/copilot-instructions.md` | All Copilot interactions in the repo | VS Code, CLI, github.com |
| `AGENTS.md` | Coding agent and CLI sessions | Copilot coding agent, Copilot CLI |
| `.github/instructions/*.instructions.md` | Path-specific instructions (via `applyTo` frontmatter) | VS Code Copilot Chat |

When you open this repository in VS Code or work in it with Copilot CLI, these files are automatically loaded into context. No installation steps required.

## Quick Start

### For Repositories That Already Include Superpowers

If the repository already contains the Superpowers files (`.github/copilot-instructions.md`, `AGENTS.md`, `.github/instructions/`), Copilot will automatically use them. Nothing else is needed.

Verify it is working: start a Copilot Chat session and ask for help planning a feature. Copilot should respond with clarifying questions instead of jumping straight to code.

### For New Repositories

Copy the following files from the Superpowers repo into your project:

```bash
# From the Superpowers repo root, copy to your project:
.github/copilot-instructions.md
AGENTS.md
.github/instructions/skills.instructions.md
.github/instructions/testing.instructions.md
```

Or clone and copy:

```bash
git clone https://github.com/obra/superpowers.git /tmp/superpowers
cp /tmp/superpowers/.github/copilot-instructions.md .github/
cp /tmp/superpowers/AGENTS.md .
mkdir -p .github/instructions
cp /tmp/superpowers/.github/instructions/*.instructions.md .github/instructions/
```

Commit the files to your repository. Copilot picks them up automatically.

---

## Platform-Specific Setup

### Copilot in VS Code

Custom instructions are loaded automatically when you open a repository that contains them.

**Requirements:**
- VS Code with the GitHub Copilot extension installed
- "Enable custom instructions" must be enabled in settings (it is on by default)

To verify the setting: open VS Code settings and search for `github.copilot.chat.useProjectTemplates`. It should be enabled.

**How to use:**
1. Open the repository in VS Code.
2. Start a Copilot Chat session (`Ctrl+Shift+I` / `Cmd+Shift+I`).
3. Ask for help with a task. Copilot will follow the Superpowers workflow automatically.

Path-specific instructions in `.github/instructions/` are applied automatically when you are working on files that match their `applyTo` glob patterns. For example, when editing test files, the TDD instructions are active.

### Copilot CLI

Copilot CLI reads instructions from `.github/copilot-instructions.md` and `AGENTS.md` automatically when you run `copilot` commands inside the repository directory.

```bash
# Navigate to your repo directory first
cd /path/to/your/project

# Then use Copilot CLI — instructions are automatically in context
gh copilot suggest "help me plan a new feature"
gh copilot explain "what does this function do"
```

You can also point Copilot CLI at a custom instructions directory using the `COPILOT_CUSTOM_INSTRUCTIONS_DIRS` environment variable:

```bash
export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="/path/to/your/project/.github/instructions"
```

### Copilot Coding Agent on github.com

The Copilot coding agent (available on github.com for issues and pull requests) reads `AGENTS.md` and `.github/copilot-instructions.md` automatically when working on the repository.

When you assign an issue to the Copilot coding agent or ask it to work on a PR, it will follow the Superpowers workflow: brainstorm the approach, plan before implementing, use TDD, and review before finishing.

---

## What Is Supported

Superpowers was originally built around Claude Code's tool invocation system (subagent dispatch, parallel agents, `TodoWrite` checklists). Not all of those features translate to Copilot. Here is what works:

| Feature | Copilot Support | Notes |
|---|---|---|
| Brainstorming workflow | ✅ Full | Via `.github/copilot-instructions.md` |
| TDD enforcement | ✅ Full | Via `.github/copilot-instructions.md` |
| Systematic debugging | ✅ Full | Via `.github/copilot-instructions.md` |
| Implementation planning | ✅ Full | Via `.github/copilot-instructions.md` |
| Code review | ✅ Full | Via `AGENTS.md` |
| Git worktrees | ⚠️ Partial | Instructions only — no auto-setup |
| Skill auto-invocation | ⚠️ Adapted | Instructions are always in context; no explicit invocation needed |
| Subagent dispatching | ❌ Not supported | Copilot does not support spawning sub-agents |
| Visual companion | ❌ Not supported | Browser-based feature; Claude Code specific |
| Parallel agents | ❌ Not supported | Copilot does not support parallel agent dispatch |

### What "Adapted" Means for Skill Auto-Invocation

In Claude Code, skills are invoked explicitly with a `Skill` tool call that loads the relevant SKILL.md at runtime. In Copilot, the workflow instructions are always present in context — Copilot does not need to "invoke" a skill because the instructions are already loaded.

The practical effect is the same: Copilot follows the brainstorm-plan-TDD-review workflow for all development tasks. The mechanism is different (always-on instructions vs. on-demand tool invocation), but the behavior is equivalent for supported features.

---

## Customizing the Instructions

The instruction files are plain Markdown. You can customize them for your project:

### Adding Project-Specific Instructions

Append project-specific conventions to `.github/copilot-instructions.md`:

```markdown
## Project-Specific Conventions

- Use Python 3.11+ features
- All database queries must use parameterized statements
- API endpoints follow the `/api/v1/resource` pattern
```

### Adding Path-Specific Instructions

Create new files in `.github/instructions/` with an `applyTo` frontmatter glob:

```markdown
---
applyTo: "src/api/**"
---

# API Development Instructions

All API endpoints must:
- Include input validation
- Return consistent error response format
- Be documented with OpenAPI annotations
```

### Adjusting Workflow Strictness

If the default workflow is too strict for your use case, you can soften specific gates in `.github/copilot-instructions.md`. For example, to make TDD optional for configuration files, add a note to the TDD section. However, be aware that removing gates tends to reduce workflow discipline over time.

---

## Troubleshooting

### Instructions Not Being Picked Up

**VS Code:**
1. Check that the file exists at `.github/copilot-instructions.md` (not in a subdirectory or with a different name).
2. Verify the VS Code Copilot extension is up to date.
3. Check that custom instructions are enabled: VS Code settings → search `github.copilot.chat.useProjectTemplates` → must be enabled.
4. Restart VS Code after adding instruction files.
5. In Copilot Chat, type `/help` — if instructions are loaded, you may see a reference to custom instructions.

**Copilot CLI:**
1. Make sure you are running `gh copilot` from within the repository directory.
2. Verify the file exists: `ls .github/copilot-instructions.md`
3. Check the CLI version: `gh copilot --version` — update if outdated.

**Coding Agent on github.com:**
1. Verify `AGENTS.md` exists in the repository root (not in `.github/`).
2. Verify `.github/copilot-instructions.md` exists.
3. The coding agent must be enabled for the repository in GitHub settings.

### Copilot Is Not Following the Workflow

If Copilot jumps straight to writing code instead of brainstorming:

1. Check that the instruction files were committed and pushed (not just local changes).
2. Start a fresh chat session — existing sessions may not pick up newly added files.
3. Explicitly ask: "Please follow the Superpowers workflow for this task" to reinforce the instructions.
4. Check if you have conflicting instructions in other files (e.g., a `.cursorrules` or `CLAUDE.md` file that overrides behavior).

### Path-Specific Instructions Not Applying

1. Verify the `applyTo` frontmatter glob is correct. Use standard glob syntax: `**/*.test.ts` not `*.test.ts`.
2. Verify the file is in `.github/instructions/` and has the `.instructions.md` extension.
3. Path-specific instructions currently require VS Code with Copilot — they may not apply in CLI or coding agent contexts.

---

## How This Differs From Other Platforms

| Aspect | Claude Code | Codex | Copilot |
|---|---|---|---|
| Skill loading | `Skill` tool call at runtime | Scanned from `~/.agents/skills/` | Always-on via `.github/copilot-instructions.md` |
| Subagents | Yes — spawns fresh subagents per task | Yes (with config) | No |
| Visual companion | Yes | No | No |
| Configuration | Plugin install | Symlink + config | No setup needed |
| Scope | Per-session plugin | Per-user install | Per-repository files |

---

## Getting Help

- Report issues: https://github.com/obra/superpowers/issues
- Main documentation: https://github.com/obra/superpowers
- GitHub Copilot custom instructions docs: https://docs.github.com/en/copilot/how-tos/configure-custom-instructions/add-repository-instructions
- GitHub Copilot CLI customization: https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions
