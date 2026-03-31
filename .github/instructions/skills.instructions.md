---
applyTo: "skills/**"
---

# Instructions for Working with Skill Files

When creating or modifying files in the `skills/` directory, follow these conventions.

## Skill Structure

Each skill lives in its own subdirectory under `skills/` and must contain a `SKILL.md` file:

```
skills/
  my-skill/
    SKILL.md          # Required: skill definition
    supporting.md     # Optional: additional reference material
```

## SKILL.md Frontmatter

Every `SKILL.md` must begin with YAML frontmatter containing at minimum:

```markdown
---
name: skill-name
description: Use when [condition] - [what it does]
---
```

- `name` — kebab-case, matches the directory name.
- `description` — a clear trigger condition followed by what the skill does. This is what agents use to decide when to activate the skill automatically.

## Skill Content Guidelines

- Skills must be **self-contained** — a skill should be understandable without reading other skills.
- Write skills in **natural language Markdown**. Do not use code-specific syntax for workflow instructions.
- Each skill should have a clear **scope** — it does one thing. If a skill is doing multiple unrelated things, split it.
- Include a **checklist** or **phase structure** so the skill can be followed step-by-step.
- Include **anti-patterns** — explicitly list what NOT to do. This prevents rationalization.
- End with a **verification checklist** or success criteria.

## Supporting Files

Skills may include supporting `.md` files for reference material (examples, technique details, edge cases). Reference them from `SKILL.md` by relative path.

## Writing New Skills

Follow the pattern in `skills/writing-skills/SKILL.md` when creating new skills. Key points:

1. Start with the use case — when should this skill trigger?
2. Define the process as explicit phases or steps.
3. Include examples of correct and incorrect behavior where helpful.
4. Add anti-patterns and common rationalizations to avoid.
5. End with a verification checklist.

## Modifying Existing Skills

- Preserve the existing frontmatter structure.
- Keep changes focused — do not combine multiple changes in one edit.
- If a skill references other skills by name, keep those references current.
- Test that the skill still reads clearly as a standalone document after changes.
